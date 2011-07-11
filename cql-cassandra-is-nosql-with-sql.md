Eric Evans
==
Rackspace
--

Works at Rackspace, commits to Cassandra. @jericevans. This is the guy that coined the term NoSQL!

Sounds like he worked on the RPC API. Today, to get at data in Cassandra, you can use one of three things:

* RPC API
* Third Party API via Thrift
* CQL

HISTORY
==

RPC API sucked. New Goal: Build a new interface identical to something that doesn't suck
==

* Same API as Avro (http://avro.apache.org/) -- something he also built.
* Still wasn't "complete" -- people kept asking "When is the API going to be done?" when it was...done.

Reasons it sucked still
--

* Unstable because too tightly coupled to internal APIs
  * Upgrading Cass was not fun.
* Too difficult to use.

Other considered technologies when building out a "new" way of getting at data
==

* RPC
* REST
  * HTTP is your client, so little abstraction
  * But HTTP doesn't map well to a query model.
  * Fails for Cassandra's pluggable type system (no way to describe the types)
* Some query language...

What is CQL?
==

* Structured query language for Apache Cassandra.
* "Pretty much is SQL"
* Availbe for use in Cassandra 0.8.0
* "No mandate coming down from on high that says we need to replace the RPC API"
* Ready to use now!

Why bring CQL (SQL) back?
==

* Because the RPC API sucks! (His words, not mine)
  * "Thrift sucks so the API sucks."
    * This guy hates thrift too.
  * Kinda part of the community, according to him.
    * Hard to get patches pulled in
    * Hard to get bug fixes
  * "Upstream alternating between extremes of combativeness and apathy"
  * "To have to pull in a compiler (read: thrift) to have to use a client for Cassandra is just terrible."
  * Thrift is technically generated code, but all the libs aren't bound to a C lib for Cassandra. Thus, all implementations are COMPLETELY differen't. There is no common factor.
  * Languages support for thrift isn't great.
* The structure of the query succintly describes what the engine is going to do.
* And, of course, everyone knows SQL

LANGUAGE
==

Versioned Spec
==

* Stored in the soure tree and the document itself is versioned.
  * Major, Minor, and Patch version.

And...that's it. If you want to learn about the stuff, read the spec I guess. I got a little distracted about here, but it didn't seem like he was actually talking about CQL in terms of syntax or usage here.

From Q&A
==

* Aggregate functions?
  * Should be supported.
* Is it a subset of SQL?
  * Yes, but a little different.
* JDBC?
  * Maybe...write your own, just don't left Thrift types bubble up through it!! They want to be able to change some stuff.
* Compatible with Python 3?
  * Probably not...just like everything else (my words).
* Performance? What cases where Thrift is better and where is CQL better?
  * GC is the enemy in Cass. Any query that generates more garbade is faster in CQL.
* Fun fact: Bike shedding is bringing something to commitee that is very complex and everything thinks they know about it.
* Cursors? "Uh...(thinking)...yeah? probably?"
