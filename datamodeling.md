Data Modeeling
==

Matthew F. Dennis // Datastax
@mdennis

Goals
--

* Data queried together should be kept on disk together.
* Start with the queries you want, then defined data structure
  * What are the questions I want to answer, and how to answer them efficiently
* Don't normalize
* Keep a record that something happened, instead of changing a value

ClickStream Data
--

Use case #1

* The sequence of clicks a user took

h3. Data Schema

* CF for a sesion a user had
  * Rowkey == user name/id
  * column name is session id (TimeUUID)
  * column value is empty (or could be a aggregated values like if they bought something)

* CF for actural sessions
  * Row Key is a TimeUUID session id
  * Column Name is timestamp/TimeUUID
  * Value is click data (json/xml/etc)

Everyone hates supercolumns now.  They are slow, and you shouldn't use them.
TimeUUID means that data comes back sorted by time

h4. Alternative schemas

h5. Secondary Indexes

Secondary indexes are better for cases where you expect to return more than one row

h5. All Sessions in One Row

* Row Key is userId
* Column Name is composite of timestamp and sessionId
* More efficient to request activity of a user across all sessions within a time range


h5. Time Period Partitioning

* Row Key is composite of userId and time "bucket"
* Column name is TimeUUID of click
* Column Value is serialized click data
* Easy to aggregate, since you know when a bucket will never change
  * Cache/aggregate data on read


Rolling Time Window
--

Use case #2

* Row Key is the id of the data you tracking e.g. "System7:RenderTime"
* Column Name is TimeUUID
* Value is data point

Use TTL if you need to expire.  Very cheap way to get rid of data you don't
want anymore.


Rolling Window of Counters
--

Use case #3

How many times has a user logged in the last 12 months, 7 days, 1 hour, etc.

* On row for duration
* Query higher level buckets that fall completely in the queried time span
* Then query lower level buckets to fill in left side and right side

Eventually Atomic
--

Use case #4

Transaction logs


