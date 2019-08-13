# Partitioning

Method to break a very large table and/or its associated indexes into smaller and manageable pieces.
In another words partitioning allows tables, indexes, and index-organized tables to be subdivided into smaller pieces, 
enabling these database objects to be managed and accessed at a smaller level.

* When to Partition a Table??  
  * Tables greater than 2 GB should always be considered as candidates for partitioning.
  * Tables containing historical data, in which new data is added into the newest partition. A typical example is a historical table where only the current month's data is updatable and the other 11 months are read only.
  * When the contents of a table need to be distributed across different types of storage devices.
