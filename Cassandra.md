#Cassandra Notes

- Key/Value and a row store. No need for pre-defined schemas, even rows within a ColumnFamily are not required to always follow the same naming schema.
- designed to handle large amounts of data spread across many commodity servers while remaining highly available. Cassandra is loosely 
defined as a key/value store where one key can map to one or more values.
- Is architected to handle real-time big-data workloads across multiple data centers with no single point of failure. Every node in a cluster can serve any request.

####Key Features
1. Schema-less if needed.
2. Homogenous Environment - each and every Cassandra node contains everything required to complete a cluster.
3. Tunable Consistency

####Configuration - (Cassandra.yaml)
```bash
# Cluster_name

# num_tokens - default 256 - This means the amount of data in propotion to the cluster this node owns.

# initial_token 
```
[Setup a highly avaiable cluster](http://highscalability.com/blog/2016/8/1/how-to-setup-a-highly-available-multi-az-cassandra-cluster-o.html)
[Jepsen Cassandra](https://aphyr.com/posts/294-jepsen-cassandra)
