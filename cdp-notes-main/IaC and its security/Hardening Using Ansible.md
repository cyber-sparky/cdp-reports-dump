
## Install ansible and ansible-lint 

```sh
pip3 install ansible==8.7.0 ansible-lint==6.8.1
```

## Creating a inventory file 

```sh
cat > inventory.ini <<EOL

# DevSecOps Studio Inventory
[devsecops]
devsecops-box-oiahxczk

[prod]
prod-oiahxczk

EOL
```

## Before executing the ansible with an inventory file 

Take list of rsa keys and add it to known list of hosts 

```sh
ssh-keyscan -t rsa prod-oiahxczk >> ~/.ssh/known_hosts
ssh-keyscan -t rsa devsecops-box-oiahxczk >> ~/.ssh/known_hosts
```

## Running a command using ansible across the hosts 

Here we have used apt ansible module to install the ntp service on the production machine.

```sh
ansible -i inventory.ini  prod -m apt -a "name=ntp state=present"
```

## Print bash version on all the hosts 

```sh
ansible -i inventory.ini all -m command -a "bash --version"
```

# Tasks 

## Use the Ansible command module to find shell module

```sh
/# ansible-doc -l | grep shell
ansible.builtin.shell                                                                            Execute she...        
ansible.windows.win_powershell                                                                   ...                   
ansible.windows.win_shell                                                                        Execute shell co...   
community.mongodb.mongodb_shell                                                                  Run commands...       
community.vmware.vmware_vm_shell                                                                 Run commands in a VMware ...
fortinet.fortimanager.fmgr_sshfilter_profile_shellcommands                                       ...                   
vmware.vmware_rest.appliance_access_shell                                                        Set enabled state of BASH, that is, access to BASH from wit...
vmware.vmware_rest.appliance_access_shell_info                                                   Get enabled state of BASH, that is, access to BASH from wit...
```

## Use inventory file (-i inventory.ini) and run the uptime command on the production machine using shell module

```sh
/# ansible -i inventory.ini prod -m shell -a "uptime"
prod-oiahxczk | CHANGED | rc=0 >>
 06:33:47 up 10 min,  1 user,  load average: 11.76, 10.74, 9.32

# m -> module where we specified it as shell 
# -a -> kind of an argument where we passed our command to run
```

## Creating a playbook against prod instance 

```sh
cat > playbook.yml <<EOL
---
- name: Example playbook to install firewalld
  hosts: prod
  remote_user: root
  become: yes
  gather_facts: no
  vars:
    state: present

  tasks:
  - name: ensure firewalld is at the latest version
    apt:
      name: firewalld
      update_cache: yes
EOL

# update_cache: yes will ensure package handler updates its repository cache prior to attempting the package installation.
```

## Running with playbook and inventory 

you will notice `changed=1` meaning it has implemented on what needs to be implemented 

```sh
ansible-playbook -i inventory.ini playbook.yml
```

# Tasks

## Create a new directory /challenge and create a playbook.yml file inside the /challenge directory. You can use the above playbook.yml syntax as a starter for this task, but the /challenge/playbook.yml needs to use the secfigo.terraform role to install the Terraform utility.

```sh
/# mkdir /challenge 
/# cd /challenge/
/challenge# cat > playbook.yml <<EOL
> - name: Example of implementing a role called terraform
>   hosts: prof
>   ^C
/challenge# ^C
/challenge# ls
/challenge# cd /challenge/
/challenge# nano playbook.yml
/challenge# cp ../inventory.ini ./
/challenge# cat playbook.yml 
- name: Example playbook for terraform 
  hosts: prod
  remote_user: root
  become: yes
  vars:
    state: present

  roles:
    - secfigo.terraform
```

## Install secfigo.terraform role using ansible-galaxy.

```sh
ansible-galaxy role install secfigo.terraform
```

## Execute the /challenge/playbook.yml against the prod machine to install the Terraform utility. Optionally put this hardening job in the CI pipeline.

```sh
/# ansible-playbook -i /challenge/inventory.ini /challenge/playbook.yml 

PLAY [Example playbook for terraform] ***************************************************************

TASK [Gathering Facts] ******************************************************************************
ok: [prod-oiahxczk]

TASK [secfigo.terraform : Make sure unzip is installed] *********************************************
ok: [prod-oiahxczk]

TASK [secfigo.terraform : Download and Install Terraform] *******************************************
skipping: [prod-oiahxczk]

PLAY RECAP ******************************************************************************************
prod-oiahxczk              : ok=2    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
/challenge# 
```

## Searching for module through ansible-galaxy 

```sh
ansible-galaxy search terraform
```

## Installing a terraform module from secfigo 

```sh
ansible-galaxy install secfigo.terraform
```

# Tasks 

## Create a new directory /hardening and create an ansible-hardening.yml file inside the /hardening directory. The /hardening/ansible-hardening.yml needs to use the dev-sec.os-hardening role to harden a target server.

```sh
/# mkdir /hardening 
/# cd /hardening/
/hardening# cp ../challenge/playbook.yml ./ansible-hardening.yml
/hardening# cp ../challenge/inventory.ini ./
/hardening# nano ansible-hardening.yml 
/hardening# cat ansible-hardening.yml 
- name: Example playbook for terraform 
  hosts: prod
  remote_user: root
  become: yes
  vars:
    state: present

  roles:
    - dev-sec.os-hardening
/hardening# 
```

## Install dev-sec.os-hardening role from ansible-galaxy

```sh
ansible-galaxy role install dev-sec.os-hardening
```

