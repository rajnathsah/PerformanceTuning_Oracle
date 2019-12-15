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

For more details refer Oracle guide on [Explain Plan](https://docs.oracle.com/cd/B19306_01/server.102/b14211/ex_plan.htm#i3305).

You can also refer my own document [Explain Plan and Statistics](https://github.com/rajnathsah/Oracle-Scripts-and-Notes/blob/master/Notes/Explain%20Plan%20and%20Statistics%20Help%20Document.doc).
