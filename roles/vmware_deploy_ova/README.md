Role Name
=========

This role can be used to import an OVA/OVF template to a target VMware vCenter Server or VMware ESXi host.

Requirements
------------

- python >= 2.6
- PyVmomi

Role Variables
--------------

## Default variables that have been defined in defaults/main.yml

  \# Set where the OVA file should be sourced.
  \# Current options are 'local' and 'http'.
  \# If local is set, then the ova_file will be sourced from the defined ova_path.
  \# If http is set, then the ova_file will be downloaded to the defined ova_path.
  \# For http, the ova_url also needs to be set.
  ```ova_source: "local"```

  \# Whether or not certificate verification should be enabled against
  \# the target host the OVA is being imported to.
  ova_validate_certs: no

  \# Whether or not duplicates of the same name are allowed.
  ova_allow_duplicates: no

  \# Whether or not the appliance should be powered on once the OVA
  \# import process has completed.
  ova_power_on_after_deploy: yes

  \# Whether or not the module should wait for the IP address to become
  \# available in vCenter after powering on the OVA.
  ova_wait_for_ip_address: no

  \# The default disk format to use for the imported OVA's disks.
  ova_deployment_disk_type: thin

## The following parameters need to be provided, as extra vars, group_vars or host_vars:

  \# Set the OVA deployment variables.
  ova_deployment_hostname: "vcenter/esxi hostname"
  ova_deployment_username: "vcenter/esxi username"
  ova_deployment_password: "vcenter/esxi password"

  \# Datastore clusters are not supported by the module.
  ova_deployment_datastore: "datastore"

  \# The following are only required when deploying to vCenter Server.
  ova_deployment_datacenter: "vcenter datacenter"
  ova_deployment_cluster: "vcenter cluster"
  ova_deployment_folder: "vcenter folder"

## The following mandatory global variables need to be set:

  \# The OVA file name.
  ova_file: "ova_file.ova"

  \# The local path to the OVA file (do not use a leading /).
  ova_path: "/path/to/ova_file"

## The following optional global variables can be set:

  \# The URL to the OVA file if source is set to 'http' (do not use a leading /).
  ova_url: "http[s]://example.com/ovas"

## The following mandatory variables need to be set in roles that use this role:

  \# A key:value for the network property, for example:
  \# ova_networks:
  \#   "Network 1": "label"
  ova_networks:
    "key":"value"

  \# A dictionary of key: value pairs. for example:
  \# ova_properties:
  \#   "guestinfo.cis.appliance.net.addr.family": "ipv4"
  \#   "guestinfo.cis.appliance.net.mode": "static"
  ova_properties:
    "key": "value"
    "key": "value"

## The following optional variables can be set in roles that use this role:

  \# A string containing the deployment option
  ova_deployment_option: "option"

  \# A list of dictionaries for the vapp properties, with userConfigurable set to true, for example:
  \# vapp_properties:
  \#   - id: guestinfo.cis.deployment.node.type
  \#     type: string
  \#     value: "embedded"
  \#     userConfigurable: true
  \#   - id: guestinfo.cis.appliance.ssh.enabled
  \#     type: boolean
  \#     value: "true"
  \#     userConfigurable: true
  vapp_properties: 
    - list of dict1
    - list of dict2


Example Playbook
----------------

This role is designed to be used as a dependency for parent roles.