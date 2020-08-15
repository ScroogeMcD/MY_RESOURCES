### Some postgres sqls snippets

Reference used to understand postgres internals : https://blog.anayrat.info/en/2018/11/12/postgresql-and-heap-only-tuples-updates-part-1/

##### 1.To find out the directory containing the data file for current database
```
postgres=# show data_directory;
```

##### 2. List down the datafiles under the base directory of the above directory
The name of the datafiles is the object_id (oid) of the corresponding databases

##### 3. To find the object_id (oid) of a specific database
```
postgres=# select datname, oid from pg_database where datname='postgres';
```
