### Some postgres sqls snippets

Reference used to understand postgres internals : https://blog.anayrat.info/en/2018/11/12/postgresql-and-heap-only-tuples-updates-part-1/

It would be good to use tmux and split your terminal in two panes [let's call them TERM1 and TERM2]. We will use TERM1 to execute psql commands, and use TERM2 to execute some unix commands and see them work side by side in the same screen.

#### I. Postgres storage basics
We will create a table 'test1' under 'public' schema of 'postgres' database.

##### 1. create the base setup
```
postgres=# select current_database();
postgres=# create table test1(id int, name text, year_of_birth int);
postgres=# insert into test1 values (1, 'ABC', 1900), (2, 'DEF', 1901), (3, 'GHI', 1902), (4, 'JKL', 1903);
postgres=# select * from test1;
postgres=# select ctid, xmin, xmax, cmin, cmax, * from test1;
```

##### 2.To find out the directory containing the data file for current database
```
postgres=# show data_directory;
postgres=# select datname, oid as database_oid from pg_database where datname='postgres';
postgres=# select oid as table_oid, relname, relfilenode from pg_catalog.pg_class where relname='test1';
OR
postgres=# select tableoid, * from test1;
```

##### 3. List down the datafile for this table
```
[TERM2]: ls -al {data_directory}/base/{database_oid}/{table_oid}
[TERM2]:pg_filedump {data_directory}/base/{database_oid}/{table_oid}
```
We will use more of pg_filedump later on.
ctid(0,1) means this tuple is located as item1 of block0

#### 2.MVCC in Postgres
1. Initial state of the data
```
postgres=# select ctid, xmin, xmax, cmin, cmax, * from test1; 
postgres=# select lp, t_data from heap_page_items(get_raw_page('test1',0));
```
2. Update a row, and then see the corresponding changes
```
postgres=# update test1 set year_of_birth=20001 where id=1;
postgres=# select ctid, xmin, xmax, cmin, cmax, * from test1; 
postgres=# select lp, t_data from heap_page_items(get_raw_page('test1',0));
postgres=# CHECKPOINT; -- to ensure that the block is written to the disk
[TERM2]: pg_filedump {data_directory}/base/{database_oid}/{table_oid}
```
Note that after the update the ctid of the updated row changes from (0,1) to (0,5). However both heap_page_items and pg_filedump show the old version [ctid (0,1)]of the updated row.
```
postges=# VACUUM;
postgres=# select lp, t_data from heap_page_items(get_raw_page('test1',0));
```
Now the old version that was kept around is actually deleted, and that space is made available for new entries.



