Role Name
=========

This role can be used to import an OVA/OVF template to a target VMware vCenter Server or VMware ESXi host.

Requirements
------------

- python >= 2.6
- PyVmomi

Role Variables
--------------

### Default variables that have been defined in defaults/main.yml

  Set where the OVA file should be sourced.

  Current options are '**local**' and '**http**'.

  If **local** is set, then the **ova_file** will be sourced from the defined **ova_path**.

  If **http** is set, then the **ova_file** will be downloaded to the defined **ova_path**.

  For **http**, the **ova_url** also needs to be set.
  ```
  ova_source: "local"
  ```

  Whether or not certificate verification should be enabled against the target host the OVA is being imported to.
  ```
  ova_validate_certs: no
  ```

  Whether or not duplicates of the same name are allowed.
  ```
  ova_allow_duplicates: no
  ```

  Whether or not the appliance should be powered on once the OVA import process has completed.
  ```
  ova_power_on_after_deploy: yes
  ```

  Whether or not the module should wait for the IP address to become available in vCenter after powering on the OVA.
  ```
  ova_wait_for_ip_address: no
  ```

  The default disk format to use for the imported OVA's disks.
  ```
  ova_deployment_disk_type: thin
  ```

### The following parameters need to be provided, as extra vars, group_vars or host_vars:

  Set the OVA deployment variables.
  ```
  ova_deployment_hostname: "vcenter/esxi hostname"
  ova_deployment_username: "vcenter/esxi username"
  ova_deployment_password: "vcenter/esxi password"
  ```

  Set the target datastore. Datastore clusters are not supported by the module.
  ```
  ova_deployment_datastore: "datastore"
  ```

  The following are only required when deploying to vCenter Server.
  ```
  ova_deployment_datacenter: "vcenter datacenter"
  ova_deployment_cluster: "vcenter cluster"
  ova_deployment_folder: "vcenter folder"
  ```

### The following mandatory global variables need to be set:

  Set the OVA file name.
  ```
  ova_file: "ova_file.ova"
  ```

  Set the local path to the OVA file (do not use a leading /).
  ```
  ova_path: "/path/to/ova_file"
  ```

### The following optional global variables can be set:

  Set the URL to the OVA file if source is set to 'http' (do not use a leading /).
  ```
  ova_url: "http[s]://example.com/ovas"
  ```

### The following mandatory variables need to be set in roles that use this role:

  A key:value pair for the network property.
  ```
  ova_networks:
    "key":"value"
  ```
  Example:
  ```
  ova_networks:
    "Network 1": "label"
  ```

  The OVA Properties as set of dictionary key: value pairs.
  ```
  ova_properties:
    "key": "value"
    "key": "value"
  ```
  Example:
  ```
  ova_properties:
    "guestinfo.cis.appliance.net.addr.family": "ipv4"
    "guestinfo.cis.appliance.net.mode": "static"
  ```

### The following optional variables can be set in roles that use this role:

  A string containing the deployment option.
  ```
  ova_deployment_option: "option"
  ```

  The vApp Properties as list of dictionaries with userConfigurable set to true.
  ```
  vapp_properties: 
    - list of dict1
    - list of dict2
  ```
  Example:
  ```
  vapp_properties:
    - id: guestinfo.cis.deployment.node.type
      type: string
      value: "embedded"
      userConfigurable: true
    - id: guestinfo.cis.appliance.ssh.enabled
      type: boolean
      value: "true"
      userConfigurable: true
  ```

Example Playbook
----------------

This role is designed to be used as a dependency for parent roles.