


Convoy Installation
```
wget https://github.com/rancher/convoy/releases/download/v0.5.0/convoy.tar.gz
tar xvf convoy.tar.gz
sudo cp convoy/convoy convoy/convoy-pdata_tools /usr/local/bin/
sudo mkdir -p /etc/docker/plugins/
sudo bash -c 'echo "unix:///var/run/convoy/convoy.sock" > /etc/docker/plugins/convoy.spec'
```

## Convoy with GlusterFS
- Install glusterfs-client
```
sudo apt-get -y install glusterfs-client
```
- Run daemon
```
sudo convoy daemon --drivers devicemapper \
  --driver-opts glusterfs.servers=10.1.1.2,10.1.1.3,10.1.1.4 \
  --driver-opts glusterfs.defaultvolumepool=docker-volume
```


Reference
- https://github.com/rancher/convoy/blob/master/docs/glusterfs.md
