
## NFS Server Installation
- Installation
```
sudo apt-get install nfs-common nfs-kernel-server rpcbind
```
- Configuration
```
sudo mkdir /var/nfs
sudo chmod 777 /var/nfs

sudo vi /etc/exports
/var/nfs 127.0.0.1(rw,no_root_squash,sync)
/var/nfs 10.128.0.3(rw,no_root_squash,sync)
/var/nfs 10.128.0.4(rw,no_root_squash,sync)
/var/nfs 10.128.0.5(rw,no_root_squash,sync)
```
- Server Start
```
sudo /etc/init.d/nfs-kernel-server restart
sudo /etc/init.d/rpcbind restart
```

## NFS Client Installation
- Installation
```
sudo apt-get install nfs-common
```
- Mount
```
mkdir nfs
sudo mount -t nfs 10.128.0.3:/var/nfs nfs
sudo chown -R $USER.$USER nfs
```
- Use
```
touch nfs/hello.txt
```
