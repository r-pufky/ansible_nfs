# NFS
Manage NFS for ansible hosts.

## Requirements
[supported platforms](https://github.com/r-pufky/ansible_nfs/blob/main/meta/main.yml)

## Role Variables
[defaults](https://github.com/r-pufky/ansible_nfs/tree/main/defaults)

## Ports
All ports and protocols have been defined for the role.

[defaults/ports.yml](https://github.com/r-pufky/ansible_nfs/blob/main/defaults/main/ports.yml)

## Dependencies
Part of the [r_pufky.srv](https://github.com/r-pufky/ansible_collection_srv)
collection.

## Example Playbook
Support only NFSv4 servers and clients.

Exported directories should be managed by other tasks before using this role;
ZFS filesystems should export NFS shares via ZFS (see [r_pufky.srv.zfs](https://github.com/r-pufky/ansible_zfs));
these exports will automatically be shared via the configured nfs-server. Role
defaults to NFSv4.2 configuration.

A machine can simultaneously have both `nfs_server` and `nfs_client` applied.

Configure NFSv4.2 server and export directories.
``` yaml
- name: 'Manage NFS'
  ansible.builtin.include_role:
    name: 'r_pufky.srv.nfs'
  vars:
  nfs_server: true
  nfs_exports:
    - source: '/d/pictures'
      hosts:
        - host: '10.2.2.80'
          options:
            - 'rw'
            - 'sync'
            - 'fsid=1'
            - 'all_squash'
            - 'no_subtree_check'
            - 'anonuid=1000'
            - 'anongid=1000'
        - host: '10.4.4.0/24'
          options:
            - 'rw'
            - 'sync'
            - 'fsid=1'
            - 'all_squash'
            - 'no_subtree_check'
            - 'anonuid=1000'
            - 'anongid=1000'
```

Configure NFSv4.2 client.
``` yaml
- name: 'Manage NFS'
  ansible.builtin.include_role:
    name: 'r_pufky.srv.nfs'
  vars:
  nfs_client: true
  nfs_mounts:
    - server: 'example.com'
      export: '/data/pictures'
      mount: '/data/pictures'
      options:
        - 'nfsvers=4'
        - 'minorversion=2'
        - 'proto=tcp'
        - 'fsc'
      timeout: 30
```

## Development
Configure [environment](https://github.com/r-pufky/ansible_collection_srv/blob/main/docs/dev/environment/README.md)

Run all unit tests:
``` bash
molecule test --all
```

### Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License](https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0)
 [(direct link)](https://github.com/r-pufky/ansible_nfs/blob/main/LICENSE)

## Author Information
PGP Fingerprint: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9](https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9)
| [github gist](https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22)
