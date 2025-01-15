# NFSv4.2 Client Mounts

[Mount Options](options.md)

## Systemd Automount
Preferred as these will automatically mount when needed and unmount after idle.

The mount unit defines how to mount the remote filesystem, automount unit
triggers the mount unit.

Unit names **must** match `where` using only valid systemd unit characters;
typically just replacing `/` with `-`, and dropping root: `/mnt/path`:
`mnt-path`.

/etc/systemd/system/mnt-client_test.mount
``` ini
[Unit]
Description=NFS mount 127.0.0.1:/home/vagrant

[Mount]
What=127.0.0.1:/home/vagrant
Where=/mnt/client_test
Type=nfs
Options=nfsvers=4,minorversion=2,proto=tcp,fsc,noauto,nofail
TimeoutSec=30

[Install]
WantedBy=multi-user.target
```

/etc/systemd/system/mnt-client_test.automount
``` ini
[Unit]
Description=Automount NFS 127.0.0.1:/home/vagrant

[Automount]
Where=/mnt/client_test

[Install]
WantedBy=multi-user.target
```

These units only need to be enabled.
``` bash
systemctl daemon-reload
sysctmctl enable mnt-client_test.mount mnt-client_test.automount
```

Reference:
* https://wiki.archlinux.org/title/NFS
* https://www.freedesktop.org/software/systemd/man/latest/systemd.unit.html

## NFS Client (fstab)
Traditional NFS share mounting.

The root directory must existing for NFS mounts; options may be tested with
manual NFS mount before adding to fstab.
``` bash
mkdir /autofs/{EXPORT}  # repeat for all mounts
mount -t nfs -o nfsvers=4,minorversion=2,proto=tcp,fsc,nocto 10.10.10.8:/home/vagrant /autofs/home/vagrant
```
* `0755 root root` for unmounted directories; directory inherits permissions of
  the export.

`0644 root root` /etc/fstab
```bash
10.10.10.8:/home/vagrant /autofs/home/vagrant nfs4 rw,nfsvers=4,minorversion=2,proto=tcp,fsc,nocto,_netdev 0 0
```
* `nfs4` enables automounting.
* `_netdev` specifies it is a network device and forces fstab to delay mounting
  until after required networks are up.

Force reload fstab and mount all nfs exports
```bash
systemctl daemon-reload
mount -a
```

[Reference](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html)

## Troubleshooting

### Mounting NFS shares in LXC containers
Mounting NFS shares require elevated privileges. A better pattern is to mount
the NFS shares on the cluster node and map the mount point directly in the
container. These should be mapped with minimum permissions.

On the proxmox node mount NFS shares using [fstab](#nfs-client-fstab) instead
of the WebGUI. Mounts created using the WebGUI are only mounted on first use,
which generally leads to container mounting failures due to timeouts on the PVE
host.

`0644 root root` /etc/fstab
``` bash
10.10.10.8:/d/backup /autofs/backup nfs4 ro,nfsvers=4,minorversion=2,proto=tcp,fsc,nocto,_netdev 0 0
```

Add a standard container mountpoint directed at the NFS mount:
``` ini
mp0: volume=/autofs/backup,mp=/data/backup,ro=1
```

[Reference](https://forum.proxmox.com/threads/tutorial-mounting-nfs-share-to-an-unprivileged-lxc.138506/)

Reference:
* https://github.com/zilexa/Homeserver/tree/master/Filesystems-guide/networkshares_HowTo-NFSv4.2
