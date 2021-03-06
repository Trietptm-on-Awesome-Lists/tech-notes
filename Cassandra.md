# Cassandra Notes

- Key/Value and a row store. No need for pre-defined schemas, even rows within a ColumnFamily are not required to always follow the same naming schema.
- designed to handle large amounts of data spread across many commodity servers while remaining highly available. Cassandra is loosely 
defined as a key/value store where one key can map to one or more values.
- Is architected to handle real-time big-data workloads across multiple data centers with no single point of failure. Every node in a cluster can serve any request.
- Cassandra is typically classified as an AP system, meaning that availability and partition tolerance are generally considered to be more important than consistency in Cassandra. But Cassandra can be tuned with replication factor and consistency level to also meet C.
- Cassandra supplies linear scalability, meaning that capacity may be easily added simply by adding new nodes online. 

#### Key Features
1. Schema-less if needed.
2. Homogenous Environment - each and every Cassandra node contains everything required to complete a cluster.
3. Tunable Consistency

#### [Configuration](http://cassandra.apache.org/doc/latest/configuration/cassandra_config_file.html) - (Cassandra.yaml)
```bash
# Cluster_name

# num_tokens - default 256 - This means the amount of data in propotion to the cluster this node owns.

# initial_token - default None.
When setting up a new cluster, it is important to manually calculate the tokens for each node that will be in the cluster.

#authenticator - Default: org.apache.cassandra.auth.AllowAllAuthenticator
It is recommended to use a replication factor of 2 or higher when using the PasswordAuthenticator to prevent data loss in the event of an outage.

#authorizer
Default: org.apache.cassandra.auth.AllowAllAuthorizer.

# permissions_validity_in_ms - Default: 2000
If permissions do not change very often, increase this value to increase the read/write performance.

#partitioner
Default: org.apache.cassandra.dht.Murmur3Partitioner.
It is important to note that when using the ordered partitioners such as ByteOrdered pr CollatingOPP, the ability to do range slices is increased  but may also lead to hot spots. If you change this parameter, you will destroy all data in the data directories.

#data_file_directories - Default: /var/lib/cassandra/data.
Cassandra should store the data on disk.

#commitlog_directory Default: /var/lib/cassandra/commitlog.
This value should reside on a different volume from the data_file_directories.

#disk_failure_policy - Default: stop .

#saved_caches_directory - Default: /var/lib/cassandra/saved_caches. 
Specifies the directory in which to store the saved caches.

#commitlog_sync - Default: periodic.
Cassandra will not acknowledge writes until the batch has been fsynced.

#commitlog_sync_period_in_ms - Default: 10000. Specifies the time in which Cassandra will fsync writes to disk. When
using batch syncs, this value should be low as writes will block until the sync happens.

#commitlog_segment_size_in_mb - Default: 32. 
This specifies how large the CommitLog will grow before a new file is created.

#seed_provider- Default: org.apache.cassandra.locator.SimpleSeedProvider.
When running a multiple-node cluster, it is important to have as many seeds as possible so new nodes will be able to bootstrap in the event of an outage of a seed node.

#concurrent_reads -Default: 32.
A general rule of thumb is to set this value to 16 * the number of disks in use by data_file_directories.

#concurrent_writes - Default: 32. The number of concurrent writes. Because writes are appended to the CommitLog, they are almost never #I/O bound. The general rule of thumb for concurrent writes is 16 * the number of cores in the machine.

#memtable_total_space_in_mb - Default: not specified. 
When specified, Cassandra will flush the largest MemTable when this limit has been reached. When left unspecified, Cassandra will flush the largest MemTable when it reaches one-third of the heap.

#listen_address - Default: localhost. 
This is the address that the host listens on. When left unspecified, the listen address will default to the local address. In most cases, this will work. If left at localhost, other nodes may not be able to communicate.

```
1. [Setup a highly avaiable cluster](http://highscalability.com/blog/2016/8/1/how-to-setup-a-highly-available-multi-az-cassandra-cluster-o.html)
2. [Jepsen Cassandra](https://aphyr.com/posts/294-jepsen-cassandra)
3. [Learn Cassandra in X minutes](https://jeoygin.gitbooks.io/learn-y-in-x-minutes/content/cassandra.html)

### Data Modeling
- Need to know the read/write patterns before you create your data model. This also applies to indexes. Indexes in Cassandra are a requirement for specific types of queries, unlike a relational database where indexes are a performance-tuning device.

![](https://teddyma.gitbooks.io/learncassandra/content/assets/sortedmap.jpg)

- Creating a table in Cassandra, also tells it how to store and distribute the data. This is done via the <b>primary key</b> definitiion.

- When there are multiple fields in the PRIMARY KEY , as is the case with compound keys,the first field is the partition key (how the data is distributed) and the subsequent fields are known as the clustering keys (how the data is stored on disk).

- Rows are not segmented accross nodes. This means that the entire row will exist in a particular node, this may cause read/write hotspots due to spikes in writes/reads for that particular row.

#### Key Points
1. Model usage and queries, not the data;
2. Denormalization and duplication of data are not bad—in fact, they are recommended.
3. Collections can be very powerful, but they may impact performance when it comes to very large data sets.

[CQL Command Reference](http://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlCommandsTOC.html)

[Understanding how CQL maps to Cassandra's internal data structure](http://opensourceconnections.com/blog/2013/07/24/understanding-how-cql3-maps-to-cassandras-internal-data-structure/)

#### Internals
- Cassandra is a Dynamo system. it divides a hash ring into a several chunks, and keeps N replicas of each chunk on different nodes. It uses tunable quorums, hinted handoff, and active anti-entropy to keep replicas up to date.

1. [Operating Cassandra](http://cassandra.apache.org/doc/latest/operating/index.html)
2. [Inernals from Cassandra Wiki](https://wiki.apache.org/cassandra/ArchitectureInternals)
3. [Learn Cassandra](https://www.gitbook.com/book/teddyma/learncassandra/details)
4. [Practical Cassandra - A developer's Approach]()
5. [Amazon Dynamo Whitepaper](http://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)
6. [Google Big Table White paper](http://xpgc.vicp.net/course/svt/TechDoc/storagepaper/bigtable-osdi06.pdf)

#### Monitoring
1.[Enable Cassandra Monitoring with Graphana](https://www.pythian.com/blog/monitoring-apache-cassandra-metrics-graphite-grafana/)

2.[Cassandra monitoring part1](https://softwaremill.com/cassandra-monitoring-part-1/)

3. [Cassibility - Prometheus/Cassandra](https://github.com/outbrain/Cassibility)
