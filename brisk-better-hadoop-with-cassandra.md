T Jake Luciani
==
DataStax
--

* Cassandra comitter
* Project lead for Brisk

What is Brisk?
==

* Hadoop on Cassandra
* Why would you do this?
  * So you don't have to run multiple stacks.
  * Because of Cassandra, supports cross-DC story, multi-master story
  * Natural replication of Cassandra
* Based on vanilla Cassandra 0.8 and Hadoop 0.20.03

Who do you do this?
==

* Combine two stacks. Remove the single points of failure. Put as many of the components in Cassandra as you can.

New components in Cassandra
--

* CassandraFS layer
* Jobs/tasks tracker
* Hive "MetaStore" layer (something for Hive)

More Hadoop components will be added to Brisk as time goes on.

CassandraFS
==

HDFS backed by Cassandra instead of a block device.

* HDFS compatible layer.
* Uses Thrift
* inode, sblocks are ColumnFamilies.
* data is mmap'd so reads are stupid fast.
* Note that HDFS blocks need to be broken up to sub-blocks because you can't stream from Cassandra.

This allows you to move from HDFS to Brisk really quickly.

BriskSnitch
==

Does something that automagically figures out which nodes are Cassandra and which nodes are Brisk.
