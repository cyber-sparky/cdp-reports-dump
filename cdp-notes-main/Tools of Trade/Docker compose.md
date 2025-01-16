# Introduction 

| Task                           | Command                                            | Description |
|--------------------------------|----------------------------------------------------|-------------|
| Running a Container with Docker| `docker run -d --name ubuntu -i ubuntu:20.04`      | Runs a container named `ubuntu` from the `ubuntu:20.04` image. |
| Running a Container with Docker Compose | `cat >docker-compose.yml<<EOF`<br>`version: "3"`<br>`services:`<br>`  ubuntu:`<br>`    image: ubuntu:20.04`<br>`    container_name: ubuntu1`<br>`    stdin_open: true`<br>`EOF` | Defines a container in `docker-compose.yml` using `ubuntu:20.04` image. |
| Starting Containers with Docker Compose | `docker-compose up -d`                          | Starts containers defined in `docker-compose.yml`. |
| Viewing Running Containers    | `docker ps`                                        | Lists running containers. |
| Cleaning Up                   | `docker rm -f $(docker ps -aq)`<br>`docker-compose down` | Removes all running containers.<br>Brings down services defined in `docker-compose.yml`. |

**Note:**
- Docker Compose allows handling multiple containers using a single file (`docker-compose.yml`).
- When using `docker-compose up`, a network is created during provisioning.
- `docker-compose` automatically checks for `docker-compose.yml` in the current directory.
- `docker-compose down` stops and removes services defined in `docker-compose.yml`.

**Advantages of Docker Compose:**
- Efficiently manages multiple containers.
- Simplifies running complex applications with multiple containers in the same network.

# Compose file 

**Purpose:**
- To run/handle multiple containers using `docker-compose` and its YAML file instead of the `docker` command.

**Basic Syntax of `docker-compose.yml`:**
```yaml
version: "3"

services:
  web:
    image: nginx
    container_name: webserver
    ports:
     - "80:80"
    environment:
     - NGINX_HOST=example.com
    volumes:
     - ./:/var/www/html/app
     - image_data:/var/www/html/images

volumes:
  image_data:
```

**Descriptions:**
| Syntax            | Description                                                                 |
|-------------------|-----------------------------------------------------------------------------|
| `version`         | Version of compose file format to use.                                      |
| `image`           | Specify the image to run.                                                   |
| `container_name`  | Specify a custom container name.                                            |
| `ports`           | Expose port(s), similar to `docker run -p` argument.                        |
| `environment`     | Add environment variables into the container by defining a key-value pair.  |
| `volumes`         | Volumes to save data persistently using various options like bind or volumes.|

**Note:**
- The `docker-compose.yml` script should be created in a specific directory to avoid issues with the created volume.
- Using a `docker-compose` file is more efficient as it declares the desired behavior using a file (IaC - Infrastructure as Code).
- The container behavior is defined inside the `services:` dictionary, without which you cannot operate Docker containers using `docker-compose`.

**Example `docker-compose.yml` File:**

```yaml
cat >~/docker-compose.yml<<EOF
version: "3"

services:
  ubuntu:
    image: ubuntu:20.04
    container_name: myubuntu
    volumes:
     - data:/opt

  alpine:
    image: alpine:3.13
    container_name: myalpine
    volumes:
     - data:/tmp

volumes:
  data:
EOF
```

**Running the Example:**

```sh
cd ~/
docker-compose up -d
```
**Command Output:**

```sh
[+] Running 4/4
 ⠿ Network root_default  Created
 ⠿ Volume "root_data"    Created
 ⠿ Container ubuntu      Started
 ⠿ Container alpine      Started  
```

**Checking Containers:**

```sh
docker-compose ps
```
**Command Output:**

```sh
NAME                COMMAND             SERVICE             STATUS              PORTS
alpine              "/bin/sh"           alpine              exited (0)          
ubuntu              "bash"              ubuntu              exited (0)
```

**Stopping Services:**

```sh
docker-compose down
```
**Command Output:**

```sh
[+] Running 3/3
 ⠿ Container ubuntu      Removed
 ⠿ Container alpine      Removed
 ⠿ Network root_default  Removed
```

# Challenges

## Create a compose file at /opt/docker-compose.yml with the service and container named webserver using the nginx image (without a specific version). Bind port 80 from the container to port 8080 on the host. Ensure that the container is running and not exited

```bash
cat > /opt/docker-compose.yml<<EOF
version: "3"
services:
  webserver:
    image: nginx
    container_name: webserver
    ports:
     - 8080:80
EOF
```

## Add docker volume name data into webserver service and mapping it to /usr/share/nginx/html

```yml
cat > /opt/docker-compose.yml<<EOF
version: "3"
services:
  webserver:
    image: nginx
    container_name: webserver
    ports:
     - 8080:80
    volumes:
     - data:/usr/share/nginx/html

volumes:
  data:
EOF
```

## Create a random file with hello world text inside the webserver container and save it in /usr/share/nginx/html/hello.html

```bash
~# docker-compose -f /opt/docker-compose.yml up -d
[+] Running 8/8
 ⠿ webserver Pulled                                                                                             9.5s
   ⠿ f11c1adaa26e Pull complete                                                                                 3.2s
   ⠿ c6b156574604 Pull complete                                                                                 7.0s
   ⠿ ea5d7144c337 Pull complete                                                                                 7.0s
   ⠿ 1bbcb9df2c93 Pull complete                                                                                 7.1s
   ⠿ 537a6cfe3404 Pull complete                                                                                 7.2s
   ⠿ 767bff2cc03e Pull complete                                                                                 7.3s
   ⠿ adc73cb74f25 Pull complete                                                                                 7.3s
[+] Running 3/3
 ⠿ Network opt_default  Created                                                                                 0.1s
 ⠿ Volume "opt_data"    Created                                                                                 0.0s
 ⠿ Container webserver  Started                                                                                 0.6s
~# docker exec webserver sh -c 'echo "hello world" > /usr/share/nginx/html/hello.html'
```