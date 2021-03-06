Red Hat CodeReady Containers Role w/hacks
=========

Use Ansible to deploy Red Hat CodeReady Containers with HACKS

CodeReady Containers brings a minimal, preconfigured OpenShift 4.1 or newer cluster to your local laptop or desktop computer for development and testing purposes. CodeReady Containers is delivered as a Red Hat Enterprise Linux virtual machine that supports native hypervisors for Linux, macOS, and Windows 10.

Requirements
------------

*  Ansible 
*  Fedora or RHEL 
* IF using RHEL make sure it is registered
* OpenShift CodeReady WorkSpaces  pull secert 
  * https://cloud.redhat.com/openshift/install/crc/installer-provisioned

**GET SHA** 
```
$ curl -OL https://mirror.openshift.com/pub/openshift-v4/clients/crc/1.8.0/sha256sum.txt
$ cat sha256sum.txt | grep crc-linux-amd64.tar.xz | awk '{print $1}'
```

Inspriation 
--------------
[Accessing CodeReady Containers on a Remote Server](https://www.openshift.com/blog/accessing-codeready-containers-on-a-remote-server/) by Jason Dobies  
[Overview: running crc on a remote server](https://gist.github.com/tmckayus/8e843f90c44ac841d0673434c7de0c6a) by [Trevor McKay](https://gist.github.com/tmckayus)

Features
--------
* CodeReady Containers Remote Server Access

Role Variables
--------------

Type  | Description  | Default Value
--|---|--
crc_version  | Target CRC version  | 1.10.0
crc_sha      | SHA informaqtion of the crc-linux-amd64.tar.xz file | 97aa185f6ff88560611f81fbf5991cfced814e8d9ac796947c7bef6374ee7af3
crc_url      |  CRC download URL | https://mirror.openshift.com/pub/openshift-v4/clients/crc/
crc_file_name  | CRC filename  | crc-linux-amd64.tar.xz
pull_secert_path | default path of pull secert | /tmp/pull-secert.txt
pull_secert_content: | pull secert content     |  changeme
use_all_in_one_haproxy | Use current machine as haproxy LB | true
haproxy_ip             | Set ha proxy ip if above is set to flase **NOT TESTED**| ""
use_all_in_one_dnsmasq | Use current machine as dnsmasq server | true
log_level              | Change log level of crc start command | info
crc_ip_address | Default CRC ip address| 192.168.130.11
ocp4_release  | OCP release folder for cli | latest
ocp4_version   | OCP cli version | 4.4.3
ocp4_release_url | OCP release url | "https://mirror.openshift.com/pub/openshift-v4/clients/ocp/{{ ocp4_release }}/"
ocp4_client | OCP cli filename | ""openshift-client-linux-{{ ocp4_version }}.tar.gz"
remove_oc_tool | remove oc cli  | false
delete_crc_deployment | delete CodeReady Containers deployment  | false

Dependencies
------------

**On RHEL 8.x**
* Register system
* Follow system requirements from the code ready containers documentation 

**On Fedora**
* Follow system requirements from the code ready containers documentation 
* enable and start sshd

Example Playbook
----------------
To run playbook as sudo add the `-K` flag 
Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:
```
- hosts: servers
  become: yes
  vars:
    crc_version: 1.10.0
    crc_sha: 97aa185f6ff88560611f81fbf5991cfced814e8d9ac796947c7bef6374ee7af3
    pull_secert_path: /tmp/pull-secert.txt
    pull_secert_content: |
      changeme
    use_all_in_one_haproxy: true
    haproxy_ip: ""
    use_all_in_one_dnsmasq: true 
    log_level: info
    ocp4_release: latest
    ocp4_version: 4.4.3
    remove_oc_tool: false
    delete_crc_deployment: false
  roles:
  - codeready-containers-hacks
```

Deployment Flags
---------------
**Start a deployment**
```
ansible-playbook  -i inventory deploy-crc.yml  -K
```

**Setup crc and start deployment**
```
ansible-playbook  -i inventory deploy-crc.yml --tags setup_crc,start_crc_deployment  -K
```

**Download and install CRC**
```
ansible-playbook  -i inventory deploy-crc.yml --tags download_crc,extract_crc  -K
```

**Configure OpenShift cli**
```
ansible-playbook  -i inventory deploy-crc.yml --tags configure_oc_cli -K
```

**Configure HAPROXY**
```
ansible-playbook  -i inventory deploy-crc.yml --tags configure_ha_proxy  -K
```

**Configure dnsmasq**
```
ansible-playbook  -i inventory deploy-crc.yml --tags configure_dnsmaq  -K
```

**Get crc url and login info**
```
ansible-playbook  -i inventory deploy-crc.yml --tags get_codeready_info
```

**Delete deployment**
```
ansible-playbook  -i inventory deploy-crc.yml --extra-vars "delete_crc_deployment=true" -K 
```

Debug info
----------
* During setup the new CodeReady Containers release tasks
  * `tail -f /tmp/crc_setup.log`
  * or `tail -f  tail -f ~/.crc/crc.log`

To-Do
-------
* develop against MacOS
* test against RHEL 7
* develop for windows
* test using external dns and haproxy 
* develop against other OS's
* add cpu and memory custom sizing options 
* delete deployment
  
License
-------

GPL-3.0

Author Information
------------------

* Tosin Akinosho - [tosin2013](https://github.com/tosin2013)
