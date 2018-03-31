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
