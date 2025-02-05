# SQL Server: Analyzing Query Performance for Developers

Introduction to analyzing query performance for developers.

## 1. Introduction

Developer's responsibilities:

- write code,
- fix code (write more code).

A developer's work doesn't end when the code is written. The code must be maintained and optimized.

### Why We Need to Understand Query Execution?

T-SQL is declarative.

We need to understand what decisions were made by SQL Server:

- every query has a query plan generated,
- every query has execution metrics.

### Course Objectives

> Explain what information can be captured about a query and its execution from SQL Server.

That includes:

- query execution metrics,
- using performance metrics,
- query text and plan.

> Provide an understanding of where to find information about query execution and what it means.

Methods to capture information about query execution and performance interpreting query metrics.

> Step through an execution plan and the information it provides.

That includes:

- how to read a query plan,
- how to find information in a plan,
- discuss what's not important in a plan.

> Understand the information included in a query plan.

Examine the most frequently-seen operators in a query plan.

Examine how changes to a plan can affect query performance.

Demonstrate additional, valuable information found within query plans.

## 2. Finding Information About Queries

### Introduction and Information About Queries

Information we can find about query execution:

- query text,
- query plan,
- execution statistics.

Information generated by query execution:

- can be viewed "live",
- "stored" information can be retrieved and reviewed.

Data from SSMS:

- I/O, CPU, duration,
- actual plan,
- estimated plan.

### Viewing "Live" Query Execution Data

Showed during demo.

### Finding Query Execution Data

Finding query execution data:

- SSMS,
- extended events,
- DMOs,
- Query Store,
- Performance Monitor.

### Query Information from Extended Events

Extended Events worth using:

- `sql_statement_completed`,
- `sp_statement_completed`,
- `rpc_completed`,
- `sql_batch_completed`.

Extended Events that should be avoided:

- `query_post_compilation_showplan`,
- `query_post_execution_showplan`,
- `query_pre_execution_showplan`.

### Query Execution Data in DMOs

DMOs worth using (detailed):

- `sys.dm_exec_sql_text`,
- `sys.dm_exec_query_plan`,
- `sys.dm_exec_cached_plans`,
- `sys.dm_exec_text_query_plan`

DMOs worth using (summary):

- `sys.dm_exec_query_stats`,
- `sys.dm_exec_function_stats`.

### Query Store

Query Store:

- captures query text,
- captures query plan,
- captures runtime statistics.

### Query Metrics From Performance Monitor

Performance Monitor:

- CPU,
- memory,
- physical disk,
- access methods,
- locks,
- SQL statistics.

### A Note About Estimated and Actual Plans

| Estimated                                                                | Actual                                                  |
| ------------------------------------------------------------------------ | ------------------------------------------------------- |
| Contains estimates only                                                  | Contains estimates and actual values                    |
| Estimates are based on values use for the _first_ execution of the query | Actuals based on input values used for _that_ execution |
| SSMS, XE/trace, plan cache                                               | SSMS, XE/trace                                          |
| -                                                                        | May be different than the estimated plan                |

## 3. Understanding Query Performance Metrics

Examine query resource use and execution duration in detail:

- source for data,
- how to use it for troubleshooting and comparing performance.

### Query Metrics of Interest

You might be interested in:

- duration,
- CPU,
- I/O,
- memory.

### SSMS, Extended Events, DMOs, and Query Store

SSMS:

- information for the execution of one query,
- client statistics have limitations,
- returning results to the UI incurs overhead,
- output must be saved.

Extended Events:

- data for an individual query or batch,
- only selected data is collected, based on the capture duration and predicates,
- data can be saved,
- aggregation must be done post-collection.

Dynamic Management Objects (DMOs):

- data is an aggregation from the time the plan entered the plan cache,
- data is lost when the plan leaves the cache or when the instance is restarted.

Query Store:

- SQL Server aggregates query execution data for a plan across user-defined time intervals.

### Viewing and Interpreting Query Execution Data

Showed during demo.

## 4. Reading Query Plans

### What is a Plan?

A plan is a set of instructions that SQL Server uses to execute a query:

- SQL Server Optimizer,
- logical operations transformed to physical operations,
- query plan.

### What's in a Plan and What's Not in a Plan

Cost _used_ to equate to elapsed time in seconds required to run on a specific Microsoft employee's machine.

"_Cost_" today in the context of query plans is a unit-less measure.

Cost is _always_ an estimate.

What is not in a plan?

