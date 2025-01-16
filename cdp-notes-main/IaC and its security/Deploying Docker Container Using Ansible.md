
## Ansible playbook for deploying docker container 

```ansible 
---
- name: Deploying container
  hosts: prod
  remote_user: root
  gather_facts: no

  tasks:
  - name: Ensure docker is installed
    stat:
      path: "/usr/bin/docker"
    register: docker_result

  - debug:
      msg: "Please install the docker"
    when: not docker_result.stat.exists

  - name: Install python3-docker for Docker SDK
    pip:
      name: docker
      executable: pip3

  - name: Log into DockerHub
    docker_login:
      registry_url: "registry-oiahxczk.lab.practical-devsecops.training"
      username: "{{ docker_username }}"
      password: "{{ docker_password }}"

  - name: Pulling new image
    docker_image:
      name: "registry-oiahxczk.lab.practical-devsecops.training/django.nv:1.0"
      source: pull

  - name: Remove django.nv container
    docker_container:
      name: "django.nv"
      state: absent
      force_kill: yes

  - name: Running new container
    docker_container:
      name: "django.nv"
      image: "registry-oiahxczk.lab.practical-devsecops.training/django.nv:1.0"
      detach: yes
      ports:
        - 8000:8000
```