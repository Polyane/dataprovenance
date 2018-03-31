# Configure Cassandra Cluster with Docker

To create the cassandra cluster we will use two machines. The cassandra cluster uses the ring architecture.

![Cassandra Cluster](https://github.com/Polyane/dataprovenance/blob/master/Cassandra/cassandraCluster.png)

Create the directory on the host where the database data will be persisted:

```
$ mkdir /opt/cassandra
$ mkdir /opt/cassandra/data
```
These folders must be created on all nodes so that it is possible to persist the data on all nodes.

Start a container of the cassandra in both machines:

Machine 1

```
$ docker run --name cassandra -d -e CASSANDRA_BROADCAST_ADDRESS=<ip machine 1> -e CASSANDRA_CLUSTER_NAME=provbio -e CASSANDRA_SEEDS="<ip machine 1>, <ip machine 1>" -p 7000:7000 -v /opt/cassandra/data:/var/lib/cassandra cassandra:3.11.2
```
Machine 2

```
$ docker run --name cassandra -d -e CASSANDRA_BROADCAST_ADDRESS=<ip machine 2> -e CASSANDRA_CLUSTER_NAME=provbio -e CASSANDRA_SEEDS="<ip machine 1>, <ip machine 1>" -p 7000:7000 -v /opt/cassandra/data:/var/lib/cassandra cassandra:3.11.2
```

Access the cassandra on any machine to create a keyspace and column family

```
$ docker exec -it cassandra bash
root@node1:/# cqlsh
Connected to provbio at 127.0.0.1:9042.
[cqlsh 5.0.1 | Cassandra 3.11.2 | CQL spec 3.4.4 | Native protocol v4]
Use HELP for help.
```

Create keyspace and columns family:

```
cqlsh> CREATE KEYSPACE provenance
       WITH REPLICATION = {
       'class' : 'SimpleStrategy',
       'replication_factor' : 2
       };
       
cqlsh> CREATE TABLE provenance.provbio (
   id_provbio int,
   name_project text, 
   description_project text, 
   inst_financing text, 
   inst_participating text, 
   coordinator text,
   date_begin_project date,
   date_end_project date,
   name_exp text,
   description_exp text,
   local_exp text,
   date_begin_exp date,
   date_end_exp date,
   anotation_exp text,
   version_exp text,
   date_version date,
   time_exec_exp int,
   cost_exec_exp float,
   name_activ text,
   name_program_activ text,
   version_program_activ int,
   command_activ text,
   date_begin_activ date,
   time_begin_activ date,
   date_end_activ date,
   time_end_activ date,
   status_execution_activ int,
   type_activ text,
   name_file_in text,
   description_file_in text,
   localization_file_in text,
   anotation_file_in text,
   size_file_in int,
   file_in blob,
   date_insertion_file_in date,
   type_file_in text,
   name_ag text,
   login_ag text,
   institution_ag text,
   position_ag text,
   anotations_ag text,
   hostname_mac text,
   ip_mac text,
   type_mac text,
   so_mac text,
   cpu_mac int,
   ram_mac int,
   disk_mac text,
   type_disk_mac text,
   price_mac text,
   type_price_mac text,
   name_cluster text,
   description_cluster int,
   name_provider text,
   url_provider text,
   description_provider text,
   region_mac text,
   zone_mac text,
   region_provider text,
   zone_provider text,
   name_file_out text,
   description_file_out text,
   localization_file_out text,
   anotation_file_out text,
   size_file_out int,
   file_out blob,
   date_insertion_file_out date,
   type_file_out text,
   PRIMARY KEY (id_provbio, name_project, name_exp));       
```

Source: https://hub.docker.com/_/cassandra/
