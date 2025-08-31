# sshconfig

An `Ansible` role for configuring `SSH` hosts.

## Prerequisites

Make sure the following software is installed on your system:

1. `ansible`

To install it (on `Ubuntu 24.04`, for example):

```shell
sudo apt update && sudo apt install -y \
  python3 \
  python3-pip

pip install --upgrade --user ansible
```

## Usage

```shell
git clone https://github.com/rezwyi/sshconfig.git
cd sshconfig
ansible-playbook main.yaml --extra-vars "@/path/to/the/file/with/extra/vars.yaml"
```

Example `extra-vars` file:

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
      - name: bar
        host_group_defaults: |
          IdentityFile ~/.ssh/foo
          User foobar
        hosts:
          - name: host-1
            config: |
              Hostname hostname-1
              Port 65321
          - name: host-2
            jump_host: host-1
            config: |
              Hostname hostname-2
              Port 65322
              User barbaz
```

This configuration will generate the following file at `~/.ssh/config.d/foo.conf`:

```txt
Host foo.bar.host-1
  ForwardAgent yes
  Hostname hostname-1
  IdentitiesOnly yes
  IdentityFile ~/.ssh/foo
  Port 65321
  PreferredAuthentications publickey
  StrictHostKeyChecking no
  User foobar
  UserKnownHostsFile /dev/null

Host foo.bar.host-2
  ProxyJump foo.bar.host-1
  ForwardAgent yes
  Hostname hostname-2
  IdentitiesOnly yes
  IdentityFile ~/.ssh/foo
  Port 65322
  PreferredAuthentications publickey
  StrictHostKeyChecking no
  User barbaz
  UserKnownHostsFile /dev/null
```

And then:

1. Run `ssh foo.bar.host-1` (for example) in your terminal.
1. Enjoy!