- query duration,
- I/O information\*,
- locks,
- wait statistics,
- actual cost.

> Unless you capture the actual execution plan, the numbers you see in a plan are estimates.

### Reading Plans

Control flow starts at the root operator.

Data flow starts at the leaf level.

### How to Start Looking at a Query Plan

How do you find information in a plan?

- graphical plan,
- XML plan,
- Plan Explorer.

## 5. Operators in a Query Plan

Topics:

- identify frequently-seen plan operators and understand their function,
- review how altering a query can change the operator used,
- review how a change in operator can change query performance.

### What Do You Look for in a Plan?

What is important and what not so much?

### Scans and Seeks

Scans:

- `Table Scan`,
- `Clustered Index Scan`.

Seeks:

- `Clustered Index Seek`,
- `Nonclustered Index Seek`.

### Data Access Operators

Showed during demo.

### Nested Loop and Lookups

One operator:

- `Nested Loop`.

Uses each row from one input (outer data set) to find rows from a second input (inner data set) that meet the join predicate.

Seen with smaller data sets and lookups.

Watch for inflated I/O.

Lookups:

- `Key Lookup`,
- `RID Lookup`.

More info showed during demo.

### Merge Join and Sort

One operator:

- `Merge Join`.

Data from two ordered inputs joined:

- matching rows returned.

Ideally data is already ordered (e.g. index).

Worktables used when multiple values on both sides of the data set ("many-to-many").

Sort:

- `Sort`.

Orders rows.

Stop-and-go operation.

Requires memory.

Possible to spill to disk.

More info showed during demo.

### Hash Join

One operator:

- `Hash Join`.

Joins two unordered inputs:

- outputs the matching rows.

Builds a hash table based on key value for each row in the outer data set, then for each row of the inner data set, calculates the hash and probe the table.

Possible to spill to disk.

More info showed during demo.

### Compute Scalar

Compute scalar:

- `Compute Scalar`.

Evaluates an expression:

- produces a scalar value (e.g. functions).

The estimated cost can be low.

Plans do not always show objects accessed.

More info showed during demo.

## 6. Important Information in a Plan

Review additional, essential information in a query plan, including:

- row estimates/actuals,
- parallelism,
- predicates,
- warnings.

### Input Parameters

Input values for a query, used on initial compilation, can be found in the plan.

This can be extremely important when comparing an actual plan (with different values) to the plan in cache.

### Finding Input Parameters in a Plan

Showed during demo.

### Trace Flags in a Query Plan

Global and session trace flags are captured by execution plans in SQL Server 2014 SP2 and SQL Server 2016+.

Use IsCompileTime attribute to determine if flag present at compilation and execution.

More info [here](https://support.microsoft.com/enus/kb/3170115).

More info showed during demo.

### Cardinality Estimator Version and Issues

Cardinality estimator version:

- the cardinality estimator (CE) was significantly changed in SQL Server 2014,
- the new CE can be enabled in a variety of ways,
- to confirm CE used, check attribute value in the plan.

CE issues:

- check for differences between estimates and actual,
- can cause significantly more data to move through the plan than needed,
- validate statistics, CE version, table variables, table value functions to start.

### Examining CE Version and Estimates

Showed during demo.

### Execution Statistics for Operators

Operator execution statistics:

- available in SQL Server 2014 SP2 and SQL Server 2016+,
- includes CPU, duration and I/O information on a per-operator basis.

More info showed during demo.

### Parallelism in Plans

Parallelism in plans:

- if `MAXDOP` is not set to 1, the optimizer can generate a serial or parallel plan,
- not every plan can be parallelized,
- indicates a higher cost query - not necessarily good or bad.

Parallelism and operators:

- `Distribute Streams`,
- `Repartition Streams`,
- `Gather Streams`,
- operator running in parallel.

Parallel plans can have multiple zones of concurrent execution.

`MAXDOP` limits the maximum number of threads _per operator_.

`MAXDOP` does not limit the total threads for a plan.

More info [here](https://sqlperformance.com/2013/10/sql-plan/parallel-plans-branches-threads).

More info showed during demo.

### Seek and Residual Predicates

Seek predicate.

Residual predicate.

More info showed during demo.

### Generating Plan Warnings

Warnings:

- spill to tempdb (hash and sort),
- implicit conversion,
- no statistics,
- missing index,
- missing join predicate.

More info showed during demo.

## Summary

Now that you have completed this course, you should be able to analyze a query plan and understand the information it contains.
