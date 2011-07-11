Brisk
==

Better Hadoop with Cassandra
--

T Jake Luciani (@tjake)

The Traditional Hadoop Stack
--

1. HDFS Components
1. Map Reduce Components
1. Other Tools
1. Based on vanilla Cassandra 0.8 and Hadoop 0.20.03

Many single points of failure

Many alien races try to take over the world but have single points of failure.
They should run C*.

Why would you do this?
--

1. So you don't have to run multiple stacks.
1. Because of Cassandra, supports cross-DC story, multi-master story
1. Natural replication of Cassandra

Brisk Stack
--

1. Embedded with Cassandra
  1. CassandraFS Layer
  1. Job/Task Tracker
  1. Hive MetaStore Layer
1. Also on the node
  1. Hadoop Tools

Benefits
--

* Easy to deploy and operate
* No SPoF
* Scale and change nodes with no downtime
* Cross-DC, multi-master clusters
* Allocate resources with OLAP vs OLTP
  * No ETL
  * Uses natural C* replication
  * Realtime access to transaction data instead of having to do a batch ETL
* C* 0.8, Hadoop 0.20.203


Cassandra Background:
--

* Static tables (e.g. Users)
* Large rows (like indexes, list of followers, etc)

CassandraFS
--

HDFS backed by Cassandra instead of a block device.

* Uses thrift :`-(
* Maintains `inode`, `sblocks` column families
* data is mmap'd so reads are stupid fast.
* blocks are compressed with Google Snappy
* `hadoop distcp hdfs:///mydata cfs:///mydata`
* Note that HDFS blocks need to be broken up to sub-blocks because you can't stream from Cassandra.

Hybrid Workflow
--

Uses "rack awareness" to split cluster between an analytics cluster and a
transactional cluster.

There's a BriskSnitch that makes this partitioning easy.


Hive Support
--

* Hive MetaStore in C*
  * Unified schema view from any node, with no external systems an no SPoF
  * Automatically maps C* column families to Hive tables
* Supports dynamic column families as well as static columns
* Runs right in C* so you don't need a seperate meta store process

You can use some special keys in a Hive query to transpose your wide rows into
thing rows.  Makes data joinable in Hive.

Pig Support too


Live Demo!
--

* Low-Latency Side (OLTP)
  * Live tick prices for NASDAQ stocks
  * Thousands of Portfolios
* Batch Analytics Side (OLAP)
  * Historical Stock Prices
  * Hitorical VAR (Value At Risk) calculation for each portfolio using Hive
  * VAR number is written back to C* from Hive

https://github.com/riptano/brisk
https://github.com/riptano/brisk/tree/master/demos/portfolio_manager

Q&A
--

* HCatalog
* Namenode is just a CF in C* so no SPoF
* What's going on with Predicate Pushdown
  * Iterate through a subset of rows, based on secondary index
