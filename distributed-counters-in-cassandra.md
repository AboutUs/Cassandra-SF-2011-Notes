Sylvain Lebresne
==
DataStax
--

Note: This session turned out to be mostly a "how we implemented counters" session.

History
==

* Work on it started in 2009. First committed December 2010.
  * A year to finish.
* Available in 0.8
* Digg started the patch, Twitter did it too.
  * Twitter: Rainbird.

Why Counters?
==

Prior to counters, no good way to count in Cassandra.
* In the past, add a column per increment. Good for reads, bad for writes.
* Use external synchronization?
  * Zookeeper. Locks by cluster.
    * Lock cluster
    * Increment counter
    * Unlock cluser.
    * Doesn't scale.
* Use a different DB (i.e. Redis, etc.?).
  * Bad for obvious reasons.

What are Counters?
==

A counter is a specific column.

* Declare a counter (column of type Counter or Countable, look this up)
* Counters need to be incremented manually. Looks like syntax is INCR COUNTERS(<Column> BY x) where X is an integer...probably positive.

About Counters
--

* Counters need to be fast
* Need to be distributabe.
* "Master" design doesn't work. What do you do if counters are in different data centers?
* Reliable. If the network gets segmented, it should recover the value after the network comes back up.

Limitations of Counters
==

* When you do a write in Cassandra and it fails (i.e. times out or network error), replaying the commit log has a chance of re-incrementing counters.
* No TTL for counters
* If you remove a counter, you cannot increment it (i.e. recreate the counter on the fly, I guess)
  * To reset a counter, remove it then re-insert it -- THEN you can incr it.
* If you loose an SSTable, you will need to remove all the SSTables from the column family and repair.
* No secondary indexes on counters.
* No consistency level ANY for writes.
