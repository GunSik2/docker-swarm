Guluster Container
=========

## Objective
How to install and use guluster container?

## Installation
```
$ docker pull gluster/gluster-centos
```

### (Optional) Creating your own docker image
```
$ git clone git@github.com:gluster/gluster-containers.git
$ docker build -t gluster-centos docker/CentOS/Dockerfile
```

## Running gluster container

### Running options
 - --net=host               : This option brings maximum network throughput for your storage container(Optional)
 - --privileged=true        : If you are exposing the `/dev/` tree of host to the container to create bricks from the container
 - Bind mounting of following directories enables:
   - `/var/lib/glusterd`     : To make gluster metadata persistent in the host.
   - `/var/log/glusterfs`    : To make gluster logs persistent in the host.
   - `/etc/glusterfs`        : To make gluster configuration persistent in the host.
```
$ mkdir -p /etc/glusterfs /var/lib/glusterd /var/log/glusterfs
$ docker run --name gluster -d \
    --privileged=true --net=host \
    -v /etc/glusterfs:/etc/glusterfs:z \
    -v /var/lib/glusterd:/var/lib/glusterd:z \
    -v /var/log/glusterfs:/var/log/glusterfs:z \
    -v /sys/fs/cgroup:/sys/fs/cgroup:ro \
    -v /dev/:/dev gluster/gluster-centos

```

### Verify running
- inspect the container
```
$ docker ps -a
$ docker inspect gluster
```

- get inside the container
```
$ docker exec -ti gluster /bin/bash
# ps aux |grep glusterd
# gluster peer status
# gluster --version
```

### Run the second gluster
- Create a new gluster node by following the previous steps
- configure cluster on both nodes
```
vi /etc/hosts
192.168.0.100	server1.example.com	gluster1
192.168.0.101	server2.example.com	gluster2

gluster peer probe gluster1
gluster peer probe gluster2
gluster peer status
```

### Start volume
- mount
```
sudo mkdir -p /mnt/gluster
gluster  volume create datapoint replica 2 transport tcp
```
- start volume
```
gluster volume start datapoint
```
- view volume
```
gluster volume info
```

## Reference
- https://github.com/gluster/gluster-containers
- http://www.slideshare.net/HumbleChirammal/gluster-containers
- http://www.humblec.com/building-glusterfs-in-a-docker-container/
