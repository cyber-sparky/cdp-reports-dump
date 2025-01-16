# Pull the Image 

## Pull Command

- `docker pull` is used to download a Docker image from a Docker registry.
- Similar to a Git repository but designed to store Docker images.

## Default Behavior:

- By default, images are pulled from DockerHub.
- If no tag/version is specified, Docker assumes the `latest` tag.

## Steps to Pull an Image:

1. **Pull Ubuntu Image:**
    ```sh
    docker pull ubuntu
    ```
    - **Output:**
      ```
      Using default tag: latest
      latest: Pulling from library/ubuntu
      d72e567cc804: Pull complete
      0f3630e5ff08: Pull complete
      b6a83d81d1f4: Pull complete
      Digest: sha256:bc2f7250f69267c9c6b66d7b6a81a54d3878bb85f1ebb5f951c896d13e6ba537
      Status: Downloaded newer image for ubuntu:latest
      docker.io/library/ubuntu:latest
      ```

2. **Pull Alpine Image Quietly:**
    ```sh
    docker pull -q alpine
    ```
    - **Output:**
      ```
      docker.io/library/alpine:latest
      ```

## Summmary

| Action                  | Command                                 | Output Example                                                                                                                  |
|-------------------------|-----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Pull Ubuntu Image       | ```docker pull ubuntu```                | ```Using default tag: latest latest: Pulling from library/ubuntu d72e567cc804: Pull complete 0f3630e5ff08: Pull complete ...``` |
| Pull Alpine Image Quietly | ```docker pull -q alpine```           | ```docker.io/library/alpine:latest```                                                                                           |

# Run the Image 

## Docker Run Commands and Outputs

| Action                                | Command                                               | Expected Output                                                                                                                    |
|---------------------------------------|-------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Run Container in Background           | `docker run -d --name myubuntu ubuntu`                | `CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES`                                                                            |
| Check Running Containers              | `docker ps`                                           | No output for `myubuntu` container due to no attached process.                                                                     |
| Run Interactive Container (will error)| `docker run -d --name myubuntu -i ubuntu`             | `docker: Error response from daemon: Conflict. The container name "/myubuntu" is already in use...`                                |
| Remove Container                      | `docker rm myubuntu`                                  | Container `myubuntu` removed.                                                                                                      |
| Run Interactive Container Again       | `docker run -d --name myubuntu -i ubuntu`             | `c43190a11b8a68673a49f967d78da11cc4d27c3222fedb6496395f4e175ac807`                                                                 |
| Check Running Containers (success)    | `docker ps`                                           | `CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES 7aca537e07e2 ubuntu "/bin/bash" 2 seconds ago Up Less than a second myubuntu`|
| Run and Pull Image Automatically      | `docker run busybox`                                  | `Unable to find image 'busybox:latest' locally latest: Pulling from library/busybox 205dae5015e7: Pull complete...`                |

## Key Points:
- **`docker run`**: Starts a container from an image.
  - **Options**:
    - `-d`: Run container in background.
    - `--name`: Assign a name to the container.
    - `-i`: Keep container running interactively.
- **Error Handling**:
  - If container name conflicts, remove the existing container using `docker rm`.
- **Automatic Pulling**:
  - `docker run` pulls the image if not available locally.

# Run a command in a running container

## Docker Exec Commands and Outputs

| Action                                 | Command                                     | Expected Output                                                                                                 |
|----------------------------------------|---------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| Execute Command Inside Container       | `docker exec myubuntu whoami`               | `root`                                                                                                           |
| Execute Command to View File Content   | `docker exec myubuntu cat /etc/lsb-release` | `DISTRIB_ID=Ubuntu` <br> `DISTRIB_RELEASE=20.04` <br> `DISTRIB_CODENAME=focal` <br> `DISTRIB_DESCRIPTION="Ubuntu 20.04.1 LTS"` |
| Spawn a Shell in the Container         | `docker exec -it myubuntu bash`             | `root@c43190a11b8a:/#`                                                                                           |
| Exit the Shell                         | `exit`                                      | Exits the interactive shell and returns to the host system.                                                      |

