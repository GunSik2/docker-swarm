


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
sudo convoy daemon --drivers glusterfs --driver-opts glusterfs.servers=10.128.0.3 --driver-opts glusterfs.defaultvolumepool=vol

ERRO[0000] Failed to execute: mount [-t glusterfs 10.128.0.3:/vol /var/lib/rancher/convoy/glusterfs/mounts/vol], output Mount failed. Please check the log file for more details.
, error exit status 1
{
        "Error": "Failed to execute: mount [-t glusterfs 10.128.0.3:/vol /var/lib/rancher/convoy/glusterfs/mounts/vol], output Mount failed. Please check the log file for more details.\n, error exit status 1"
}

```


Reference
- https://github.com/rancher/convoy/blob/master/docs/glusterfs.md
