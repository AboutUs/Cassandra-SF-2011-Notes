Adrian Cockcroft
==
Netflix
--

Netflix just announced support internationally.

Why use the public cloud anyway?
==

* Frictionless deployment.
* Create a cluster of any size anywhere in the world.
  * Obviously, they don't have people everywhere in the world.
* Never run out of space, power, and never wait for stuff.
* Bottom line: Better business agility.
  * Netflix couldn't build data centers fast enough!
  * Netflix saw 37x growth in data last year.
  * Some bits aren't in the cloud. All of the internation stuff is.

Why Amazon?
--

Biggest cloud with the most features. Custom features on top of it, custom tools.

* They had lots of automation out of the box that was useful.

Port to the Cloud
==

* Port took...a while, missed how long.
* All the PS3 UIs were because they were doodling around with PS3s.

Goals
--

* Horizontal scalability
* Better programmer productivity
* Faster
* Available

Anti-patterns
--

Defined as part of the migration.

Datacenter Anti-Patterns
==

* Central SQL
* Sticky In-Memory sessions
* Chatty Protocols
* Tangled service interfaces
* Instrumente code
* Complex objects, unserializeable
* Components as Jar Files

SQL Database
--

* People put a ton of data in a single database.
* Basically a single point of failure.
* Schema changes require downtime.
  * Impacts scalability and availablility

Migrating data to Cassandra
==

This is a transitional step.

* Bidirectional replication
  * Scrape data from Oracle and put it in to SimpleDB (they used SimpleDB before Cassandra)
  * Backups remain in DC via Oracle
* Built the new coud-only data sources
  * Cassandra-based
  * No replication to DC

New Challenges
--

* Backup, restore of a TON of data.
  * Backup to S3.

Replication
==

* Replication factor set such that data goes to all availability zones in AWS.
* Quorum set to 2 of 3.

Backup
==

* Cron job takes a snapshot -> tar -xcvf -> S3. (Full)
* Any time an SSTable is written, it's copied to S3. (Incremental)
* Scrape commit log and write to EBS every 30s. (Continuous)

Restore
--

* Can restore EVERY NODE at once. It looks at the bucket, figures out how many devices are writing to that bucket, creates that many machines and puts that data on those machines.

ETL
==

* Using Brisk/Hadoop and the backup data for doing analytics and stuff.
* Use parts of a Brisk ring to do intra-day extractions.

Concerns with Archiving
==

* Make sure the data that was snapshotted is readable by restoring before archiving.
  * Run an extraction against it, is what they do.
* Encrypt your archive.
* East region could have problems, so make sure it's in West too!
* Don't allow production accounts to delete data.
* Keep an extra copy with a different cloud vendor incase S3 explodes somehow.

Deploy
==

Interesting bit: Netflix's build creates a fully-baked AMI. The build, generate the WAR, inject that in to a FS, build the AMI, and tell Amazon to launch that AMI.

Migrating Developers
==

* Detailed SQL to NoSQL Transition Advice
  * Sid Ananod - QConSF Nov. 5th
  * Blog: http://practicealcloudcomputing.com/

Cassandra Use Casses
==

* Key data by customer. for customer data.
* Key customer:movie - e.g. Viewing History
  * Remembers where you were in a movie!
  * "You are in Season 5, Episode 3"
  * Sized to keep hot data in memory
* Large scale data logging - lots of writes
  * Data expires after time period
  * Distributed counters would help here.

Model Driven Architecture
==

* Datacenter Practices
  * Hard to enfouce patterns

Use a Core Platform. i.e. Netflix
--

* Tomcat Server, always running, manages cass state
* SimpleDB configuration
* S3 backed AMIs for stateful services.

Chaos Monkey
==

* Systems should be resilient
* Artifically kill instances randomly to test error handling and make sure stuff works properly.
* Computers randomly die throughout the week.

