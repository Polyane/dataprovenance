```
 mkdir /opt/provbio
```

```
$ docker run --name provbio -v /opt/provbio:/opt/provbio -itd -p 2424:24242 -p 2480:2480 -p 27017:27017 -p 7000:7000 polyane/provbio
$ docker exec provbio bash
$ docker exec -it provbio bash
```

```
sudo chown -R biolinux.biolinux /opt/provbio/
cd /opt/provbio
fastq-dump -I --split-files ERX412400
fastq-dump SRR5181508
```
