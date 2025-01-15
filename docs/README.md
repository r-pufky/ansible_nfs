# NFS (Network File System)
The venerable king of network shares. Now in NFSv4 flavors.

Mounts remote exported filesystems locally.

[Server](server.md)

[Client](client.md)

[Options](options.md)

[Exports](exports.md)

[Mounts](mounts.md)

## Versions
NFSv4 removes a **lot** of cruft focusing on supporting single socket, local
file copies, state, authentication, encryption. Configuration is now in
`/etc/nfs.conf.d/` and actively migrated to this location if detected.

NFSv2 is **no** longer supported in Debian bookworm (12); and active migrations
to NFSv4 should be made.

Reference:
* https://orca.pet/nfs4debian/
* https://github.com/zilexa/Homeserver/tree/master/Filesystems-guide/networkshares_HowTo-NFSv4.2
* https://wiki.archlinux.org/title/NFS
* https://forum.proxmox.com/threads/nfsv4-server-disabled-under-pve-8.129803/
