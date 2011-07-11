Matthew F. Dennis
==
DataStax
--

Goals
==

* The data you're querying needs to stay together.
  * Avoids seeks
  * Relational DBs are bad at this, rows get intersperesed.
* Start with the questions you want to answer, then figure out how you would store that efficiently in Cassandra.
* Don't normalize data too much.
* Better to keep a record that something happened as opposed to a changing value.

Use Case: ClickStream data
==

What do you want to do?
--

* Record actions within a session

How would you structure it?
--

* Column family for session a user had.
  * Row key is user name or user id
  * Column Name is session id
  * Column Value is empty.
* Column family for actual sessions
  * Row key is TimeUUID session id
  * Column name is timestamp
  * Column value is a JSON or XML blob
    * _Why JSON or XML?_ You don't want to use SuperColumns. SuperColumns have a 10-15% performance penalty on read or writes. Also, this data is immutable. No need to change the data!
    * Maybe we aren't doing it so "wrong" then?

* Append columns to UserSessions
  * userId: session_01, session_02, session_03
  * Note: If you use TimeUUID comparator, it will bring them out in the right order.
    * We should do this for our run_ids

Partitioning by Time
--

"Bucketing" data: Grouping it logically. i.e. "everything that happened in January"

* Row key is composite of userid and time bucket
* Column name is TimeUUID of Click
* Column Value is serialized click data
* Can aggregate these.

Use Case: Rolling Time Window of Data Points
==

Data point is a value or a set of descrete values that corresponds with a specific point in time or an interval.

I got lost here, I have the information but I don't have time to write it now.

Eventually Atomic
==

How do you cope with losing ACID? Example: User uploads a bunch of pictures but they're all related to another user. Answer: Tune consistency!
