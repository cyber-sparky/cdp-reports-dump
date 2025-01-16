## Dockerfile Intro

Docker CLI can build images automatically by reading the instructions from a Dockerfile. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image. Using `docker build`, a user can create an automated build that executes several command-line instructions in succession.

Source: [Docker Documentation](https://docs.docker.com/engine/reference/builder/)

First, we need to know the syntax of Dockerfile to create our custom image from scratch. Let’s have a look at the following list:

| Syntax   | Description                                                                                      |
|----------|--------------------------------------------------------------------------------------------------|
| FROM     | Creates a layer from Docker Image. Specifies the base image to use.                              |
| COPY     | Copies the files from the local directory to the docker image on a specific location.            |
| ADD      | Similar to COPY but supports file download (HTTP), auto extracting compressed file(s), and replacing the existing file to a specific location if needed forcefully. |
| RUN      | Run OS command (just like you would do on a terminal) but only executes it during the image creation process. |
| ENTRYPOINT | Run a command as the default command when the container starts. It's the entry point to the utility/command. |
| CMD      | Command to run when a container starts or arguments to ENTRYPOINT if specified.                   |

These are the standard instructions used while creating a docker image.

**ProTip**

We are often asked what is the difference between CMD and ENTRYPOINT. [This Blog post](#) explains the major differences between them.

We will try to build a Docker image with Ubuntu 20.04 as the base image. We will also install a web server in it.

Create a new file called Dockerfile under `learn-dockerfile` directory.

```bash
mkdir learn-dockerfile
cd learn-dockerfile
touch Dockerfile
```

Let's create the Dockerfile using the following command:

```bash
cat > Dockerfile <<EOL
FROM ubuntu:20.04

RUN apt update && apt install -y nginx
EOL
```

As mentioned before, we run the OS command(s) available in Dockerfile to install the Nginx server. Let’s try to build the image.

```bash
docker build -t nginx-custom .
```

**Command Output**

```
Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM ubuntu:20.04
 ---> c090eaba6b94
Step 2/2 : RUN apt update && apt install -y nginx
 ---> Running in bcb4d2b9414e

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

Get:1 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]
Get:2 http://archive.ubuntu.com/ubuntu bionic InRelease [242 kB]
Get:3 http://security.ubuntu.com/ubuntu bionic-security/restricted amd64 Packages [284 kB]
Get:4 http://archive.ubuntu.com/ubuntu bionic-updates InRelease [88.7 kB]
Get:5 http://archive.ubuntu.com/ubuntu bionic-backports InRelease [74.6 kB]

...[SNIP]...

Setting up libnginx-mod-http-image-filter (1.14.0-0ubuntu1.7) ...
Setting up nginx-core (1.14.0-0ubuntu1.7) ...
invoke-rc.d: could not determine current runlevel
invoke-rc.d: policy-rc.d denied execution of start.
Setting up nginx (1.14.0-0ubuntu1.7) ...
Processing triggers for libc-bin (2.27-3ubuntu1.4) ...
Removing intermediate container bcb4d2b9414e
 ---> 6c54510dea11
Successfully built 6c54510dea11
Successfully tagged nginx-custom:latest
```

Great, we managed to create an image with the name `nginx-custom:latest`. To verify the image creation process was successful, we can use the `docker images` command.

```bash
docker images
```

**Command Output**

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx-custom        latest              6c54510dea11        44 seconds ago      159MB
ubuntu              18.04               c090eaba6b94        11 hours ago        63.3MB
```

As you can see above (line number 2), the `nginx-custom` image was created 44 seconds ago

## Working with Containers and Dockerfile


In the previous step, we learned the basic syntax of Dockerfile. Sometimes, it’s faster to create a container using an image as a base and add extra layers on top using the `RUN` commands. Once we are happy with the results, we can create a Dockerfile.

Let’s create a container using the `ubuntu:20.04` image.

```bash
docker run -d --name ubuntu -it ubuntu:20.04
```

Notice the `-d` flag.

Use the `docker exec` command to start the Bash process inside the container (imagine it as though you are logging into a container).

```bash
docker exec -it ubuntu bash
```

Now, we are inside the Ubuntu container. Let’s update the apt cache.

```bash
apt update
```

Then install the nginx package.

```bash
apt install -y nginx
```

After installing Nginx in the DevSecOps Box, configure the timezone to accurately represent the time zone within the container. Type your desired geographic area for the time zone and then press Enter.

Now, we want to start the nginx service.

```bash
service nginx start
```

**Note**: If a pop-up asking for region selection appears, it is necessary to enter a geographic area such as Africa, US, Australia, or any other geographic region.

Great, our web server is running. We can verify it by requesting a webpage from our local container using the curl command.

```bash
curl localhost
```

Oops, looks like curl is not installed. Let’s install the curl utility with the `apt install` command.

```bash
apt install curl -y
```

Then, run the previous command once again, and you will see the welcome page from nginx.

Now, let's create a Dockerfile based on our manual steps.

```Dockerfile
FROM ubuntu:20.04

RUN apt update && apt install nginx -y

CMD ["/bin/bash", "-c", "service nginx start"]
```

Let’s build the Docker image using the above Dockerfile.

```bash
docker build -t custom-nginx .
```

Run the image as a container.

```bash
docker run -d -it --name custom-nginx-container custom-nginx
```

Check running containers.

```bash
docker ps -a
```

As you can see, our container has Exited. Let’s check the logs to ascertain what went wrong using the docker logs command.

```bash
docker logs custom-nginx-container
```

The output shows that nginx was started successfully.

**Note**: The `CMD` instruction in Dockerfile will exit the container right after running the command. To keep the container running, we might need to use a different approach such as running nginx in the foreground or using supervisord.

Reference: [Stack Overflow on Dockerfile CMD Instruction](https://stackoverflow.com/questions/42218957/dockerfile-cmd-instruction-will-exit-the-container-just-after-running-it)

## Advanced Dockerfiles

Let's analyze the Dockerfile commands and container behavior step-by-step:

### Dockerfile 1: Using `CMD` for Service Start and Sleep

```dockerfile
FROM ubuntu:20.04

RUN apt update && apt install nginx -y

CMD ["/bin/bash", "-c" , "service nginx start; sleep infinity"]
```

**Explanation:**
- **`FROM ubuntu:20.04`**: Base image is Ubuntu 20.04.
- **`RUN apt update && apt install nginx -y`**: Updates package lists and installs Nginx.
- **`CMD ["/bin/bash", "-c" , "service nginx start; sleep infinity"]`**:
  - Starts the Nginx service.
  - `sleep infinity` keeps the container running indefinitely.

#### Docker Commands

1. **Build the Docker Image:**
   ```sh
   docker build -t custom-nginx .
   ```

2. **Remove Existing Container:**
   ```sh
   docker rm -f custom-nginx-container
   ```

3. **Run the Docker Image as a Container:**
   ```sh
   docker run -d -it --name custom-nginx-container custom-nginx
   ```

4. **Check Container Status:**
   ```sh
   docker ps -a
   ```

### Dockerfile 2: Using `ENTRYPOINT` and `CMD`

```dockerfile
FROM ubuntu:20.04

RUN apt update && apt install nginx -y

ENTRYPOINT ["/bin/bash", "-c"]

CMD ["service nginx start"]
```

**Explanation:**
- **`ENTRYPOINT ["/bin/bash", "-c"]`**: Sets the command that will be executed when the container starts.
- **`CMD ["service nginx start"]`**: Provides default arguments to the `ENTRYPOINT`.

### Analysis: Why the Container with `CMD` Alone is Not Alive

When using `CMD ["service nginx start"]` without `sleep infinity`:
- The `CMD` instruction runs the `service nginx start` command.
- Once this command completes, there are no active processes keeping the container running.
- Hence, the container stops immediately after starting Nginx because there's no ongoing process to maintain its execution.

### Solution: Running a Shell with the `bash` Command

To keep the container alive for debugging or exploration purposes:
- Run a shell (e.g., `bash`) at the end of the `docker run` command:
  ```sh
  docker run -d --name nginx-one -it custom-nginx bash
  ```
- This starts a new container (`nginx-one`) based on the `custom-nginx` image and runs the `bash` shell.
- The `bash` process keeps the container alive, allowing you to interact with it and investigate its state.

### Conclusion

- Using `ENTRYPOINT` and `CMD` correctly impacts how a container behaves upon startup.
- `CMD` alone, without a persistent process or `sleep infinity`, will not keep the container running unless there's an ongoing process.
- Running a shell (`bash`) in a container can help maintain its execution for debugging or interactive exploration.