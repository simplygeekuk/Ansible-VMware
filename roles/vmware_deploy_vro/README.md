vmware_deploy_vro
=========

Deploys the vRealize Orchestrator Appliance from OVA and allows for the following configuration:
- Hostname (appliance hostname or VIP)
- SSL Certificate
- Authentication Provider (vSphere or vRealize Automation)
- Log Rotation Settings and Log Integration (LogInsight)
- NTP
- Install Plugins
- Import Packages
- Add Plugin Endpoints (SOAP, REST, vRA, vCenter, PowerShell, vAPI)

Supported vRealize Orchestrator Appliances
------------

- vRealize Orchestrator 7.5
- vRealize Orchestrator 7.6

Requirements
------------

- python >= 2.6
- PyVmomi

Role Variables
--------------

### Default variables that have been defined in defaults/main.yml

These variables can be overridden using extra vars or by copying the variable to a host_vars or group_vars file and changing to the desired value.

Default list of NTP Servers that will be used.
```
ntp_servers:
  - "0.pool.ntp.org"
  - "1.pool.ntp.org"
  - "2.pool.ntp.org"
  - "3.pool.ntp.org"
```

HTTP REST API Variables.
```
http_content_type: "application/json"
http_Accept: "application/json"
http_validate_certs: no
http_body_format: "json"
```

Set the ports used by the vco and vco-controlcenter APIs.
```
vro_api_port: 8281
vro_cc_api_port: 8283
```

Enable SSH access to the appliance.
```
vro_enable_ssh: "True"
```

Enable Customer Experience Improvement Program.
```
vro_enable_telemetry: "False"
```

Set to '**yes**' to default to using self-signed certificates otherwise provide the certificate file in PEM format using the host name, as it has been defined in the hosts file (fqdn), with the extension .pem.
```
vro_use_selfsigned_certificate: no
```

Set to enable or disable if packages should be installed. This will have a dependency on an authentication provider being configured and the API user a member of the adminGroup.
```
vro_install_packages: yes
```

If a package version is already installed, set to '**yes**' to force the package to be installed.
```
vro_force_install_packages: no
```

Set the source location of where packages can be found.

Available options are '**local**'.

If **local** is set, then packages should be placed in the '**files/packages**' folder in the '**vmware_deploy_vro**' role.
```
vro_package_source: "local"
```

Set to enable or disable if plugin endpoints should be configured. This will have a dependency on an authentication provider being configuredand the API user a member of the adminGroup.
```
vro_configure_plugin_endpoints: yes
```

If a plugin is already installed, set to '**yes**' to force the plugin to be installed.
```
vro_force_plugin_install: no
```

Set if you would like to ignore any errors when adding plugin endpoints. This can be useful if re-running the playbook fails due to duplicate endpoints.
```
vro_ignore_plugin_errors: no
```

Default log rotation parameters.

Valid log levels are: '', '**ALL**', '**TRACE**', '**DEBUG**', '**INFO**', '**WARN**', '**ERROR**', '**FATAL**', '**OFF**'
```
vro_logging_globalLevel: "INFO"
vro_logging_maxFileCount: 10
vro_logging_maxFileSizeMb: 5
vro_logging_scriptingLevel: "INFO"
```

### The following parameters need to be defined in host_vars:

Set Network Configuration for the appliance.
```
ova_network_label: "VM Network"
ova_network_ip_address: "x.x.x.x"
ova_network_mask: "x.x.x.x"
ova_network_gateway: "x.x.x.x"
```

Set the root password for this appliance, or leave commented out to use the default.
```
vro_root_password: "VMwar3!!"
```

Set the Ansible connection variables (use exactly as shown)
```
ansible_user: "{{ vro_root_username }}"
ansible_password: "{{ vro_root_password }}"
ansible_host: "{{ ova_network_ip_address }}"
```

### The following mandatory parameters need to be defined, as extra vars, or in group_vars or host_vars:

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

The following are only required when deploying to vCenter Server. If folder is not defined then the appliance will deploy to the default folder.
```
ova_deployment_datacenter: "vcenter datacenter"
ova_deployment_cluster: "vcenter cluster"
ova_deployment_folder: "vcenter folder"
```

Set the DNS domain that should be used.
```
dns_domain: "example.com"
```

Provide a list of available DNS Servers.
```
dns_servers:
  - "x.x.x.x"
  - "x.x.x.x"
```

