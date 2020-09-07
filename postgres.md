### Some postgres sqls snippets

Reference used to understand postgres internals : https://blog.anayrat.info/en/2018/11/12/postgresql-and-heap-only-tuples-updates-part-1/

It would be good to use tmux and split your terminal in two panes [let's call them TERM1 and TERM2]. We will use TERM1 to execute psql commands, and use TERM2 to execute some unix commands and see them work side by side in the same screen.

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

4. **Serialization levels in SQL**
- *read uncommitted*
- *read committed*
- *repeatable read*
- *serializable*

Postgres does not implement *read uncommitted*, and hence does not allow dirty reads.
Postgres defaults to *read committed*.

5. **Transactions demo**
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
|```sql BEGIN TRANSACTION ISOLATION LEVEL READ COMMITTED;```|| though default is read committed, still explicitly setting it for clarity|
|```sql SELECT xmin, xmax, * from txn_demo where id=1;```||xmin, xmax are like our tt_begin, tt_end columns that we have in our temporal tables, only these are implicit|
|```sql select txid_current();```||Shows the current txn_id(say T1) which will be assigned to next transaction|
|```sql update txn_demo set val=val+1 where id = 1;```|||
|```sql SELECT xmin, xmax,ctid, * from txn_demo where id=1;```||xmin here would be T1|
||```sql SELECT xmin, xmax, * from txn_demo where id=1;```|xmax here would be T1, but we would be seeing the older version as new update is yet to be committed.|
|```sql commit;```| | |
| |```sql SELECT xmin, xmax, * from txn_demo where id=1;```| Now I see the latest version, since the first transaction committed.|


