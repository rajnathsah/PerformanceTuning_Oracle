# Partitioning

Method to break a very large table and/or its associated indexes into smaller and manageable pieces.
In another words partitioning allows tables, indexes, and index-organized tables to be subdivided into smaller pieces, 
enabling these database objects to be managed and accessed at a smaller level.

* When to Partition a Table??  
  * Tables greater than 2 GB should always be considered as candidates for partitioning.
  * Tables containing historical data, in which new data is added into the newest partition. A typical example is a historical table where only the current month's data is updatable and the other 11 months are read only.
  * When the contents of a table need to be distributed across different types of storage devices.
  
## Type of partitioning
1. Range partitioning
2. List partitioning
3. Hash partitioning
4. Interval partitioning (Added in 11gr2)

## Composite partitioning
1. Range-Range partitioning
2. Range hash partitioning
3. Range-list partitioning
4. List-range partitioning
5. List-hash partitioning
6. List-list partitioning

### Range partitioning  
Each partition has a "values less than" clause which specifies a non-inclusive upper bound of the partitions.
Any binary value of the partition equal to or higher than this literal are added to the next higher partition.
All partitions, except the first have and implicit lower bound specified by the "values less than" clause on the previous partition
MAXVALUE literal can be used to define the highest partition.

Example:  
```sql
CREATE TABLE emp (
   empno NUMBER(4), 
   ename VARCHAR2(30), 
   sal   NUMBER
) 
PARTITION BY RANGE(empno) (
  partition e1 values less than (1000), 
  partition e2 values less than (2000), 
  partition e3 values less than (MAXVALUE)
); 
```

### List Partitioning  
Enables to explicitly control how rows are map to partition. List partitioning is a partitioning technique where you specify a list of discrete values for the partitioning key in the description for each partition.  

Example:  
```sql
create table emp (
       emp#     number primary key,
       ename    varchar2(30),
       salary   number(8,2),
       deptno   number)
  partition by list (deptno) (  
       partition p10 values (10), 
       partition p20 values (20), 
       partition p30 values (30,40),
       partition p40 values (default));
```

### Hash partitioning  
If data to be partitioned does not fit in range and list partition then hash partition is the way to partition data. Hash partitioning is a partitioning technique where a hash key is used to distribute rows evenly across the different partitions (sub-tables). This is typically used where ranges aren't appropriate.  

Example:
```sql
create table emp (
   empno number(4), 
   ename varchar2(30), 
   sal   number
) 
partition by hash(empno) (
  partition e1, 
  partition e2, 
  partition e3,
  partition e4
);
```

### Interval partitioning
Interval partitioning is an extension to range partitioning in which, beyond a point in time, partitions are defined by an interval. Interval partitions are automatically created by the database when data is inserted into the partition.

Restrictions on interval partition:  
*  You can only specify one partitioning key column, and it must be of NUMBER or DATE type.
*  Interval partitioning is NOT supported for index-organized tables.
*  You can NOT create a domain index on an interval-partitioned table.  

Example:  
```sql
create table emp
    (sno number(6),
    last_name varchar2(30),
    salary number(6))
partition by range(salary)
    Interval  (5000)
(partition p1 values less than (5000),
partition p2 values less than (10000),
partition p3 values less than (15000),
partition p4 values less than (20000));
```

## Important points
1. How system partition status can be verified?  
   By querying the view user_tab_partitions

2. List the restrictions on creating Interval partition?  
   You can only specify one partitioning key column, and it must be of NUMBER or DATE type.
   Interval partitioning is NOT supported for index-organized tables.
   You can NOT create a domain index on an interval-partitioned table.

3. Which type of partition can be used to manage automatically creating new partitions as needed by data?  
   Interval partitioning   

4. Partition key must be always a physical column of a table?  
   prior to 11g partition key has to be a physical key but in 11g onwards one can use virtual columns as partition key.

5. List the steps to be followed when partioning the existing tables?  
    1. Create a partitioned interim table with same structure of the table to be partitioned.
    2. Check if redefinition is possible or not.
	```sql
	EXEC Dbms_Redefinition.Can_Redef_Table(USER, 'BIG_TABLE');
	```
    3. If no error then we can start redifinition process.
	```sql
	BEGIN
	  DBMS_REDEFINITION.start_redef_table(
	  	uname      => USER,        
	        orig_table => 'BIG_TABLE',
	        int_table  => 'BIG_TABLE2');
	END
	/
 	```
    4. Move the constraints, indexes, triggers if any to interim table using alternative names.
    5. Complete the redefinition process
	```sql
	BEGIN
  	  dbms_redefinition.finish_redef_table(
	  uname      => USER,        
	  orig_table => 'BIG_TABLE',
	  int_table  => 'BIG_TABLE2');
	END;
	/
 	```
      	This will change the name of interim table to original table.
    6. Raname the constraints, index and triggers name on the new original table.  
    7. Verify the table whether partitioning was successful or not
 	```sql
	SELECT PARTITIONED
	FROM USER_TABLES
	WHERE TABLE_NAME='TAB_NAME';
 	```
      	You can also view the list of partitioned got created for the table by querying.
	```sql
	select partition_name
	from user_tab_partitions
	where table_name ='TAB_NAME'
	```
6. When to use global and non-global partitioned index with example?  
    1. If the table partitioning column is a subset of the index keys, use local index.
    2. If the index is unique, use global index.
    3. If your priority is manageablity, use local index.
    4. If application is OLTP one and user needs quick time response, use global index.

7. List the partitioning improvement in 11g R2.  
    1. Interval partitioning  
       Extension to range partition. Defined by an interval, providing equi-width ranges.  
       With the exception of first partition all partitions are automatically created on demand when matching data arrives.  
       Interval partitioning includes Interval, Interval-Range, Interval-List, Interval-Hash
    2. REF Partitioning  
       Partitioning for a child table is inherited from the parent table through a primary key-foreign key relationship.
       The partitioning keys are not stored in actual columns in the child table.
    3. Virtual column based partitioning  
       Partitioned key is based on virtual columns (Virtual columns are not stored on disk and only exist as metadata)

8. List the reasons to use partitioning feature?  
   When database size is huge and indivisual table size is more than 2g then sql running on such table may perform very poorly.  
   Files recovery may take days  
   Maintenance - Rebuilding of indexes may take days  
   Queried with full table scans take hours to complete  
   Index range scans become inefficient  

9. The implementation of partition is based on data values or data types in the table?  
   Data values

10. Describes the difference between truncating a partition and dropping the partition?  
    Droping a partition also drops the corresponding local index partitions, any global non-partitioned index or partitioned index with refernce to the dropped rows are markes as unusable.Where as by truncating a partition we can get rid of the data stored in that partition but keeps the partition,It also makes corresponding local index partition un-usable.  
    
For more details, please refer [Partitioning in oracle](https://www.oracle.com/technetwork/database/options/partitioning/overview/index.html)
