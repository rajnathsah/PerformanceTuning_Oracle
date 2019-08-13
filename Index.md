# Index

An index is a performance-tuning method of allowing faster retrieval of records. An index creates an entry for each value that appears in the indexed columns. By default, Oracle creates B-tree indexes.

### Types of indexe

1. B Tree
  General purpose indexes in oracle, they are default indexes in oracle.
    B tree index can have at most 32 columns. 
2. Bitmap
   Bitmap indexes are ideal in cases where low number of distict values. It is ideal for DSS systems where more read happens. 
   It can also have at most 32 columns. But it is used for less number of columns.
3. Hash
   Indexes created on hash cluster
4. Index-organized table
   An index orgnized table is a type of table that stores data in B Tree index structure that is logically sorted in primary key.
5. Reverse key
   reverse key index works only on exact match. It does not use range scan.
6. Function based key
   Indexes created on function based column
7. Partitioned (Local and Global)
   Breaking indexes into pieces. 
8. Bitmap join indexes
