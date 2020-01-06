# Explain Plan

The EXPLAIN PLAN statement displays execution plans chosen by the Oracle optimizer for SELECT, UPDATE, INSERT and DELETE statements. 
A statement's execution plan is the sequence of operations Oracle performs to run the statement.

The row source tree is the core of the execution plan. It shows the following information:
* An ordering of the tables referenced by the statement
* An access method for each table mentioned in the statement
* A join method for tables affected by join operations in the statement
* Data operations like filter, sort, or aggregation

In addition to the row source tree, the plan table contains information about the following:
* Optimization, such as the cost and cardinality of each operation
* Partitioning, such as the set of accessed partitions
* Parallel execution, such as the distribution method of join inputs

The EXPLAIN PLAN results let you determine whether the optimizer selects a particular execution plan, such as, nested loops join. 
It also helps you to understand the optimizer decisions, such as why the optimizer chose a nested loops join instead of a hash join, and lets you understand the performance of a query.

#### How to read Explain Plan:
EXPLAIN PLAN statement displays execution plans chosen by the Oracle optimizer for SELECT, UPDATE, INSERT and DELETE statements. Statement's execution plan is the sequence of operations Oracle performs to execute the statement. The components of execution plans includes :  
* An ordering of the tables referenced by the statement  
* An access method for each table mentioned in the statement  
* A join method for tables affected by join operations in the statement  

EXPLAIN PLAN output shows how oracle executes SQL statements. In order to determine if you are looking at a good execution plan or not, you need to understand how the optimizer determined the plan in the first place. You should also be able to look at the execution plan and assess if the optimizer has made any mistake in its estimations or calculations, leading to a suboptimal plan. The components to assess are:

* Cardinality– Estimate of the number of rows coming out of each of the operations.
* Access method – The way in which the data is being accessed, via either a table scan or index access.
* Join method – The method (e.g. hash, sort-merge, etc.) used to join tables with each other.
* Join type – The type of join (e.g. outer, anti, semi, etc.).
* Join order – The order in which the tables are joined to each other.
* Partition pruning – Is it accessing only necessary partitions to answer the query?
* Parallel Execution – In case of parallel execution, is each operation in the plan being conducted in parallel? Is the right data redistribution method being used?  

#### Note :  
1. Oracle will always generate the explain plan either based on available statistics or dynamic sampling (Statistics generated at run time). For better plan always maintain latest statistic of objects in your database.  
2. Plan generated through explain plan command may not be the same plan which oracle will use at the time of query execution. Generated plan may vary with actual used plan. In cases where difference is seen, look at the actual explain plan.  




For more details refer Oracle guide on [Explain Plan](https://docs.oracle.com/cd/B19306_01/server.102/b14211/ex_plan.htm#i3305).

You can also refer my own document [Explain Plan and Statistics](https://github.com/rajnathsah/Oracle-Scripts-and-Notes/blob/master/Notes/Explain%20Plan%20and%20Statistics%20Help%20Document.doc).
