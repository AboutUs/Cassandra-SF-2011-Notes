Hive Over Brisk
==

Yewei Zhang
Female Software Engineer - W00t!

What is Hive?
--

* Data warehouse infrastructure on top of Hadoop
  * SQL like query

When to use Hive?
--

* Batch jobs over large set os immutable data
* Large hadoop cluster
* Structured data (schema)

Hive in Brisk
--

* Brisk provides a Metastore and Hadoop infastructure in C*
* MetaStore is sent via thrift into cassandra (and is replicated by C*)
* configure this via `hive-site.xml`


Cassandra Storage Handler
--

    CREATE EXTERNAL TABLE
    cassandra_hive_table(key int, column string, value string)
    STORED BY
    'org.apache.hadoop.hie.cassandra.CassandraStorageHandler'
    WITH SERDEPROPERTIES (
    "cassandra.columns.mapping" = ":key,clumn,value",
    "cassandra.cf.name" = "Table",
    "cassandra.host" = "127.0.0.1",
    "cassandra.port" = "9170")
    TBLPROPERTIES (
    "cassnadra.ks.name" = "Hive" );

Transposed Tables
--

Reserved keywords, `row_key`, `sub_column_name`, `column_name`, `value` => magic transposed tables.

Auto map column_names into a Hive field.  You can also specify how the mapping
into Hive tables should work.


IRC #datastax-bris
