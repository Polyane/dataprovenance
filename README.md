# General Settings

In this experiment, we used four machines with Linux CentOS. The following settings must be made on all machines.

# Installing docker on Linux (CentOS)
Repository configuration

```
 $ yum install -y yum-utils

 $ yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

Install Docker CE

```
$ yum install docker-ce

$ systemctl start docker
$ systemctl enable docker

$ docker system info
```

# Assign names to machine's ips in /etc/hosts file

```
$ vi /etc/hosts

<ip machine 1> node1.db
<ip machine 2> node2.db
<ip machine 3> node3.db
<ip machine 4> provbio.biolinux
```

# To enable communication between hosts of different cloud providers, if you wanted to configure a cloud federation environment:

```

$ docker swarm init --advertise-addr <public ip leader machine>

```

The system will return a command similar to this:

```
Swarm initialized: current node (2vfkvgecpiz4avley9v9lt6uz) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-5pumxo2o5n92ye41flboeklf2839dwhw1nfxf9w9m03hxh5tw6-5uhm6n3d9srxvcm0fit3l8pnd 142.93.251.234:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

```

Run the command on hosts that will be part of the cluster:

```
docker swarm join --token SWMTKN-1-5pumxo2o5n92ye41flboeklf2839dwhw1nfxf9w9m03hxh5tw6-5uhm6n3d9srxvcm0fit3l8pnd 142.93.251.234:2377
```

To check the cluster nodes:

```
docker node ls
```

The output of the command will display the nodes that make up the Docker Swarm cluster:

```
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
2r425j1ldlurvhxz1fj28yvfe     polysrv1            Ready               Active                                  18.06.1-ce
yume9g3s1rodzvvt1c2dri4q8 *   polysrv2            Ready               Active              Leader              18.06.1-ce
```
