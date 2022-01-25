# wilmardo.domoticz

[![Build Status](https://travis-ci.org/wilmardo/ansible-role-domoticz.svg?branch=master)](https://travis-ci.org/wilmardo/ansible-role-domoticz)
[![Galaxy](https://img.shields.io/badge/galaxy-wilmardo.domoticz-blue.svg)](https://galaxy.ansible.com/wilmardo/domoticz/)

This role installs Domoticz Home Automation System and support for OpenZWave can be enabled.


# ToDo

This a work in progress!

* Add tests!
* An empty domoticz.db database is created when installing a new Domoticz server.
* zwave is not yet idempotent and fails on seconde converge run.
```
TASK [voskampm.domoticz : Clone Open-ZWave repo] *******************************
fatal: [debian10]: FAILED! => {"before": "f150a985de47ddcaca5fdba97bda71688c4a4069", "changed": false, "msg": "Local modifications exist in repository (force=no)."}
fatal: [debian11]: FAILED! => {"before": "f150a985de47ddcaca5fdba97bda71688c4a4069", "changed": false, "msg": "Local modifications exist in repository (force=no)."}
```
## Requirements

None.

## Role Variables

### Default usage

As default Domoticz is installed and running http on port 8080 and https on port 8081 with the default certificate. 
If you want to adapt this to your needs look at the [Advanced usage](#advanced-usage) section.

### Advanced usage

For more advanced usage the following variables are available:
```yaml
# The directory where the Domoticz will be installed
domoticz_dir: /home/domoticz/domoticz

# The location of the Domoticz install script.
domoticz_install_script: https://raw.githubusercontent.com/domoticz/domoticz/master/scripts/install.sh

# The user which the Domoticz daemon runs as
domoticz_user: domoticz
# The group which the Domoticz daemon runs as
domoticz_group: domoticz
# The port for Domoticz to run http (-www daemon option). For ports <1024 root privileges are required, better to setup a reverse proxy with for example Nginx
domoticz_http_port: 8080
# Enable/Disable https for Domoticz
domoticz_https: true
# The port for Domoticz to run https (-sslwww daemon option). For ports <1024 root privileges are required, better to setup a reverse proxy with for example Nginx
domoticz_https_port: 8081
# Path to SSL certificate, if left default the server_cert.pem from Domoticz will be used (-sslcert daemon option)
domoticz_ssl_cert: "{{ domoticz_dir }}/server_cert.pem"

# Add support for ZWave
domoticz_zwave_support: true
# The version of Open-ZWave to be installed (accepts same arguments as version parameter of git module)
zwave_version: master
# The Open-ZWave git url
zwave_url: https://github.com/OpenZWave/open-zwave.git
```

## Dependencies

None but for Domoticz on Centos 6, Python 3.3 out of the [Software Collections](https://www.softwarecollections.org/en/scls/rhscl/python33/) is installed

## Example Playbook

Install Domoticz with the default settings
```yaml
- hosts: domoticz-servers
  roles:
     - { role: wilmardo.domoticz }
```
After running the playbook, Domoticz can be found for HTTP at http://ipaddress:8080 and HTTPS at https://ipaddress:8081

## License

BSD-3-Clause-Clear

## Author Information

This role was created in 2017 by [Wilmar den Ouden](https://wilmardenouden.nl).
