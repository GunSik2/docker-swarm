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


## Structure

![](https://www.google.co.kr/url?sa=i&rct=j&q=&esrc=s&source=images&cd=&cad=rja&uact=8&ved=0ahUKEwjvl4bO-MXRAhXMnZQKHT32ChsQjRwIBw&url=http%3A%2F%2Francher.com%2Fintroducing-convoy-a-docker-volume-driver-for-backup-and-recovery-of-persistent-data%2F&psig=AFQjCNGhw20uN2kAD7s4shzew-VEU6x_LA&ust=1484630425270520)


## Reference
- https://github.com/rancher/convoy
- http://rancher.com/introducing-convoy-a-docker-volume-driver-for-backup-and-recovery-of-persistent-data/
