- [Some postgres sqls snippets](####%20Some%20postgres%20sqls%20snippets)
- [I. Postgres storage basics](####%20I.%20Postgres%20storage%20basics)
  * [1. create the base setup](#sub-heading)
  * [2.To find out the directory containing the data file for current database](#sub-heading)
  * [3. List down the datafile for this table](#sub-heading)
- [2.MVCC in Postgres](#heading)
- [Partitioning in Postgres](#partitioning-in-postgres)

#### Some postgres sqls snippets

Reference used to understand postgres internals : https://blog.anayrat.info/en/2018/11/12/postgresql-and-heap-only-tuples-updates-part-1/

It would be good to use tmux and split your terminal in two panes [let's call them TERM1 and TERM2]. We will use TERM1 to execute psql commands, and use TERM2 to execute some unix commands and see them work side by side in the same screen.

------------------------------------------------------------------------------------------------------------------------------------------------------------
#### I. Postgres storage basics
We will create a table 'test1' under 'public' schema of 'postgres' database.

##### 1. create the base setup
```sql
select current_database();

create table test1(id int, name text, year_of_birth int);

insert into test1 values (1, 'ABC', 1900), (2, 'DEF', 1901), (3, 'GHI', 1902), (4, 'JKL', 1903);

select * from test1;

select ctid, xmin, xmax, cmin, cmax, * from test1;
```

##### 2.To find out the directory containing the data file for current database
```sql
show data_directory;

select datname, oid as database_oid from pg_database where datname='postgres';

select oid as table_oid, relname, relfilenode from pg_catalog.pg_class where relname='test1';
OR
select tableoid, * from test1;
```

##### 3. List down the datafile for this table
```
[TERM2]: ls -al {data_directory}/base/{database_oid}/{table_oid}
[TERM2]:pg_filedump {data_directory}/base/{database_oid}/{table_oid}
```
We will use more of pg_filedump later on.
ctid(0,1) means this tuple is located as item1 of block0

------------------------------------------------------------------------------------------------------------------------------------------------------------
#### 2.MVCC in Postgres
1. **Some system columns**
 - **ctid** : Tuple identifier, a pair(block_number, tuple_index_within_block). It stores the physical location of the row version within its table.
 - **xmin** : The transaction id of the inserting transaction for this row version
 - **xmax** : The transaction id of the deleting transaction, or zero for an undeleted row version. This column can be non-zero in a visible row version, usually
          indicating that the deleting transaction has not completed yet, or that an attempted deletion was rolled back.
 - **cmin** : The command identifier (starting at 0) within the inserting transaction.
 - **cmax** : The command identifier within the deleting transaction, or zero.
 
2. **Initial state of the data**
```sql
select ctid, xmin, xmax, cmin, cmax, * from test1; 

select lp, t_data from heap_page_items(get_raw_page('test1',0));
```
3. **Update a row, and then see the corresponding changes**
```sql
update test1 set year_of_birth=20001 where id=1;

select ctid, xmin, xmax, cmin, cmax, * from test1; 

select lp, t_data from heap_page_items(get_raw_page('test1',0));

CHECKPOINT; -- to ensure that the block is written to the disk

[TERM2]: pg_filedump {data_directory}/base/{database_oid}/{table_oid}
```
Note that after the update the ctid of the updated row changes from (0,1) to (0,5). However both heap_page_items and pg_filedump show the old version [ctid (0,1)]of the updated row.
```sql
VACUUM;
select lp, t_data from heap_page_items(get_raw_page('test1',0));
```
Now the old version that was kept around is actually deleted, and that space is made available for new entries.

------------------------------------------------------------------------------------------------------------------------------------------------------------
4. **Serialization levels in SQL**   
<details>
 
 <summary> read uncommitted </summary>
</details>
<details>
 
 <summary> read committed </summary>
 
 |T1|T2|Comments|
 |--|--|--------|
 |```get X```| |finds X to be 2|
 | |```set X=3```| |
 |```get X```| |still finds X to be 2|
 | |```set Y=3```| |
 | |```COMMIT```| |
 |```get X```| |now finds X to be 3|
 
 It provides the following two guarantees:
 * *no dirty reads* : When reading from DB, you will only see data that has been committed. Transaction T1 - in the above example - first finds the value of X to be 2, and then finds the latest value 3 only after T2 has committed. Thus, within the same transaction it is possible for a transaction to find multiple values of an object [initially old value, and then new value], if read multiple times, but only after the updated value has been commited by other concurrent transactions. This can be implemented by keeping both the old and new values(or versions) around (MVCC - Multi Version Concurrency Control).
       
 * *no dirty writes* : When writing to DB, you will only overwrite data that has been committed. This can be implemented by taking a lock on each row.
</details>
<details>
 
 <summary> repeatable read - also called snapshot isolation </summary>
 
 In the example above for *read committed*, transaction T1 was able to read values of an object that keep getting updated by other transactions, as and when the other transactions commit. In snapshot isolation, the idea is that each transaction reads from a consistent snapshot of the database - i.e. the transaction only sees that state of the DB that was in committed state when this transaction started. Even if the data is subsequently changed and committed by other transactions, each transaction sees only the old data from that particular point in time.
</details>
<details>
 
 <summary> serializable </summary>
 
It guarantees that even though transactions may execute in parallel, the end result is the same as if they had executed one at a time - *serially* - without any concurrency. Three techniques that are mostly used to implement serializability in Databases are :    
  * literally executing transactions in serial order : execute only one transaction at a time, in serial order, on a single thread.
  * Two Phase Locking (2PL)
  * Optimistic concurrency control techniques such as Serializable Snapshot Isolation (SSI)
  
</details>

Postgres does not implement *read uncommitted*, and hence does not allow dirty reads.
Postgres defaults to *read committed*.

------------------------------------------------------------------------------------------------------------------------------------------------------------
5. **Transactions demo I**
<details>
 
**Question : How is the uncommitted change in one transaction T1, not visible to another transaction T2, even though the underlying data store is the same ?**
```
--[TERM1]
drop table if exists txn_demo;
CREATE TABLE txn_demo(id INT PRIMARY KEY, val INT NOT NULL);
INSERT INTO txn_demo values (1, 100),(2,200);
```
SHOW TRANSACTION ISOLATION LEVEL; --  if you want to check the current transaction isolation level

| TERMINAL-1| TERMINAL-2|Comments|
|-----------|-----------|--------|
|``` BEGIN TRANSACTION ISOLATION LEVEL READ COMMITTED;```|| though default is read committed, still explicitly setting it for clarity|
|``` SELECT xmin, xmax, * from txn_demo where id=1;```||xmin, xmax are like our tt_begin, tt_end columns that we have in our temporal tables, only these are implicit|
|``` select txid_current();```||Shows the current txn_id(say T1) which will be assigned to next transaction|
|``` update txn_demo set val=val+1 where id = 1;```|||
|``` SELECT xmin, xmax,ctid, * from txn_demo where id=1;```||xmin here would be T1|
||``` SELECT xmin, xmax, * from txn_demo where id=1;```|xmax here would be T1, but we would be seeing the older version as new update is yet to be committed.|
|``` commit;```| | |
| |``` SELECT xmin, xmax, * from txn_demo where id=1;```| Now I see the latest version, since the first transaction committed.|

</details>

------------------------------------------------------------------------------------------------------------------------------------------------------------
6. **Transactions demo II**
<details>
 
```
--[TERM1]
drop table if exists txn_demo;
CREATE TABLE txn_demo(id INT PRIMARY KEY, val INT NOT NULL);
INSERT INTO txn_demo values (1, 100),(2,200);
```

| TERMINAL-1| TERMINAL-2|Comments|
|-----------|-----------|--------|
|```select xmin, xmax, ctid, * from txn_demo;``` |||
||```select xmin, xmax, ctid, * from txn_demo;``` ||
|```BEGIN TRANSACTION ISOLATION LEVEL READ COMMITTED;```|||
||```BEGIN TRANSACTION ISOLATION LEVEL READ COMMITTED;```||
|```UPDATE txn_demo set val = val+1 where id=1;```|||
|```select xmin, xmax, ctid, * from txn_demo;```|||
||```UPDATE txn_demo set val = val+1 where id=1;```||
|```COMMIT;```|||
||```select xmin, xmax, ctid, * from txn_demo;```||
||```COMMIT;```||

</details>

------------------------------------------------------------------------------------------------------------------------------------------------------------
7. **Transactions demo III**
<details>
 
```
--[TERM1]
drop table if exists txn_demo;
CREATE TABLE txn_demo(id INT PRIMARY KEY, val INT NOT NULL);
INSERT INTO txn_demo values (1, 100),(2,200);
```

| TERMINAL-1| TERMINAL-2|Comments|
|-----------|-----------|--------|
|```BEGIN TRANSACTION ISOLATION LEVEL SERIALIZABLE;```|||
||```BEGIN TRANSACTION ISOLATION LEVEL SERIALIZABLE;```||
|```select txid_current();```|||
||```select txid_current();```||
|``` update txn_demo set val=val+1 where id = 1;```|||
|```select xmin, xmax, ctid, * from txn_demo where id = 1;```|||
||```select xmin, xmax, ctid, * from txn_demo where id = 1;```||
||``` update txn_demo set val=val+1 where id = 1;```|The query in terminal 2 is going to stall. What happens if we commit in Term1?|
|```COMMIT;```|||

</details>

#### Partitioning in Postgres
* First declare a master table (say ``trade_data``), partitioned by ``trade_date`` field.
```sql
create table trade_data(
	trade_date date not null,
	security int not null,
	settle_date date,
	qty int not null,
	price numeric(32,8)
)partition by range (trade_date)
```
* Then add partitions to the table, covering some partition range. Also, if a trade_date is not covered by any of the ranges, then we will need to declare a default partition, else an error will be thrown while inserting such records.
```sql
create table trade_date_2021
partition of trade_data
for values from ('2021-01-01') to ('2021-12-31')

create table trade_date_2022
partition of trade_data
for values from ('2022-01-01') to ('2022-12-31')

create table trade_date_default
partition of trade_data
DEFAULT
```
* We can then insert into the master table, and the record will get stored in the appropriate partition. Nothing will be stored in the master table.
```sql
insert into trade_data values ('2021-01-10',1234,'2021-01-12',10,10.56)
insert into trade_data values ('2019-01-10',1234,'2021-01-12',10,10.56)
```

* We can insert and query directly to/from the partition tables as well, but even direct insertion needs to honour the range constraint, else an error will be thrown.
