# voskamp.domoticz
This role installs Domoticz Home Automation System and support for OpenZWave can be enabled if needed. By Default the role will also install scripts to backup Domoticz. If Domoticz is not yet installed and /backup/domoticz/data/daily/latest/domoticz.db exists that file will be restored to the Domoticz directory.

## Recovering Domoticz
If your Domoticz server installed with this domoticz role crashes you can just install a new server, restore the /backup directory and run this domoticz role. The Domoticz database will restored as part of the installation process.

## Updating Domoticz
By the default the domoticz user created by this playbook does not have sudo rights. That means that upgrading Domoticz by using the upgrade button on the Domoticz website does not work. In fact the domoticz user can not even download the Domoticz install script. You can, instead, use Ansible to upgrade Domoticz. Just give the domoticz_upgrade variable the value true. For example:

```ansible-playbook -i inventory.yml --extra-vars 'domoticz_upgrade=true' playbook.yml```

This worked when upgrading to Domoticz 2022.1

## Requirements
This role has been tested on Debian 10 and 11 running on X86-64 and ARCH64.

## Role Variables
```yaml

# Wether or not to upgrade Domoticz version. This will (re)install the latest Domoticz
# version when Domoticz is already installed. This might the same version as is already
# installed. Only use when yo're experiencing technical problems or when a new Domoticz 
# version is available. Make sure you have a GOOD BACKUP when set to true.
domoticz_upgrade: false

# create backup jobs for Domoticz
domoticz_create_backup_jobs: true
domoticz_backup_app_software_dir: /backup/domoticz/software

domoticz_dir: "/home/{{ domoticz_user }}/domoticz"
domoticz_database_file: "{{ domoticz_dir }}/domoticz.db"
domoticz_database_file_backup: /backup/domoticz/data/daily/latest/domoticz.db
domoticz_install_script: https://raw.githubusercontent.com/domoticz/domoticz/master/scripts/install.sh

# The user which the Domoticz daemon runs as
domoticz_user: domoticz
# The primary group which the Domoticz daemon runs as
domoticz_group: "{{ domoticz_user }}"
domoticz_additional_groups:
  - dialout                    # Needed to access the USB serial port connected to the P1 Smart Meter

domoticz_what_to_backup:
  - "{{ domoticz_database_file }}"

# Enable/Disable http for Domoticz
domoticz_http: true
# The port for Domoticz to run http (-www daemon option). For ports <1024 root privileges are required, better to setup a reverse proxy with for example Nginx
domoticz_http_port: 8080
# Enable/Disable https for Domoticz
domoticz_https: true
# The port for Domoticz to run https (-sslwww daemon option). For ports <1024 root privileges are required, better to setup a reverse proxy with for example Nginx
domoticz_https_port: 8081
# Path to SSL certificate, if left default the server_cert.pem from Domoticz will be used (-sslcert daemon option)
domoticz_ssl_cert_file: "{{ domoticz_dir }}/server_cert.pem"

# Add support for ZWave
domoticz_zwave_support: true
# The version of Open-ZWave to be installed (accepts same arguments as version parameter of git module)
zwave_version: master
# The Open-ZWave git url
zwave_url: https://github.com/OpenZWave/open-zwave.git

# The P1 Smart Meter in Domoticz knows the total gas consumpution but does not record it for later usage. If 
# you want to record it you can create a dummy device in Domoticz and then create a virtual 'sensor'. The
# p1_meter_readings.sh script uploads data to that virtual 'sensor' using Mosquitto.
domoticz_p1_meter_readings_install: true
domoticz_p1_meter_readings_dest: "{{ domoticz_dir }}/myscripts"
domoticz_idx_smartmeter_gas: 9
domoticz_idx_meter_gas: 10
domoticz_server: 127.0.0.1
domoticz_mosquitto_server: "{{ domoticz_server }}"
domoticz_mosquitto_port: 1883

# The Sbfspot Solar device in Domoticz has some data which is not stored in the Domoticz database. We created a script 
# that reads that data and then stores it in custom sensors previously created in Domoticz (Not yet as part of this role). The value of # the *idx* parameters will probably be different for your Domoticz server!
domoticz_solar_meter_readings_install: true
domoticz_solar_meter_readings_dest: "{{ domoticz_dir }}/myscripts"
domoticz_idx_solar_meter: 2           # Get data from this Solar device
domoticz_idx_solar_meter_total: 20    # Set data for these Solar devices
domoticz_idx_solar_meter_today: 21
```

## Dependencies
* https://github.com/voskampm/ansible-role-application_backup

## Example Playbook
Install Domoticz with the default settings but without Zwave support
```yaml
- hosts: domoticz-servers

  tasks:
  - name: Ensure domoticz is installed
      include_role:
        name: voskampm.domoticz
      vars:
        domoticz_zwave_support: false
```
After running the playbook, Domoticz should be up and running and the webserver can be found at http://ipaddress:8080 and https://ipaddress:8081

## License
BSD-3-Clause-Clear

## Source code
* https://github.com/voskampm/ansible-role-domoticz

## Author Information
The original version of this role was created in 2017 by [Wilmar den Ouden](https://wilmardenouden.nl) and can be found at: https://github.com/wilmardo/ansible-role-domoticz. Michiel Voskamp rewrote the role in 2022.
