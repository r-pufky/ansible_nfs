# NFS
Manage NFS for ansible hosts.

## [Requirements][i]
Requires [r_pufky.deb][g] galaxy-ng collection.

## Role Variables
Detailed variable use documented in defaults. See usage for role operation. See
[reference documentation][h] for specific NFS configurations.

* [defaults][j] - User configurable options.

* [service][l] - NFS service options.

* [ports][k] - Ports are **not** managed (defined for external use).

## Usage

### Feature Flags
Tasks are gated by feature flags and executed in the following order.

  Step | Flag                      | Notes
 ------|---------------------------|-------
  1    | nfs_flg_legacy_services   | Enable legacy services.
  2    | nfs_flg_server            | Configure NFS server.
  3    | nfs_flg_client            | Configure NFS client.
  4    | nfs_flg_systemd_automount | Use systemd automounts for clients.

## Example Playbooks
Support only NFSv4 servers and clients.


Exported directories should be managed by other tasks before using this role;
ZFS filesystems should export NFS shares via ZFS (see
[r_pufky.deb.zfs](https://github.com/r-pufky/ansible_zfs)); these exports will
automatically be shared via the configured nfs-server. Role defaults to NFSv4.2
configuration.

A machine can simultaneously have both **nfs_flg_server** and
**nfs_flg_client** applied.

Configure NFSv4.2 server and export directories.
``` yaml
- name: 'Manage NFS'
  ansible.builtin.include_role:
    name: 'r_pufky.deb.nfs'
  vars:
  nfs_flg_server: true
  nfs_cfg_exports:
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
    name: 'r_pufky.deb.nfs'
  vars:
  nfs_flg_client: true
  nfs_cfg_mounts:
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
Configure [environment][a].

``` bash
# Run all tests.
molecule test --all
```

### [Releases][b]

  Release | Debian | Ansible | Notes
 ---------|--------|---------|-------
  3.x.x   | 13     | 2.20    | Ansible 2.20, feature flags, and semantic versioning.
  2.x.x   | 13     | 2.18    | Migrate to Debian Trixie.
  1.x.x   | 12     | 2.18    | Use standardized libraries.
  0.x.x   | 12     | 2.11    | Migration from private repository.

## Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License][c] | [direct link][f]

## Author Information
PGP: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9][d] | [github gist][e]


[a]: https://r-pufky.github.io/ansible_docs
[b]: https://semver.org/spec/v2.0.0
[c]: https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0
[d]: https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9
[e]: https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22

[f]: https://github.com/r-pufky/ansible_nfs/blob/main/LICENSE
[g]: https://github.com/r-pufky/ansible_collection_deb
[h]: http://r-pufky.github.io/docs/service/nfs
[i]: https://github.com/r-pufky/ansible_nfs/blob/main/meta/main.yml
[j]: https://github.com/r-pufky/ansible_nfs/tree/main/defaults/main/main.yml
[k]: https://github.com/r-pufky/ansible_nfs/blob/main/defaults/main/ports.yml
[l]: https://github.com/r-pufky/ansible_nfs/tree/main/vars/service.yml