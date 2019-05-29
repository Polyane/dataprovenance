
```
$ mkdir /opt/orientdb
$ mkdir /opt/orientdb/data
$ mkdir /opt/orientdb/config
```

Machine 1

```
$ docker run -d --name orientdb --net=overnetwork -p 2424:2424 -p 2480:2480 -e ORIENTDB_ROOT_PASSWORD=entra123 \
-e ORIENTDB_NODE_NAME=node1 \
-v /opt/orientdb/data:/orientdb/databases \
-v /opt/orientdb/config:/orientdb/config orientdb
```

Machine 2

```
$ docker run -d --name orientdb --net=overnetwork -p 2424:2424 -p 2480:2480 -e ORIENTDB_ROOT_PASSWORD=entra123 \
-e ORIENTDB_NODE_NAME=node2 \
-v /opt/orientdb/data:/orientdb/databases \
-v /opt/orientdb/config:/orientdb/config orientdb
```

```
docker exec -it orientdb /bin/sh
```
