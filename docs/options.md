# NFS Options
These are the most used NFS options.

See [man page](https://man7.org/linux/man-pages/man5/nfs.5.html) for all
options.

* `all_squash[no_all_squash]`: map all UID/GIDs to anonymous user; this is
  `nobody` for debian.
* `anonuid`: anonymous user id
* `anongid`: anonymous group id
* `async`: violate NFS protocol and reply before changes committed (**unsafe**)
* `sync`: reply after changes are committed to disk (**safe**)
* `crossmnt`: enable traversing of directories below exported root
* `fsc`: use filesystem cache. NFS will not use cache unless explicitly set.
* `fsid`: `root` or `0` has special meaning in which all other exports are
  mounted underneath it. Root should only be used if the filesystem does not
  have a `UUID`. Modern systems shouldn't use this.
* `mountpoint` only export if successfully mounted first (ZFS only)
* `no_subtree_check`: disable checking if file is in exported subtree (slow)
  this is disabled by default.
* `nohide`: do not hide sub-exports on same filesystems
* `rw`: read/write
* `ro`: readonly
* `root_squash[no_root_squash]`: map UID/GID 0 to anonymous user; this is
  `nobody` for debian.
* `sec`: NFS connection authentication method.
  * `sys` - use local UIDs/GIDs
  * `krb5` - kerberos authentication only
  * `krb5i` - kerberos authentication and secure checksums
  * `krb5p` - kerberos authentication, secure checksums, NFS traffic encryption
* `nocto`: lose-to-open consistency - client trusts freshness of its view of
  the file and directory until cache attribute timers (`actimo`) elapses.
  Improves performance for read-only mounts only if data changes occasionally.
  Reduces getattr access calls **~65-70%**.
* `cto`: close-to-open consistency (default) - ensures that on open the most
  recent data for a file is always presented to the application.
* `actimo`: Sets `acregmin`, `acregmax`, `acdirmin`, `acdirmax`; `actimo=600`
  additionally reduces getattr access calls **~20-25%** with `nocto`.

Decision: `async` increases write speed only - do not use for RO mounts as it
    will have no effect. [Reference](https://hardforum.com/threads/nfs-and-async.1893929/).

Reference:
* https://man7.org/linux/man-pages/man5/nfs.5.html
* https://www.thegeekdiary.com/common-nfs-mount-options-in-linux/
* https://learn.microsoft.com/en-us/azure/azure-netapp-files/performance-linux-mount-options
* https://www.admin-magazine.com/HPC/Articles/Useful-NFS-Options-for-Tuning-and-Management
* https://wiki.archlinux.org/title/NFS
* https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/4/html/reference_guide/s2-nfs-client-config-options#s2-nfs-client-config-options
* https://unix.stackexchange.com/questions/427597/implications-of-using-nfsv4-fsid-0-and-exporting-the-nfs-root-to-entire-lan-or
