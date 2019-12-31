---
title: "Spark基础"
date: 2019-12-24T10:22:52+08:00
lastmod: 2019-12-24T10:22:52+08:00
draft: false
tags: ["spark"]
categories: ["大数据"]
---

# spark初探

[http://datastrophic.io/](http://datastrophic.io/core-concepts-architecture-and-internals-of-apache-spark/)

## 基本概念

- Job: A piece of code which reads some input from HDFS or local, performs some computation on the data and writes some output data.
- Stages: Jobs are divided into stages. Stages are classified as a Map or reduce stages (Its easier to understand if you have worked on Hadoop and want to correlate). Stages are divided based on com- putational boundaries, all computations (operators) cannot be Updated in a single Stage. It happens over many stages.
- Tasks: Each stage has some tasks, one task per partition. One task is executed on one partition of data on one executor (machine).
- DAG: DAG stands for Directed Acyclic Graph, in the present context its a DAG of operators.
- Executor: The process responsible for executing a task.
- Master: The machine on which the Driver program runs
- Slave: The machine on which the Executor program runs

## spark组件

![](http://datastrophic.io/content/images/2016/03/Spark-Cluster-Architecture--1-.png)

1. spark driver
   - separate process to execute user applications
   - creates SparkContext to schedule jobs execution and negotiate（协商） with cluster manager
2. Executors
   - run tasks scheduled by driver
   - store computation results in memory, on disk or off-heap
   - interact with storage systems
3. Cluster Manager
   - Mesos
   - YARN
   - Spark Standalone

Spark Driver contains more components responsible for translation of user code into actual jobs executed on cluster:

![](http://datastrophic.io/content/images/2016/03/Spark-Overview--1-.png)

- SparkContext
  - represents the connection to a Spark cluster, and can be used to create RDDs, accumulators and broadcast variables on that cluster
- DAGScheduler
  - computes a DAG of stages for each job and submits them to TaskScheduler
  - determines preferred locations for tasks (based on cache status or shuffle files locations) and finds minimum schedule to run the jobs
- TaskScheduler
  - responsible for sending tasks to the cluster, running them, retrying if there are failures, and mitigating stragglers
- SchedulerBackend
  - backend interface for scheduling systems that allows plugging in different implementations(Mesos, YARN, Standalone, local)
- BlockManager
  - provides interfaces for putting and retrieving blocks both locally and remotely into various stores (memory, disk, and off-heap)

## RDD

Spark is built around the concepts of **Resilient Distributed Datasets(弹性分布式数据集)** and **Direct Acyclic Graph** representing transformations and dependencies between them.

![](http://datastrophic.io/content/images/2016/03/Spark-Overview--1-.png)

Spark Application (often referred to as **Driver Program or Application Master**) at high level consists of SparkContext and user code which interacts with it creating RDDs and performing series of transformations to achieve final result. These transformations of RDDs are then translated into DAG and submitted to Scheduler to be executed on set of worker nodes.

**RDD could be thought as an immutable parallel data structure with failure recovery possibilities. **

### RDD Operations

- Transformations
  - apply user function to every element in a partition (or to the whole partition)
  - apply aggregation function to the whole dataset (groupBy, sortBy)
  - introduce dependencies between RDDs to form DAG
  - provide functionality for repartitioning (repartition, partitionBy)
- Actions
  - trigger job execution
  - used to materialize computation results
- Extra: persistence
  - explicitly store RDDs in memory, on disk or off-heap (cache, persist)
  - checkpointing for truncating RDD lineage

## Execution workflow recap

![](http://datastrophic.io/content/images/2016/03/Spark-Scheduling-Process.png)

user code containing RDD transformations forms Direct Acyclic Graph which is then split into stages of tasks by DAGScheduler. Stages combine tasks which don’t require shuffling/repartitioning if the data. Tasks run on workers and results then return to client.

## DAG

![](http://datastrophic.io/content/images/2016/03/Logical-view.png)

Here's a DAG for the code sample above. So basically any data processing workflow could be defined as reading the data source, applying set of transformations and materializing the result in different ways. Transformations create dependencies between RDDs and here we can see different types of them.

The dependencies are usually classified as "narrow" and "wide":

![](http://datastrophic.io/content/images/2016/03/Dependency-Types--1-.png)

- Narrow (pipelineable)
  - each partition of the parent RDD is used by at most one partition of the child RDD
  - allow for pipelined execution on one cluster node
  - failure recovery is more efficient as only lost parent partitions need to be recomputed
- Wide (shuffle)
  - multiple child partitions may depend on one parent partition
  - require data from all parent partitions to be available and to be shuffled across the nodes
  - if some partition is lost from all the ancestors a complete recomputation is needed

## Splitting DAG into Stages

Spark stages are created by breaking the RDD graph at **shuffle boundaries**.

![](http://datastrophic.io/content/images/2016/03/Making-Stages-from-DAG--2-.png)

- RDD operations with "narrow" dependencies, like map() and filter(), are pipelined together into one set of tasks in each stage operations with shuffle dependencies require multiple stages (one to write a set of map output files, and another to read those files after a barrier).
- In the end, every stage will have only shuffle dependencies on other stages, and may compute multiple operations inside it. The actual pipelining of these operations happens in the `RDD.compute()` functions of various RDDs

There are two types of tasks in Spark: `ShuffleMapTask` which partitions its input for shuffle and `ResultTask` which sends its output to the driver.The same applies to types of stages: `ShuffleMapStage` and `ResultStage` correspondingly.

## shuffle

During the shuffle `ShuffleMapTask` writes blocks to local drive, and then the task in the next stages fetches these blocks over the network.

- Shuffle Write
  - redistributes data among partitions and writes files to disk
  - each *hash shuffle* task creates one file per “reduce” task (total = MxR)
  - sort shuffle task creates one file with regions assigned to reducer
  - sort shuffle uses in-memory sorting with spillover to disk to get final result
- Shuffle Read
  - fetches the files and applies reduce() logic
  - if data ordering is needed then it is sorted on “reducer” side for any type of shuffle







