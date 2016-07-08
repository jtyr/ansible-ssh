ssh
===

Ansible role which helps to configuration of SSH daemon and client
through a set of variables. It also supports creation of SSH baner.


Example
-------

```
---

# Example of how to use the role
- hosts: myhost
  vars:
    # Disable root login (PermitRootLogin = no)
    ssh_server_config__custom:
      PermitRootLogin: 'no'
  roles:
    - ssh
```


Role variables
--------------

List of variables used by the role:

```
```

The server and client configuration is using exact keys as they are in the
original configuration file. If the key is a list (e.g. `SendEnv`), multiple
lines with the key will be generated.

If the `ssh_banner` is set to non-zero lenght string, the file specified in the
`ssh_banner_location` will be created and populated with the string. It also
requires to set `Banner` key with the value of the `ssh_banner_location` variable
in the `sshd_config` variable.


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)


License
-------

MIT


Author
------

Jiri Tyr
