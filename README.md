[![build status](https://github.com/alecunsolo/ansible-role-step/actions/workflows/ci.yml/badge.svg)](https://github.com/alecunsolo/ansible-role-step/actions/workflows/ci.yml)
[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit)](https://github.com/pre-commit/pre-commit)

Ansible Role: step
=========

Role to install [step](https://smallstep.com/cli/) and [step-ca](https://smallstep.com/certificates/) tools. Optionally, the role will bootstrap an internal CA using `step-ca`.

Requirements
------------

When bootstrapping the CA the role execute some tasks as a different user with `become`, so, `sudo` package must be installed. If not connecting to the ansible target as `root`, on Debian-like distros the package `acl` must be present.

Role Variables
--------------

```yaml
step_org: smallstep
step_cli_repo: '{{ step_org }}/cli'
step_ca_repo: '{{ step_org }}/certificates'
```
Github coordinates for the binaries.
```yaml
step_cli_version: <default null>
step_ca_version: <default null>
```
The versions of the tools. If not specified (default), or `lasest` the role will download the latest stable release.
```yaml
step_server_prepare: false
```
Whether or not to prepare the server CA. If this option is `true` the role will `prepare` the target to host a CA: it will create the dedicated system user and the systemd unit, but nothing else.
```yaml
step_server_user: step
step_server_conf_dir: /etc/step
step_server_systemd_unit: step-ca.service.j2
step_server_default_dir: /etc/default
step_server_pwd_file: '{{ step_server_conf_dir }}/password.txt'
```
These variables allow to customize the service user and the directories used for the CA.

```yaml
step_server_bootstrap: false
```
If this variable is set to true the role will bootstrap the CA.
```yaml
step_server_ca_password: <default null>
```
The password used to bootstrap the CA. If empty a random one will be generated. The password is stored in `step_server_pwd_file`. For more info about its usage see the [docs](https://smallstep.com/docs/step-cli/reference/ca/init/#options)
```yaml
step_server_ca_name: Automatic CA
step_server_ca_dns: [localhost]
step_server_ca_address: 0.0.0.0:443
step_server_ca_provisioner: bootstrap
```
Options to configure the bootstrap command. `step_server_ca_name` is the Common Name of the bootstrapped CA; `step_server_ca_dns` is a list of DNS names or IP addresses of the new CA. `step_server_ca_address` is the listening address of the new CA. `step_server_ca_provisioner` is the name of the first [provisioned](https://smallstep.com/docs/step-ca/provisioners/) created.

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- name: Converge
  hosts: all
  vars:
    step_server_prepare: true
    step_server_bootstrap: true
    step_server_additional_options: |
      # Test file
      UNUSED_STEP_OPTION=aaa
  roles:
    - role: alecunsolo.step
```

License
-------

MIT

Notes
-----

Testing with molecule is ~~stolen from~~ heavily inspired by [Jeff Geerling](https://www.jeffgeerling.com/). Watch [his video](https://youtu.be/FaXVZ60o8L8) (and the other ones as well).
