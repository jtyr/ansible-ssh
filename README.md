ssh
===

Ansible role which helps to configuration of SSH daemon and client
through a set of variables. It also supports creation of SSH baner.

The configuration of the role is done in such way that it should not be necessary
to change the role for any kind of configuration. All can be done either by
changing role parameters or by declaring completely new configuration as a
variable. That makes this role absolutely universal. See the examples below for
more details.

Please report any issues or send PR.


Example
-------

```yaml
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

```yaml
# Name of the SSH service
ssh_service: sshd

# Packages to be installed (exact version can be specified here)
ssh_packages_redhat:
  - openssh-server
  - openssh-clients

ssh_packages_debian:
  - openssh-server
  - openssh-clients

ssh_packages: >
  {{ ssh_packages_redhat if ansible_os_family == 'RedHat' else ssh_packages_debian }}

# SSH banner location
ssh_banner_location: /etc/ssh/banner

# SSH banner content
ssh_banner: ''

# Location of the SSH client config file
ssh_client_config_location: /etc/ssh/ssh_config

# Location of the SSH server config file
ssh_server_config_location: /etc/ssh/sshd_config


# Values of default options for all hosts
ssh_client_config_all_forward_x11_trusted: 'yes'
ssh_client_config_all_gssapi_authentication: 'yes'
ssh_client_config_all_protocol: 2
ssh_client_config_all_send_env: >
  LANG LC_ADDRESS LC_ALL LC_COLLATE LC_CTYPE LC_IDENTIFICATION LC_MEASUREMENT
  LC_MESSAGES LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE LC_TIME

# Default options for all hosts
ssh_client_config_all__default:
  ForwardX11Trusted: "{{ ssh_client_config_all_forward_x11_trusted }}"
  GSSAPIAuthentication: "{{ ssh_client_config_all_gssapi_authentication }}"
  Protocol: "{{ ssh_client_config_all_protocol }}"
  SendEnv: "{{ ssh_client_config_all_send_env }}"

# Custom options for all hosts
ssh_client_config_all__custom: {}

# Default client config
ssh_client_config__default:
  '*': "{{
    ssh_client_config_all__default | combine(
    ssh_client_config_all__default) }}"

# Custom client config
ssh_client_config__custom: {}

# Final client config
ssh_client_config: "{{
  ssh_client_config__default | combine(
  ssh_client_config__custom) }}"


# Default values of the server config options
ssh_server_config_challenge_response_authentication: 'no'
ssh_server_config_gssapi_authentication: 'yes'
ssh_server_config_gssapi_cleanup_credentials: 'yes'
ssh_server_config_password_authentication: 'yes'
ssh_server_config_protocol: 2
ssh_server_config_subsystem: sftp /usr/libexec/openssh/sftp-server
ssh_server_config_syslog_facility: AUTHPRIV
ssh_server_config_use_pam: 'yes'

# Default server config
ssh_server_config__default:
  ChallengeResponseAuthentication: "{{ ssh_server_config_challenge_response_authentication }}"
  GSSAPIAuthentication: "{{ ssh_server_config_gssapi_authentication }}"
  GSSAPICleanupCredentials: "{{ ssh_server_config_gssapi_cleanup_credentials }}"
  PasswordAuthentication: "{{ ssh_server_config_password_authentication }}"
  Protocol: "{{ ssh_server_config_protocol }}"
  Subsystem: "{{ ssh_server_config_subsystem }}"
  SyslogFacility: "{{ ssh_server_config_syslog_facility }}"
  UsePAM: "{{ ssh_server_config_use_pam }}"

# Custom server config
ssh_server_config__custom: {}

# Final server config
ssh_server_config: "{{
  ssh_server_config__default | combine(
  ssh_server_config__custom) }}"
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
