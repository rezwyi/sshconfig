# sshconfig

Ansible role for configuring ssh hosts.

## Prerequisites

You need to have the following software installed on your system:

1. `ansible`

To install do the following (e.g. Ubuntu):

```shell
sudo apt update && sudo apt install -y \
  python3 \
  python3-pip

pip install --upgrade --user ansible
```

## Run

```shell
git clone https://github.com/rezwyi/sshconfig.git
cd sshconfig
ansible-playbook main.yaml --extra-vars "@<path-to-file-with-extra-vars>.yaml"
```

Example of extra vars file:

```yaml
---
sshconfig_conf_dir: ~/.ssh/config.d

sshconfig_orgs:
  - name: foo
    org_defaults: |
      ForwardAgent yes
      IdentitiesOnly yes
      PreferredAuthentications publickey
      StrictHostKeyChecking no
      UserKnownHostsFile /dev/null
    host_groups:
      - name: ofd
        host_group_defaults: |
          IdentityFile ~/.ssh/foo
          User foobar
        hosts:
          - name: host-1
            config: |
              Hostname foo.bar.baz
              Port 65321
          - name: host-2
            jump_host: host-1
            config: |
              Hostname localhost
              Port 65322
```
