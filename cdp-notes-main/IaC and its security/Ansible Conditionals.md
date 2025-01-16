
## When 

"when" conditional in Ansible is used to run a task only if a certain condition is true.

## Match conditin using when set to ubuntu 

```yml
---
- name: Simple playbook
  hosts: all
  remote_user: root
  gather_facts: yes     # what does it means?

  tasks:
  - debug:
      msg: "This system uses Ubuntu-based distro"
    when: ansible_distribution == "Ubuntu"
```

## register

"register" statement is used to save a task as a new variable that can be used for the desired tasks.

## Ansible playbook with register conditionals

```yml
---
- name: Simple playbook
  hosts: all
  remote_user: root
  gather_facts: no    # Ansible will not automatically collect system facts (like OS type, network interfaces, etc.) before executing tasks

  tasks:
  - name: Show the content of /etc/os-release
    command: cat /etc/os-release # Takes the output of this command
    register: os_release # saved as variable called os_release

  - debug:
      msg: "This system uses Ubuntu-based distro"
    when: os_release.stdout.find('Ubuntu') != -1
```

## Challenges 

### Create a new playbook file at /challenges/main.yml that contains a task to check if the nginx package is installed or not using stat module

```yml
- name: Playbook to install nginx
  hosts: all
  remote_user: root
  become: yes

  # We are checking availability of nginx binary at a specific location
  tasks:
    - name: check if nginx installed
      stat:
        path: /usr/sbin/nginx
      register: stat_nginx
```

### Add another task with the name Print version to print the Nginx version by using the msg module if Nginx is installed.

```yml
    - name: get nginx version
      command: nginx -v
      register: nginx_version
      when: stat_nginx.stat.exists

    - name: Print version
      debug:
        msg: "{{ nginx_version.stderr }}"
      when:
        - nginx_version is defined
        - stat_nginx.stat.exists
```

### Let your playbook install the missing nginx using the apt module on the sandbox machine.

```yml
 - name: install nginx if not exist
      apt:
        name: nginx
        state: present
        update_cache: true
      when: not stat_nginx.stat.exists
```

