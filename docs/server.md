# NFSv4.2 Server

Install NFS and disable RPC/sockets (not needed in NFSv4)
```bash
apt install nfs-kernel-server
systemctl stop nfs-server
systemctl list-dependencies {UNIT}  # confirm no other dependencies
systemctl mask rpcbind rpcbind.socket
systemctl stop rpcbind rpcbind.socket
systemctl mask rpc-statd rpc-statd-notify
systemctl stop rpc-statd rpc-statd-notify
```
For PVE clusters these services can be safely removed as well.

Reference:
* https://orca.pet/nfs4debian/
* https://www.suse.com/support/kb/doc/?id=000019530
* https://forum.proxmox.com/threads/rpcbind.142493/

Disable NFSv3 support on server
``` bash
systemctl edit nfs-server
```
``` diff
[Service]
ExecStart=
ExecStart=/usr/sbin/rpc.nfsd --no-nfs-version 3
```
* Version 2 is explicitly disabled in Debian bookworm, adding
  `--no-nfs-version 2` will cause the service to fail to start.
* `0644 root root` /etc/systemd/system/nfs-server.service.d/override.conf

Disable NFSv2 support on mounts
``` bash
systemctl edit nfs-mountd
```
``` diff
[Service]
ExecStart=
ExecStart=/usr/sbin/rpc.mountd --no-nfs-version 2 --no-nfs-version 3
```
* `0644 root root` /etc/systemd/system/nfs-mountd.service.d/override.conf

Enforce NFSv4.2 only and map restricted UID/GID's to server.

``` bash
cp /etc/nfs.conf /etc/nfs.conf.d/local.conf
```
`0644 root root` /etc/nfs.conf.d/local.conf
``` diff
-manage-gids=n
+manage-gids=y
-vers3=y
+vers3=n
-vers4=n
+vers4=y
-vers4.0=y
+vers4.0=n
-vers4.1=y
+vers4.1=n
-vers4.2=n
+vers4.2=y
```
* Major version must be enabled to enable minor versions.

Restart NFS server and confirm only 4.2 and port 2049 open
``` bash
systemctl restart nfs-server nfs-mountd
cat /proc/fs/nfsd/versions
> -3 +4 -4.0 -4.1 +4.2
ss -lutpn
> tcp  LISTEN  0  64  0.0.0.0:2049  0.0.0.0:*
> tcp  LISTEN  0  64     [::]:2049     [::]:*
```

Reference:
* https://orca.pet/nfs4debian/
* https://github.com/zilexa/Homeserver/tree/master/Filesystems-guide/networkshares_HowTo-NFSv4.2
* https://wiki.archlinux.org/title/NFS
* https://old.reddit.com/r/linux/comments/sb05kw/nfs_v42_and_serverside_copying_and_how_i_got_it/
* https://wiki.debian.org/NFSServerSetup
