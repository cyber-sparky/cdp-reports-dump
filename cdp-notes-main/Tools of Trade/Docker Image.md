# Cloning and Building a docker image 

## 1. Download the Source Code
Download the source code of the project from the git repository.
```bash
git clone https://gitlab.practical-devsecops.training/pdso/django.nv.git
```

## 2. Navigate to the Project Directory
Change to the project directory to build the Docker image.
```bash
cd django.nv
```

## 3. Build the Docker Image
Ensure you have a Dockerfile with instructions to build the image. View the Dockerfile contents:
```bash
cat Dockerfile
```

### Dockerfile Contents:
```dockerfile
# FROM specify the underlying operating system you will use to build the image
FROM python:3-alpine3.15

# WORKDIR set the default working directory
WORKDIR /app

# COPY startup script
COPY . .

# RUN any required dependencies by running the necessary commands
RUN apk add --no-cache gawk sed bash grep bc coreutils \
    && pip install -r requirements.txt \
    && chmod +x run.sh

# EXPOSE port 8000 for communication to/from server
EXPOSE 8000

# CMD specifies the command to be executed when the container starts
CMD ["/app/run.sh"]
```

### Dockerfile Instructions:
| **Syntax** | **Description**                                           |
|------------|-----------------------------------------------------------|
| FROM       | Initialize an operating system that will be used as a base|
| COPY       | Copy files or directories from the host to the container  |
| WORKDIR    | Sets the working directory, similar to `cd` command on Linux|
| RUN        | Execute commands in the current image                     |

## 4. Create the Docker Image
Use the following command to create the image.
```bash
docker build -t django.nv:1.0 .
```

### Command Output
You should see an output similar to:
```
Sending build context to Docker daemon  3.985MB
Step 1/6 : FROM python:3-alpine3.15
3-alpine3.15: Pulling from library/python
...
Successfully tagged django.nv:1.0
```

## 5. Verify the Docker Image
Check the created image using the following command.
```bash
docker images
```

### Command Output
You should see an output similar to:
```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
django.nv           1.0                 cba79d46fbce        10 seconds ago      115MB
python              2-alpine            8579e446340f        5 months ago        71.1MB
```

## Summary

| **Step**                   | **Command**                                                                                         |
|----------------------------|-----------------------------------------------------------------------------------------------------|
| Clone the repository       | `git clone https://gitlab.practical-devsecops.training/pdso/django.nv.git`                          |
| Change directory           | `cd django.nv`                                                                                      |
| View Dockerfile            | `cat Dockerfile`                                                                                    |
| Build the Docker image     | `docker build -t django.nv:1.0 .`                                                                   |
| Verify the Docker image    | `docker images`                                                                                     |

## Challenges 

### Rename the docker image django.nv:1.0 to django.nv:1.1

```bash
/django.nv# docker images
REPOSITORY   TAG            IMAGE ID       CREATED         SIZE
django.nv    1.0            b6a25ce616b7   3 minutes ago   106MB
python       3-alpine3.15   c692f3b79c36   20 months ago   51.2MB
/django.nv# docker tag
"docker tag" requires exactly 2 arguments.
See 'docker tag --help'.

Usage:  docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
/django.nv# docker tag django.nv:1.0 django.nv:1.1
/django.nv# docker images
REPOSITORY   TAG            IMAGE ID       CREATED         SIZE
django.nv    1.0            b6a25ce616b7   3 minutes ago   106MB
django.nv    1.1            b6a25ce616b7   3 minutes ago   106MB
python       3-alpine3.15   c692f3b79c36   20 months ago   51.2MB
```

### Run a container named webserver with the image django.nv:1.0 and override entrypoint with bash (/bin/bash) shell

In `docker run` we have an argument called `--entrypoint` through which you can overwrite the entrypoint in docker image

```bash
/django.nv# docker run --name webserver -it --entrypoint /bin/bash django.nv:1.0
bash-5.1# whoami
root
bash-5.1# hostname
0722717ae455
bash-5.1# 
```

### Delete the docker image django.nv:1.0

Through `docker rmi` command you can delete a specific docker image by specifying the image and tag 

```bash
/django.nv# docker images
REPOSITORY   TAG            IMAGE ID       CREATED          SIZE
django.nv    1.0            b6a25ce616b7   13 minutes ago   106MB
django.nv    1.1            b6a25ce616b7   13 minutes ago   106MB
python       3-alpine3.15   c692f3b79c36   20 months ago    51.2MB
/django.nv# docker rmi django.nv:1.0
Untagged: django.nv:1.0
/django.nv# docker images
REPOSITORY   TAG            IMAGE ID       CREATED          SIZE
django.nv    1.1            b6a25ce616b7   13 minutes ago   106MB
python       3-alpine3.15   c692f3b79c36   20 months ago    51.2MB
/django.nv# 
```