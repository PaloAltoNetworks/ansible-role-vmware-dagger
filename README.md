vmware-dagger
=========
An Ansible role that gathers virtual machine IP addresses from vCenter and registers them in PAN-OS Dynamic Address Groups based on an associated VMware tag.
 
Requirements
------------
This role utilizes the Python libraries listed below.  All are available via [PyPI](https://pypi.org) and may be installed using the `pip` installer.  The use of `virtualenv` is recommended in order to avoid system library conflicts.

- [pyvomi](https://pypi.org/project/pyvmomi/)
- [pan-python](https://pypi.org/project/pan-python/)
- [pandevice](https://pypi.org/project/pandevice/)
- [requests](https://pypi.org/project/requests/)

In addition, the [vSphere Automation SDK](https://github.com/vmware/vsphere-automation-sdk-python) is required for dynamic inventory discovery with VMware tag support.  This SDK may be installed as follows:

```
$ git clone https://github.com/vmware/vsphere-automation-sdk-python.git
$ cd vsphere-automation-sdk-python
$ pip install --upgrade --force-reinstall -r requirements.txt --extra-index-url file:///<absolute_path_to_sdk>/lib
```

Role Variables
--------------
The required variables are listed below, along with default values (see defaults/main.yml):

```
# VMware variables
vmware_tag: 
vmware_datacenter: 
vmware_validate_certs: False

# PAN-OS variables
panos_address:
panos_username: 
panos_password:
panos_api_key:
```

Dependencies
------------
None

Example Playbook
----------------
```
---
- name: Register tagged vCenter virtual machines in PAN-OS DAGs
  hosts: '{{ vmware_tag }}'
  connection: local

  roles:
  - stealthllama.vmware-dagger
```

Dynamic Inventory
-----------------
This role leverages the [vmware_vm_inventory](https://docs.ansible.com/ansible/latest/plugins/inventory/vmware_vm_inventory.html) Dynamic Inventory plugin to inventory vSphere virtual machines and group them by their tag values.

The [vmware_vm_inventory](https://docs.ansible.com/ansible/latest/plugins/inventory/vmware_vm_inventory.html) plugin utilizes the following environment variables 

```
$ export VMWARE_SERVER="<vcenter hostname or ip address>"
$ export VMWARE_USERNAME="<vcenter username>"
$ export VMWARE_PASSWORD="<vcenter password>"
```

A plugin configuration file called `vmware.yml` is required and should contain the following:

```
---
plugin: vmware_vm_inventory
validate_certs: False
with_tags: True
```

The Dynamic Inventory plugin can be tested using the following command: `ansible-inventory -i vmware.yml --graph`

Usage
-----
The playbook requires a number of variables to run successfully. These variables may be defined in a separate YAML file, provided on the command line with the `--extra-vars` flag, or provided via the Ansible Tower API.

*Variables file*
```
$ ansible-playbook -i vmware.yml myplaybook.yml --extra-vars=@myvars.yml
```

*Command line*
```
$ ansible-playbook -i vmware.yml myplaybook.yml --extra-vars='{"vm_tag":"MyTag","vmware_datacenter":"MyLab", \
"panos_address":"10.0.0.1","panos_username":"admin","panos_password":"s3cr3tp@ssw0rd"}'
```

License
-------
Apache 2.0

Author Information
------------------
Role created by Robert Hagen ([@stealthllama](https://github.com/stealthllama)).
