Chris Goffinet
--
Twitter
==

Came in a little late because the other session was full. I think I missed their column family definitions.
Sounds like Twitter really contributes a lot to Cassandra. This guy is Twitter's performance guy. He measures and assess their ops for performance and has a ton of tools for doing it automatedly.

Data Storage
--

* Compression
  * Twitter NEEDED compression. The implemented it in Cassandra.
  * Saw a 7x storage saving
* Index Promotion
  * Didn't say WTF this is.
  * Normalize narrow and wide rows.
  * Allows you to not do a double pass on compaction?
* Latency & Throughput
  * Lots of possible culprits of slowness.
  * Compaction was the big one, was running alllllll the time.
  * Predictable Performance == Better Planning
  * Compaction was single threaded. Twitter made it multi threaded.
    * Twitter also made compaction throttled so it ran constantly.

Caching
--
* On heap, off heap caching both suck. Built interface to memcache via the pluggable caching thinger.
  * Interesting: They were using URL as primary key for their tweet button tweets. Variable key size sucked (didn't say why) so they started hashing. This is relevant to our interest!
  * Memcache stored data better than on the JVM (On-heap: 80+ GB of data, Off-heap: 67+ GB of data, 48 GB for Memcache)
  * Long story short: Cassandra might not be an awesome cache.
  * Using cassandra to back the cache, though, doesn't require priming a cache.

Cuckoo
--

Plugged their real-time visualization stuff. Looks cool, they use it as part of monitoring their network.

* Threw around some big numbers. "We have 3.9 GB/s going through our network! We generate 500GB of data an hour!"

Case study for Cassandra
==

GC was killing Cuckoo. GC fragmented the heap which degraded performance.
Created a "slab allocator" -- create fixed-size slabs of memory, copy data from the columns in to the slabs, let the GC do its thing, sync off the slabs.

Pluggable Compaction in Cuckoo
==

Custom TTL implementation, it's built in to the always-running compaction instead of requiring another thread to run it.

Operational Efficiency
--

* Rack awareness is awesome for them.
* Grow their DCs by racks, not by machine, so Cassandra's consistency is awesome for that.
* Fault Tolerance at Disk Level
  * Wrote a patch for Cassandra to detect when soft raid fails. Cass will eject it self from the cluster. Pretty cool.
* To test: Made ops start ripping out disks from production clusters.
  * While gossip and thrift were going down, reads were still being served out of Cassandra's cache!
* HOLY CRAP: 300 node cluster managed by 6 people. Fully automated.
* Weird shit they've seen
  * Memory corruption (i.e. bad memory) causes bad stuff in Cassandra
  * Rack switch failures. Everything kept working because of rack awareness.
  * "If you have any other processes running on you Cassandra machines, I strongly recommend you use mlockall in for GC."

Deploying
--

* Cap
* Hudson
* Git
* Murder (something twitter made...something about BT data transfer)
* Deploy takes 20 seconds (after tests). Hundreds of nodes per cluster.

How it Works
==
  * Stop gossip on node
  * Check ring on all nodes to ensure "Down" state
  * Drain
  * Restart Cassandra

Capacity Planning
--

* Built an in-house capacity planning tool. Must be nice...
* Takes a JSON blob including what throughput should be for reads and writes, sustained traffic, number of servers, etc.
* Kind of hand-wavy about capacity planning.

Testing
--

* Built a cool distributed testing harness
* Custom internal build of YCSB for performance benchmarking

Summary
--

* Understand hardware and OS. It's more than just a Java app on the machine
* Capacity planning with math, not guesses.
* Invest in storage technology.
* Automate everything!
* Expect failure.
