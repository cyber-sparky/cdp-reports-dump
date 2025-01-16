
## List hosts 

```sh
ansible -i inventory.ini prod --list-hosts
```

## Creating a config file for ansible 

```sh
/# mkdir /etc/ansible/
/# cat > /etc/ansible/ansible.cfg <<EOF
> [defaults]
> stdout_callback = yaml
> deprecation_warnings = False
> host_key_checking = False
> retry_files_enabled = False
> inventory = /inventory.ini
> EOF
```

### Imp Notes 

- Instead of using the /etc/ansible/ansible.cfg file, you can create an ansible.cfg file in the directory where you have created the playbook or from the directory where you are running Ansible commands. 

## Ping all the known hosts 

```sh
ansible -i inventory.ini all -m ping
```

## Print hostnames of all machines 

```sh
ansible -i inventory.ini all -m shell -a "hostname"
```

# Tasks 

## Use the ansible-doc command to see help examples and find a module that can send a file from DevSecOps-Box to remote machines

```sh
ansible-doc copy 
```

## Create a file with some content, let the file name be notes at the location /root

```sh
touch /root/notes 
```

## Using an ansible ad-hoc command, copy the file /root/notes into all remote machines (sandbox and production) to the destination directory /root

```sh
ansible -i inventory.ini all -m copy -a "src=/root/notes dest=/root"
```
