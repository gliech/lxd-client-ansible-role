# ['LXD Client Ansible Role'][1]

[![test & release][2]][3]

This Ansible role installs LXD and configures one or more other hosts from the
Ansible inventory as remotes.

## Requirements

None.

## Role Variables

<table>
<tr><th>Name</th><th>Required</th><th>Type / Choices</th><th>Description</th></tr>
<tr><td><code>lxd_client_remotes</code></td>
<td>yes</td>
<td>list(string)</td>
<td>

List of Ansible inventory hostnames of machines that should be configured as LXD
remotes. The ansible user on these machines should be a member of the lxd group
so that it can configure client certificates on the server.

**Example:** `"{{ groups.lxd_servers }}"`
</td></tr>


<tr><td><code>lxd_client_default_remote</code></td>
<td>no</td>
<td>string</td>
<td>

Name of an LXD remote that will be configured as default-remote in the lxc
client config. Defaults to the first entry in the `lxd_client_remotes` variable.

**Default:** `lxd_client_remotes[0]`
</td></tr>


<tr><td><code>lxd_client_users</code></td>
<td>no</td>
<td>list(string)</td>
<td>

List of OS users, on the client machines which should be configured to access
the LXD remotes. If no list is supplied, only the Ansible user will be set up to
reach the remotes.

**Default:** `["{{ ansible_user_id }}"]`
</td></tr>
</table>

## Dependencies

None.

## Example Playbook

```yaml
- hosts: lxd_servers
  tasks:
    - name: Configure LXD servers
      ansible.builtin.import_role:
        name: gliech.lxd
      vars:
        lxd_config:
          config: {}
          networks: []
          storage_pools:
            - config:
                source: /var/lib/lxd/storage-pools/default
              description: ""
              name: default
              driver: dir
          profiles:
            - config:
                security.privileged: "true"
              description: Default LXD profile
              devices:
                root:
                  path: /
                  pool: default
                  type: disk
              name: default
          projects:
            - config:
                features.images: "true"
                features.networks: "true"
                features.networks.zones: "true"
                features.profiles: "true"
                features.storage.buckets: "true"
                features.storage.volumes: "true"
              description: Default LXD project
              name: default

- hosts: localhost
  tasks:
    - name: Configure LXD client
      ansible.builtin.import_role:
        name: gliech.lxd_client
      vars:
        lxd_client_remotes: "{{ groups.lxd_servers }}"
```

## License

This project is licensed under the terms of the [GNU General Public License v3.0](LICENSE)

[1]: https://galaxy.ansible.com/ui/standalone/roles/gliech/lxd_client/
[2]: https://github.com/gliech/lxd-client-ansible-role/actions/workflows/release.yml/badge.svg
[3]: https://github.com/gliech/lxd-client-ansible-role/actions/workflows/release.yml
[4]: https://github.com/gliech/semantic-release-config-github-ansible-role
