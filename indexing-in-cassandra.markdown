Indexing in C*
=========
Ed Anuff
Works at usergrid_ - backend for mobile apps

C* 0.6
--

* No bulit in support
* Write your own index system

C* 0.7
--

* Native secondary indexes
* Deceptively similar to SQL indexes

Two ways to find things in C*
-- 

* primary index (row key)
* sometime it's menaingful, often its a time base UUID

Get vs. Find
--

* Don't try to use your row key to do find
* Usually alternate index is better to find/search things

Alternate indexes
--

Anything other than using a the row key

* Native secondary indexes
* Wide rows as lookup and grouping tables
* Custom secondary indexes

Remember there is no magic here. YMMV.

Native secondary indexes
--

* Easy to use
* Allows "WHERE" in queries
* Great starting point, but limited

h3. Under the hood

* Store in a "hidden CF
* Notes index the rows they store
* Does equaliry operation

h3. Limitations

* Not ideal for high cardinality values (timestamps, birthdates, keywords, etc.)
* Requires at least one equality comparison.  Not great for >, <, range queries
* Unsorted - results are in token order, not query order value
* Limited to search on data types C* natively understands (not too bad)

When you hit the limitations of built in indexes
--

Build your own in your App layer
"Not as bad as it seems"

Wide Rows
--

"Why would a row need 2 billion columns?"

* Basis of all indexing, organizing, and relationships in C*
* If you don't have wide rows you're doing it wrong

h3. Wide row is where:

* Column names are keys of rows in "Users" CF
 * All people in "Engineering group"

h3. Wide row as a simple index

    "alden"    : "e5f...",
    "anderson" : "e5f...",
    "anderson" : "e5f...", #=> FAIL


* CF operation are very fast
* Column slices can be retreived by range, are alsways sorted, and can be reversed.
* If target key is TimeUUID, you get free sorting by time.  Good for feeds
* If you have to combine more than one group

h3. Doesn't work for many-to-one. Super columns to the rescue

    "alden"    : "e5f...",
    "anderson" : ["e5f...", "e5f..."]

* Use supercolumns with caution, not highly recommended
* Sorts only on the super column, not subcolumn
* Some performance issues
* What if I want more nesting?
 * Can subcolumns have subcolumns? NO!
* Anecdotally, many projects have moved away from supercolumns


h3. So how do you do many-to-one w/o supercolumns?

* You could have a compound key:
    {"alden", 1}    : "e5f...",
    {"anderson", 1} : "e5f...",
    {"anderson", 2} : "e5f...",

h3. Two Types of Composites

* Base composite type
 * One index CF per query type or CF
 * e.g. `CompositeType(UTF8Type, UUIDType)`
* Dynamic composite type
 * One index CF for all indexes
 * e.g. `DynamicCompositeType(s=>UTF8Type, u=>UUIDType)`

> ASIDE: Check out the UUIDType

* Queries are easy
  * Regular column slice operations
* Updates are harder
  * Remove old values, and insert the new value
  * Uh oh, read before write

Example -- Users By Location
--

* 3 Column Families (not 2)
* First 2 CFs are obvious
  * Users
  * Indexes (Users_By_Location) {<location>, <user_key>, <ts>}
* Also need User Index Entries, CF
  * Solves read before write problem

There's sample code on github. Hector JPA does this all for you behind the
scenes.

FAQs
--

1. Do I need locking? => NO
1. Retrys are ok. Idempotent
1. False positives? => YES, but filter on read
1. Who uses this?


Other Thoughts
--

* Indexes can be derived from column values
 * Create a "last_name,firstname" index from a "fullname" column
 * _Unroll JSON object to construct deep indexes of serialized JSON structures_
* Include addition denormalized values in the index for faster lookup
* Use composites for column values too not just column names

Examples online
--

* https://github.com/edanuff/CassandraIndexedCollections
* https://github.com/riptano/hector-jpa
* https://issues.apache.org/jira/browse/CASSANDRA-2231

