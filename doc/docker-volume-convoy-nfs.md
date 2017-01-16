Docker volume with convoy nfs
======

## Convoy Installation
```
wget https://github.com/rancher/convoy/releases/download/v0.5.0/convoy.tar.gz
tar xvf convoy.tar.gz
sudo cp convoy/convoy convoy/convoy-pdata_tools /usr/local/bin/
sudo mkdir -p /etc/docker/plugins/
sudo bash -c 'echo "unix:///var/run/convoy/convoy.sock" > /etc/docker/plugins/convoy.spec'
```

## Run convoy daemon
- Run convoy daemon
```
$ sudo mkdir <vfs_path>
$ sudo mount -t nfs <nfs_server>:/path <vfs_path>
$ sudo convoy daemon --drivers vfs --driver-opts vfs.path=<vfs_path>

sudo mkdir nfs
sudo mount -t nfs 10.128.0.3:/var/nfs nfs
sudo convoy daemon --drivers vfs --driver-opts vfs.path=$(pwd)/nfs
```
- [convoy daemon init script](https://gist.github.com/deviantony/557984d62e867e6f505577b207db6ffc)


## Create a volume
```
sudo convoy create <volume_name> --size 2G
sudo convoy create vol1 --size 2G
```

## Run docker with convoy volume
- run
```
sudo docker run -v vol1:/vol1 --volume-driver=convoy ubuntu touch /vol1/foo
```
- check it
```
sudo ls nfs/vol1/foo
```

## Volume handling
- delete a volume with convoy
```
sudo convoy delete vol1  
```
- delete a volume with docker
```
sudo docker rm -v <container_name>
```
- list and inspect a volume
```
sudo convoy list
sudo convoy inspect vol1
```
- take snapshot
```
sudo convoy snapshot create vol1 --name snap1vol1
```
- delete a snapshot
```
sudo convoy snapshot delete snap1vol1
```
- backup a snapshot
```
sudo convoy backup create snap1vol1 --dest s3://backup-bucket@us-west-2/
sudo convoy backup create snap1vol1 --dest vfs:///opt/backup/
```
- restore a snapshot
```
sudo convoy create res1 --backup <url>
```
- Mount a Restored Volume into a Docker Container
```
sudo docker run -it -v res1:/res1 --volume-driver convoy ubuntu
```

## Structure

![](http://cdn.rancher.com/wp-content/uploads/2015/08/20005433/howitworks_graphic1.png)


## Reference
- https://github.com/rancher/convoy
- http://rancher.com/introducing-convoy-a-docker-volume-driver-for-backup-and-recovery-of-persistent-data/

