


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
$ sudo apt-get -y install glusterfs-client
```
- Run daemon
```
$ sudo convoy daemon --drivers glusterfs --driver-opts glusterfs.servers=10.128.0.3 --driver-opts glusterfs.defaultvolumepool=vol

ERRO[0000] Failed to execute: mount [-t glusterfs 10.128.0.3:/vol /var/lib/rancher/convoy/glusterfs/mounts/vol], output Mount failed. Please check the log file for more details.
, error exit status 1
{
        "Error": "Failed to execute: mount [-t glusterfs 10.128.0.3:/vol /var/lib/rancher/convoy/glusterfs/mounts/vol], output Mount failed. Please check the log file for more details.\n, error exit status 1"
}
```
- Check
```
$ sudo mount -t glusterfs 10.128.0.3:vol /mnt/gluster

==> /var/log/glusterfs/mnt-gluster.log <==
[2017-01-13 09:31:00.406560] I [glusterfsd.c:1910:main] 0-/usr/sbin/glusterfs: Started running /usr/sbin/glusterfs version 3.4.2 (/usr/sbin/glusterfs --volfile-id=vol --volfile-server=10.128.0.3 /mnt/gluster)
[2017-01-13 09:31:00.407888] I [socket.c:3480:socket_init] 0-glusterfs: SSL support is NOT enabled
[2017-01-13 09:31:00.407905] I [socket.c:3495:socket_init] 0-glusterfs: using system polling thread
[2017-01-13 09:31:00.408698] E [glusterfsd-mgmt.c:1574:mgmt_getspec_cbk] 0-glusterfs: failed to get the 'volume file' from server
[2017-01-13 09:31:00.408715] E [glusterfsd-mgmt.c:1664:mgmt_getspec_cbk] 0-mgmt: Server is operating at an op-version which is not supported
[2017-01-13 09:31:00.408827] W [glusterfsd.c:1002:cleanup_and_exit] (-->/usr/lib/x86_64-linux-gnu/libgfrpc.so.0(rpc_clnt_notify+0x105) [0x7fa38011fe15] (-->/usr/lib/x86_64-linux-gnu/libgfrpc.so.0(rpc_clnt_handle_reply+0x90) [0x7fa38011fc10] (-->/usr/sbin/glusterfs(mgmt_getspec_cbk+0x3b5) [0x7fa3807d6c75]))) 0-: received signum (0), shutting down
[2017-01-13 09:31:00.408838] I [fuse-bridge.c:5260:fini] 0-fuse: Unmounting '/mnt/gluster'.
```

Reference
- https://github.com/rancher/convoy/blob/master/docs/glusterfs.md
- https://gluster.readthedocs.io/en/latest/Install-Guide/Quick_start/