Set the OVA file name.
```
ova_file: "ova_file.ova"
```

Set the local path to the OVA file (do not use a leading /).
```
ova_path: "/path/to/ova_file"
```

#### Configure Authentication

Set the auth provider to use for vRO authentication.

Auth provider is one of '**CAFE**' or '**VSPHERE**'.
```
vro_auth_provider: VSPHERE
vro_auth_hostname: host.example.com
vro_adminGroup: "vro-admins"
vro_adminGroupDomain: "{{ dns_domain }}"
vro_auth_default_tenant: "vsphere.local"
```

### The following optional parameters can be defined, as extra vars, or in group_vars or host_vars:

Set the URL to the OVA file if source is set to '**http**' (do not use a leading /).
```
ova_url: "http[s]://example.com/ovas"
```

Set VIP hostname if using a load balancer. This will default to using the inventory hostname.
```
vro_vip_hostname: "vro.example.com"
```

Provide the VMware LogInsight server details to send system logs to.

Valid protocol options are '**syslog**' and '**cfapi**'.
```
loginsight_server:
  host: "loginsight.example.com"
  port: 514
  protocol: syslog
```

Provide a list of plugins that should be installed. The plugin packages should be placed in the '**files/plugins**' folder for the '**vmware_deploy_vro**' role.
```
vro_plugins:
  - plugin1.dar
  - plugin2.vmoapp
```

Provide a list of packages that should be installed. The packages should be placed in the '**files/packages**' folder for the '**vmware_deploy_vro**' role.
```
vro_packages:
 - package1.package
 - package2.package
 ```

#### Configure Plugin Endpoints

Add vCenter Server Endpoints. If you also want to add the vAPI endpoint, ensure this service has been started.
```
vcenter_plugin_endpoints:
  - hostname: "vcenter hostname"
    username: "vcenter username"
    password: "vcenter password"
    domain: "{{ dns_domain }}"
    add_vapi_endpoint: no
```

Add vRealize Automation Cafe Endpoints.
```
cafe_plugin_endpoints:
  - name: "Tenant A"
    hostname: "vra.example.com"
    tenant: "vsphere.local"
    username: "vra admin username"
    password: "vra admin password"
```

Add HTTP Restful API Endpoints. Supported authentication types are: '**Basic**' and '**OAuth2**'.

Uncomment and set proxy section if rest host is behind a proxy.
```
rest_plugin_endpoints:
  - name: Rest Host
    url: https://rest.example.com/api
    auth_type: Basic
    ## Username and Password is required when auth type is set to 'Basic'.
    username: "rest username"
    password: "rest password"
    ## OAuth2 Token is required when auth type is set to 'OAuth2'.
    # oauth2_token: "token"
    # proxy_host: proxy.example.com
    # proxy_port: 8080
    ## Proxy username and password can be left commented if proxy doesn't require authentication.
    # proxy_username: "{{ rest_plugin_endpoint_proxy_username }}"
    # proxy_password: "{{ rest_plugin_endpoint_proxy_password }}"
    host_verification: no
```

Add PowerShell Host Endpoints. Supported authentication types are: '**Basic**' and '**Kerberos**'.
```
powershell_plugin_endpoints:
  - name: PowerShell Host
    hostname: pshost.example.com
    port: 443
    auth_type: Basic
    username: "host username"
    password: "host password"
```

Add SOAP API Endpoints. Supported Authentication types are: '**Basic**', '**Digest**' and '**Kerberos**'.

Uncomment and set proxy section if SOAP host is behind a proxy.
```
soap_plugin_endpoints:
  - name: Soap Host
    wsdl_uri: soapuri.example.com
    auth_type: Basic
    ## Username and Password is required when auth type is set to 'Basic' or 'Digest'.
    username: "soap username"
    password: "soap password"
    ## Kerberos SPN is required when auth type is set to 'Kerberos'.
    # kerberos_spn: user@example.local
    # proxy_host: proxy.example.com
    # proxy_port: 8080
```

Dependencies
------------
  ```
  - src: nmshadey.vmware_deploy_ova
    name: vmware_deploy_ova
  ```
Example Playbook
----------------
    ```
    - hosts: vro_appliances
      become: no
      roles:
        - vmware_deploy_vro
    ```

License
-------

MIT

Author Information
------------------

Gavin Stephens (www.simplygeek.co.uk)