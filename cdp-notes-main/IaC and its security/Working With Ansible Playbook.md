
## Creating a playbook 

The following is a process of creating a playbook which helps in deploying a simple webserver by django and nginx

```sh
/# mkdir simple-playbook && cd simple-playbook
/simple-playbook# mkdir tasks
/simple-playbook# cat > tasks/main.yml <<EOL
> ---
> - name: Install nginx
>   apt:
>     name: nginx
>     state: present
>     update_cache: true
> 
> - name: Copy the configuration
>   template:
>     src: templates/default.j2
>     dest: /etc/nginx/sites-enabled/default
> 
> - name: Start nginx service
>   service:
>     name: nginx
>     state: started
>     enabled: yes
> 
> - name: Clone django repository
>   git:
>     repo: https://gitlab.practical-devsecops.training/pdso/django.nv.git
>     dest: /opt/django
> 
> - name: Install dependencies
>   command: pip3 install -r requirements.txt
>   args:
>     chdir: /opt/django
> 
> - name: Database migration
>   command: python3 manage.py migrate
>   args:
>     chdir: /opt/django
> 
> - name: Load data from the fixtures
>   shell: python3 manage.py loaddata fixtures/*
>   args:
>     chdir: /opt/django
> 
> - name: Run an application in the background
>   shell: nohup python3 manage.py runserver 0.0.0.0:8000 &
>   args:
>     chdir: /opt/django
> EOL
/simple-playbook# mkdir templates
/simple-playbook# cat > templates/default.j2 <<EOL
> {% raw %}
> server {
>     listen      80;
>     server_name localhost;
> 
>     access_log  /var/log/nginx/django_access.log;
>     error_log   /var/log/nginx/django_error.log;
> 
>     proxy_buffers 16 64k;
>     proxy_buffer_size 128k;
> 
>     location / {
>         proxy_pass  http://localhost:8000;
>         proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
>         proxy_redirect off;
> 
>         proxy_set_header    Host \$host;
>         proxy_set_header    X-Real-IP \$remote_addr;
>         proxy_set_header    X-Forwarded-For \$proxy_add_x_forwarded_for;
>         proxy_set_header    X-Forwarded-Proto http;
>     }
> }
> {% endraw %}
> EOL
/simple-playbook# cat > templates/default.j2 <<EOL
> {% raw %}
> server {
>     listen      80;
>     server_name localhost;
> 
>     access_log  /var/log/nginx/django_access.log;
>     error_log   /var/log/nginx/django_error.log;
> 
>     proxy_buffers 16 64k;
>     proxy_buffer_size 128k;
> 
>     location / {
>         proxy_pass  http://localhost:8000;
>         proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
>         proxy_redirect off;
> 
>         proxy_set_header    Host \$host;
>         proxy_set_header    X-Real-IP \$remote_addr;
>         proxy_set_header    X-Forwarded-For \$proxy_add_x_forwarded_for;
>         proxy_set_header    X-Forwarded-Proto http;
>     }
> }
> {% endraw %}
> EOL
/simple-playbook# tree
.
├── tasks
│   └── main.yml
└── templates
    └── default.j2

2 directories, 2 files
/simple-playbook# cat > main.yml <<EOL
> ---
> - name: Simple playbook
>   hosts: sandbox
>   remote_user: root
>   gather_facts: no
> 
>   tasks:
>   - include: tasks/main.yml
> EOL
/simple-playbook# tree
.
├── main.yml
├── tasks
│   └── main.yml
└── templates
    └── default.j2

2 directories, 3 files
/simple-playbook# 
```