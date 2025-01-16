
# Docker volumes

Docker volumes and bind mounts are both used to persist data, but they work differently:

- **Volumes** are managed by Docker and stored in a part of the host filesystem which is managed by Docker (/var/lib/docker/volumes/ on Linux).
- **Bind Mounts** can be any location on the host system.

## Summary of Commands

| **Step**                                   | **Command**                                                  |
|--------------------------------------------|--------------------------------------------------------------|
| List Docker volumes                        | `docker volume ls`                                           |
| Create a Docker volume                     | `docker volume create demo`                                  |
| Run a container with a volume              | `docker run --name ubuntu -d -v demo:/opt -it ubuntu:20.04`  |
| Access container shell                     | `docker exec -it ubuntu bash`                                |
| Create a file in the mounted volume        | `echo "Welcome to the containers" > /opt/welcome.txt`        |
| Exit and remove the container              | `exit` <br> `docker rm -f ubuntu`                            |
| Verify file on the host system             | `ls /var/lib/docker/volumes/demo/_data/`                     |
| Run another container with the same volume | `docker run --name ubuntu1 -d -v demo:/tmp -it ubuntu:20.04` |
| Verify file in the new container           | `docker exec -it ubuntu1 ls /tmp`                            |

# Bind mounts

### Bind Mounts

Bind mounts allow mounting a host’s directory into a container’s directory directly without needing specific Docker commands to create a bind mount. This can be useful for sharing data between the host system and Docker containers.

#### Key Points
- Bind mounts reference a file or directory on the host machine by its absolute path.
- Unlike volumes, bind mounts do not create a new directory within Docker’s storage directory.
- Bind mounts map the host’s directory to a container’s specific directory during the container run command.

### Steps and Commands

| **Step**                                   | **Command**                                                                                 |
|--------------------------------------------|---------------------------------------------------------------------------------------------|
| Run a container with a bind mount          | `docker run --name ubuntu2 -d -v /opt:/opt -it ubuntu:20.04`                                |
| Access container shell                     | `docker exec -it ubuntu2 bash`                                                              |
| List files in the container’s /opt directory | `ls /opt`                                                                                   |
| Exit from the container                    | `exit`                                                                                      |
| Create a file on the host system           | `echo "Hello from the host" > /opt/welcome.txt`                                             |
| Remove the old container                   | `docker rm -f ubuntu2`                                                                      |
| Start a new container with the bind mount  | `docker run --name ubuntu2 -d -v /opt:/opt -it ubuntu:20.04`                                |
| Access the new container shell             | `docker exec -it ubuntu2 bash`                                                              |
| Validate presence of welcome.txt in container | `ls /opt`                                                                                   |
| Remove the file in the container           | `rm /opt/welcome.txt`                                                                       |
| Exit from the container                    | `exit`                                                                                      |
| Check for the file on the host             | `ls /opt/welcome.txt`                                                                       |

### Tips
- Bind mounts are useful in both development and production environments.
- Changes made within the container to bind-mounted directories/files are reflected on the host and vice versa.

Learn more about bind mounts: [Docker Bind Mounts Documentation](https://docs.docker.com/storage/bind-mounts/)

# tmpfs

This tmpfs mount will not save the data persistently as it uses the host’s memory (RAM) as temporary storage.

## Challenges

### Create a hello.txt file under the /opt directory. The file can have some random text of your choice

```bash
/# touch hello.txt > /opt/
bash: /opt/: Is a directory
/# touch hello.txt > /opt/heloo.txt
/# cd /opt
/opt# mv heloo.txt hello.txt
```

### Create a container with name bindmount using ubuntu:20.04 image. Mount the above file (not the directory) as a bind mount inside this container at /src location.

```bash
/# docker run --name bindmount -v /opt/hello.txt:/src/hello.txt -it ubuntu:20.04
docker: Error response from daemon: Conflict. The container name "/bindmount" is already in use by container "5ebda9d2caeb92e5fc8320fd6420c6b885ddd16773c684896d056f6435868eb7". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
/# docker stop 5e
5e
/# docker rm 5e
5e
/# docker run --name bindmount -v /opt/hello.txt:/src/hello.txt -it ubuntu:20.04
root@7c71ae894d56:/# cd /sr
src/ srv/ 
root@7c71ae894d56:/# cd /sr
src/ srv/ 
root@7c71ae894d56:/# cd /src
root@7c71ae894d56:/src# ls
hello.txt
root@7c71ae894d56:/src# cat hello.txt 
```

### Create a new volume called data using docker volume command

```bash
/# docker volume new data

Usage:  docker volume COMMAND

Manage volumes

Commands:
  create      Create a volume
  inspect     Display detailed information on one or more volumes
  ls          List volumes
  prune       Remove all unused local volumes
  rm          Remove one or more volumes

Run 'docker volume COMMAND --help' for more information on a command.
/# docker volume create data
data
```

### Run an another ubuntu:20.04 container with name volumemount. Mount the data volume we just created into the container at /src location. Create a file /src/hello.txt in this container with some random text

```bash
/# docker run -it -v data:/src --name volumemount ubuntu:20.04
root@3d9aa3e2c848:/# cd sr
bash: cd: sr: No such file or directory
root@3d9aa3e2c848:/# cd src
root@3d9aa3e2c848:/src# touch hello.txt
root@3d9aa3e2c848:/src# exit
exit
```