C* at Urban Airship
==

Eric Onnen

Director of Platform Engineering at Urban Airship
DOE at Jive before that

Talks about what UA does
--
blah, blah
Vanity stats

100k deliveries per second
6months to deliver first 1M messages. Just broke 4 Billion.

Transactional System:
--

* Edge Systems:
 * API - Apache/Python/django+pston+pycassa
 * Device negotiation - JAva NIO + Hector
 * Message Delivery

* Persistence
 * Sharder PostGre
 * Cassandra
 * MongoDB

A Tale of Storage Engines
--

* Cassandra
* HBase
* Redis
* Mongo (on the way out)

Converging on Cassandra, and Postgre

* PostgreSQL
 * Run Postgre in EC2
 * Added memcache
 * Still use for some "silos of data"


Mongo
--
* Mongo doesn't block to write to disk
* Works until data doesn't fit into memory
* One global lock, later one read lock, one write lock
* Long running queries were devastating
  * Replication fails
  * No writes or updates
* Basically a lot of info on why Mongo sucks big ones

Cassandra
--

h3. Pros:

* By December 2010
  * C* backed 85% of our Android stack persisitance
  * 6 EC2 XLS wit each serving 30 Bg data
* Rolling upgrade FTW
  * Only exception is 0.6 => 0.7
* Dynamic rebalancing
* Column TTLs huge
  * Don't need sweepers
* Works well on EC2
* Read repair and handoff generally did the right thing when a node would flap
* No SPoF
* Ability to alter Consistency Levels on a per operation basis

h3. Battle Scars:

* Know your data model
  * Creating indexs after the fact is a PITA
  * Design around wide rows
    * I/O problems
    * Thrift problems
    * Count problems
  * Favor JSON over packed binaries if possible
* Careful with Thrift in the stack
  * Can be painful in upgrades
* Don't fear the StorageProxy
  * Read the source code
* Assume failure in the client
  * Understand difference between _read timeout_ and _connection refused_
  * When maintaining your own indexes, try and cleanup after failure
  * Be rady to cleanup inconsistencies anyway
  * Verify client library assumptions and exception handling
    * Exceptions from thrift are hard to understand
    * Retry now vs. retry later
* Embed for testing
* EC2
  * Ensure Dynamic Snitch is enabled
  * Disk IO sucks
    * Don't run on EBS except for snapshot backups or use s3
    * Stripe ephemerals, not EBS volumes
  * Avoid smaller instances all together
  * Don't assume traversing close poximity AZs is more expensive
  * Balance RAM cost vs. the cost of additional hosts and spending time w/ GC logs
* Bare metal will rock your world
* +UseLargePages will rock your world (JVM option)
* Get familiar with Java GC logs (-XX:+PrintGCDetails)
  * Understand what degenerate CMS collection looks like
* When in doubt take a thread dump
  * TDA (http://java.net/projects/tda)
* Understnad when to compact
  * They compact at night manually
* Watch hinted handoff closely
  * Avoid thundering herd effect when node comes back after GC cycle
* Monitor JMX religiously
  * C* provides a lot of RAID info via JMX
