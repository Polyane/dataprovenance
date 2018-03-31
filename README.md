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
