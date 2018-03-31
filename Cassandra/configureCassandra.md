# Configure Cassandra Cluster with Docker

To create the cassandra cluster we will use two machines. The cassandra cluster uses the ring architecture.

![Cassandra Cluster](https://github.com/Polyane/dataprovenance/blob/master/Cassandra/cassandraCluster.PNG)

Create the directory on the host where the database data will be persisted:

```
mkdir /opt/cassandra
mkdir /opt/cassandra/data
```
These folders must be created on all nodes so that it is possible to persist the data on all nodes.

Start a container of the cassandra in both machines:

Machine 1

```
docker run --name cassandra -d -e CASSANDRA_BROADCAST_ADDRESS=<ip machine 1> -e CASSANDRA_CLUSTER_NAME=provbio -e CASSANDRA_SEEDS="<ip machine 1>, <ip machine 1>" -p 7000:7000 -v /opt/cassandra/data:/var/lib/cassandra cassandra:3.11.2
```
Machine 2

```
docker run --name cassandra -d -e CASSANDRA_BROADCAST_ADDRESS=<ip machine 2> -e CASSANDRA_CLUSTER_NAME=provbio -e CASSANDRA_SEEDS="<ip machine 1>, <ip machine 1>" -p 7000:7000 -v /opt/cassandra/data:/var/lib/cassandra cassandra:3.11.2
```
Source: https://hub.docker.com/_/cassandra/
