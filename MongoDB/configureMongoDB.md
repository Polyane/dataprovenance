# Configure MongoDB Cluster with Docker

For create the mongodb cluster we will use three machines, one machine for the primary node and two for secundary nodes.

![MongoDB Cluster](https://github.com/Polyane/dataprovenance/blob/master/MongoDB/mongoCluster.PNG)

Before configuring the mongodb cluster you must install docker on all machines: [README.md](https://github.com/Polyane/dataprovenance/blob/master/README.md)

Create the directory on the host where the database data will be persisted:

```
$ mkdir /opt/mongo
$ mkdir /opt/mongo/data
$ cd /opt/mongo
```

These folders must be created on all nodes so that it is possible to persist the data on all nodes.

Generating a key that will be used to perform synchronization between mongodb replicas, and ensuring that data in addition to safely traversing is replicated between all nodes, this key must be copied to all nodes as well.

```
$ openssl rand -base64 741 > mongodb-keyfile
$ chmod 600 mongodb-keyfile
$ chown 999 mongodb-keyfile
$ scp /opt/mongo/mongodb-keyfile <ip machine 2>:/opt/mongo/mongodb-keyfile
$ scp /opt/mongo/mongodb-keyfile <ip machine 3>:/opt/mongo/mongodb-keyfile
```

Acess the machine 2 and execute:

```
$ cd /opt/mongo
$ chmod 600 mongodb-keyfile
$ chown 999 mongodb-keyfile

```

Start a container of the mongodb image to make some settings:

```
$ docker run --name mongo -v /opt/mongo/data:/data/db -v /opt/mongo:/opt/keyfile --hostname="node1" -p 27017:27017 -d mongo:2.6.5 --smallfiles
```

Access the container and configure the database users:

```
$ docker exec -it mongo /bin/bash

root@node1:/# mongo
> use admin
db.createUser( {
     user: "admin",
     pwd: "PASSWORD_ADMIN",
     roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
   });

db.createUser( {
     user: "root",
     pwd: "PASSWORD_ROOT",
     roles: [ { role: "root", db: "admin" } ]
   });
> exit
root@node1:/# exit
```

Remove the container so we can now start the whole environment (three nodes):

```
$ docker stop mongo
$ docker rm mongo
```

Start the environment. Replace in the command the tag (<ip machine x>) with the ip from your machine x.

```
$ docker run --name mongo -v /etc/hosts:/etc/hosts -v /opt/mongo/data:/data/db -v /opt/mongo:/opt/keyfile --hostname="node1" --add-host node1.db:<ip machine 1> --add-host node2.db:<ip machine 2> --add-host node3.db:<ip machine 3> -p 27017:27017 -d mongo:2.6.5 --smallfiles --keyFile /opt/keyfile/mongodb-keyfile --replSet "rs0"
```

Access this container to start the cluster:

```
$ docker exec -it mongo /bin/bash
root@node1:/# mongo
> use admin
> db.auth("root", "PASSWORD_ROOT");
> rs.initiate({_id: "rs0", members: [{_id: 0, host: "node1.db:27017" }]})

{
        "info" : "Config now saved locally.  Should come online in about a minute.",
        "ok" : 1
}

> rs.conf()

{
        "_id" : "rs0",
        "version" : 1,
        "members" : [
                {
                        "_id" : 0,
                        "host" : "node1.db:27017"
                }
        ]
}
```

On the other nodes, raise the containers of the following:

Machine 2

```
$ docker run --name mongo -v /etc/hosts:/etc/hosts -v /opt/mongo/data:/data/db -v /opt/mongo:/opt/keyfile --hostname="node2" -p 27017:27017 -d mongo:2.6.5 --smallfiles --keyFile /opt/keyfile/mongodb-keyfile --replSet "rs0"
```

Machine 3

```
$ docker run --name mongo -v /etc/hosts:/etc/hosts -v /opt/mongo/data:/data/db -v /opt/mongo:/opt/keyfile --hostname="node3" -p 27017:27017 -d mongo:2.6.5 --smallfiles --keyFile /opt/keyfile/mongodb-keyfile --replSet "rs0"
```

Go back to machine 1, go to the mongo container and execute the commands:

```
$ docker exec -it mongo /bin/bash
root@node1:/# mongo
> use admin
> db.auth("root", "PASSWORD_ROOT");
> rs0:PRIMARY> rs.add("node2.db") 

{ "ok" : 1 }

> rs0:PRIMARY> rs.add("node3.db") 

{ "ok" : 1 }

> rs0:PRIMARY> rs.status()
```

Return status command:

```
rs0:PRIMARY> rs.status()
{
        "set" : "rs0",
        "date" : ISODate("2018-03-31T14:59:53Z"),
        "myState" : 1,
        "members" : [
                {
                        "_id" : 0,
                        "name" : "node1.db:27017",
                        "health" : 1,
                        "state" : 1,
                        "stateStr" : "PRIMARY",
                        "uptime" : 1433,
                        "optime" : Timestamp(1522508129, 1),
                        "optimeDate" : ISODate("2018-03-31T14:55:29Z"),
                        "electionTime" : Timestamp(1522507242, 2),
                        "electionDate" : ISODate("2018-03-31T14:40:42Z"),
                        "self" : true
                },
                 {
                        "_id" : 1,
                        "name" : "node2.db:27017",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 287,
                        "optime" : Timestamp(1522508129, 1),
                        "optimeDate" : ISODate("2018-03-31T14:55:29Z"),
                        "lastHeartbeat" : ISODate("2018-03-31T14:59:53Z"),
                        "lastHeartbeatRecv" : ISODate("2018-03-31T14:59:53Z"),
                        "pingMs" : 1,
                        "syncingTo" : "node1.db:27017"
                },
                {
                        "_id" : 2,
                        "name" : "node3.db:27017",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 32,
                        "optime" : Timestamp(1522508129, 1),
                        "optimeDate" : ISODate("2018-03-31T14:55:29Z"),
                        "lastHeartbeat" : ISODate("2018-03-31T14:59:51Z"),
                        "lastHeartbeatRecv" : ISODate("2018-03-31T14:59:52Z"),
                        "pingMs" : 5,
                        "syncingTo" : "node1.db:27017"
                }
        ],
        "ok" : 1
}
```
Source: https://www.mundodocker.com.br/cluster-mongo-em-docker/
