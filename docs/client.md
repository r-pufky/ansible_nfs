# NFSv4.2 Client

Install NFS and disable RPC/sockets (not needed in NFSv4).
```bash
apt install nfs-common
systemctl list-dependencies {UNIT}  # confirm no other dependencies
systemctl mask rpcbind rpcbind.socket
systemctl stop rpcbind rpcbind.socket
systemctl mask rpc-statd rpc-statd-notify
systemctl stop rpc-statd rpc-statd-notify
```

Reference:
* https://orca.pet/nfs4debian/
* https://www.suse.com/support/kb/doc/?id=000019530