## Key Points:
- **`docker exec`**: Used to execute commands inside a running container or to get a shell in the container.
  - **Examples**:
    - `docker exec myubuntu whoami`: Displays the current user in the container.
    - `docker exec myubuntu cat /etc/lsb-release`: Shows the distribution release information.
    - `docker exec -it myubuntu bash`: Opens an interactive bash shell in the container.
- **Interactive Mode (`-it` flag)**:
  - Allocates a pseudo-TTY terminal.
  - Keeps the terminal open for interactive communication.
  - Allows interaction with the container’s command prompt as if using a local terminal.

# List containers

## Docker PS Command Options

### Purpose of `ps` Command:
- `ps` is used to list containers, showing their status and other details.

**Options:**
- `-a`: Shows all containers, including those in exited or stopped status.

## Example Usage:

1. **Run Container:**
   ```sh
   docker run -d --name yourubuntu ubuntu
   ```

2. **List Containers:**
   ```sh
   docker ps -a
   ```
   - **Output:**
     ```
     CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
     7aca537e07e2        ubuntu              "/bin/bash"         4 minutes ago       Up 4 minutes                            myubuntu
     9ebd1d8e6d84        debian              "bash"              5 minutes ago       Exited (0) 5 minutes ago                       sleepy_hellman
     ```

**Effect Without `-a` Flag:**
- Without `-a`, `docker ps` would only show running containers.

## Summary

| Action                              | Command                           | Output Example                                                                                                            |
|-------------------------------------|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| List All Containers (including exited or stopped) | ```docker ps -a```               | ```CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES 7aca537e07e2 ubuntu "/bin/bash" 4 minutes ago Up 4 minutes myubuntu 9ebd1d8e6d84 debian "bash" 5 minutes ago Exited (0) 5 minutes ago sleepy_hellman```

# Show the running processes of a container

## Docker Top Command

**Purpose of `top` Command:**
- `top` displays the running processes inside a container.

## Example Usage:

1. **Display Running Processes:**
   ```sh
   docker top myubuntu
   ```
   - **Output:**
     ```
     UID   PID   PPID  C    STIME  TTY  TIME     CMD
     root  1336  1318  0    19:16  ?    00:00:00 /bin/bash
     ```

**Description:**
- Shows the running processes (`PID`, `PPID`, `CMD`) inside the `myubuntu` container running `/bin/bash`.

## Summary

| Action                             | Command                   | Output Example                                                                  |
|------------------------------------|---------------------------|---------------------------------------------------------------------------------|
| Show Running Processes in Container | ```docker top myubuntu``` | ```UID PID PPID C STIME TTY TIME CMD root 1336 1318 0 19:16 ? 00:00:00 /bin/bash``` |

# Challenges 

### Create a container by running nginx:1.21.3 image in detach mode

```bash
/# docker run -d nginx:1.21.3
Unable to find image 'nginx:1.21.3' locally
1.21.3: Pulling from library/nginx
b380bbd43752: Pull complete 
fca7e12d1754: Pull complete 
745ab57616cb: Pull complete 
a4723e260b6f: Pull complete 
1c84ebdff681: Pull complete 
858292fd2e56: Pull complete 
Digest: sha256:644a70516a26004c97d0d85c7fe1d0c3a67ea8ab7ddf4aff193d9f301670cf36
Status: Downloaded newer image for nginx:1.21.3
87811c059f50ab80aaa1d90766866fe6be855236b4528cddcc16f86725c64a31
```

### Start a container using the image mentioned above. Name the container webserver and set the environment variable APP=nginx. Run the container in detached mode.

```bash
/# docker run --name webserver -e APP=nginx -d nginx:1.21.3
d1d6d2c72f14bbd2728293dd0627267ad4bb57191cfb93043d6036c1d1de0437
```

### Remove the above container and start another one using above configurations and by binding container port, 80 with the host’s 80 port. Visit this URL to verify port binding

```bash
/# docker rm webserver
Error response from daemon: You cannot remove a running container d1d6d2c72f14bbd2728293dd0627267ad4bb57191cfb93043d6036c1d1de0437. Stop the container before attempting removal or force remove
/# docker stop webserver
webserver
/# docker rm webserver
webserver
/# docker run -p 80:80 --name webserver -e APP=nginx -d nginx:1.21.3
3c629734107cfbdafff3e117c77761e8ebaad7436006a77b9cf69b20214f7cca
```