State of Cassandra
==
Jonathan Ellis

Lots of pepole are using C*

Why?
---

Big data is a problem many people face now

C* is winning in the marketplace because it has good architectural decisions.
--

Why?
--

* Multi master
* Linearly scalable
* Larger than memory datasets
* Performant
* Fully durable
* Integrated caching (new in 0.8)
* Tuneable consistency

In 0.8
---

Row caching is much improved
Lots of effort into improving read performance.
Read performance is good when working set can fit in memory

Write performace
--
No random IO on write.
No locks, concurrent, compare and swap.

Consistency
--

Frequently categorized as "eventually consistent".  It's tunable so it can be
fully consistent.

On a per operation basis you can tell C* to block for consistency or not.

Trades of Availability for Consistency

Consistency levels:
ANY, ONE, LOCAL_QUORUM (new in 0.7), QUORUM, ALL

LOCAL_QUORUM: Get quorum consistency within the datacenter

0.7 enhancements
--

* Efficient replications across data centers
* In database schemas: CREATE COLUMN FAMILY
* Expiring columns (TTL)
* Secondary (column) indexes
** We should use this.  Query by non key values.

0.8 enhancements
--

* CQL Query language
** Higher level interface to build client APIs on top of
** Fuck off thrift
** Better ease of use
** More commonaility between clients in different languages
** This seems really awesome
* Counters
* Automatic memtable tuning
** Better memory management, especially with many column families
* New bulk load interface
** replaces shitty "binary memtable interface"
** Write out memtable locally and stream to node
** Clients do most of the work
*** e.g. Hadoop job which bulk loads to C*

Road to 1.0
--
Planned release is October
Feature freeze in 2 months

* Repair
** Anti entropy -- Like Rsync, It should only trade hashes until something is
found to be out of sync BUT:
*** Consistency is checked per ColumnFamily but data is transferred per-Keyspace
*** Merkle trees requests are sent en masse, but may not execute start at the
   same time

* Compression
** C* data model makes this hard because rows can be very different sizes
** Two approaches they're working on

* Optimize for heavy read load
** Worst case workload for reads - Have to mege every SSTable to do one read
** Fixing with Level-based compaction
*** Generate more sstables instead of larger one
*** Worst case is a row will be present in each level instead of each table

* CQL 1.1 enhancements
** Alter
** Counter support
** TTL support
** Compound columns
** Prepared statements

Post 1.0
--

* Very focused on improving ease of use for application developers
* "Native" CQL Transport -- fuck thrift
** "Thrift is a bear to build"
* Triggers
* Entity groups
* Smarter range queries

Brisk
--

* Hadoop on C*
* Analytics for realtime data w/o ETL
** partition cluster into realtime nodes and analytics nodes

