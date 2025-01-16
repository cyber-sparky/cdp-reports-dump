## Store the Docker Image in the Registry

To store the image, we need a Docker repository called a registry. We can deploy a registry server locally using the following command:

```bash
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```

**Command Output:**
```bash
Unable to find image 'registry:2' locally
2: Pulling from library/registry
cbdbe7a5bc2a: Pull complete
47112e65547d: Pull complete
46bcb632e506: Pull complete
c1cc712bcecd: Pull complete
3db6272dcbfa: Pull complete
Digest: sha256:8be26f81ffea54106bae012c6f349df70f4d5e7e2ec01b143c46e2c03b9e551d
Status: Downloaded newer image for registry:2
946c1af2adf1c8640cc4dc6f6ddd69b44ceb240b34590db2f0703ef134a32863
```

If the image doesn’t exist, Docker will pull it automatically from DockerHub.

**Deploy a registry server:**
[Docker Registry Documentation](https://docs.docker.com/registry/deploying)

After the registry is up, we can push the image(s), but we need to add the image name with the registry URL as a prefix, e.g., `REGISTRY_URL/IMAGE_NAME:IMAGE_TAG`.

```bash
docker tag django.nv:1.0 localhost:5000/django.nv:1.0
```

Then, push the image to the Docker registry:

```bash
docker push localhost:5000/django.nv:1.0
```

You can see the image on the registry using the `curl` command:

```bash
curl localhost:5000/v2/_catalog
```

**Command Output:**
```json
{"repositories":["django.nv"]}
```

As you can see above, the registry has the `django.nv` image. You can consume this image by pulling it from this registry.

## Challenges 

### Use the docker login command on the DevSecOps-Box machine to login into the Docker Hub registry via Command Line Interface (CLI). When prompted for credentials, please use the credentials you just created in the above task 1

```bash
/django.nv# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: nithissh070800
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
/django.nv# 
```

### Push or upload django.nv:1.0 image you created in the previous steps of the exercise to Docker Hub

```bash
/django.nv# docker images 
REPOSITORY                 TAG            IMAGE ID       CREATED         SIZE
django.nv                  1.0            3fb88d266461   8 minutes ago   106MB
localhost:5000/django.nv   1.0            3fb88d266461   8 minutes ago   106MB
registry                   2              6a3edb1d5eb6   9 months ago    25.4MB
python                     3-alpine3.15   c692f3b79c36   20 months ago   51.2MB
/django.nv# docker tag 3fb nithissh070800/django.nv:1.0
/django.nv# docker push -a nithissh070800/django.nv:1.0
tag can't be used with --all-tags/-a
/django.nv# docker push nithissh070800/django.nv:1.0
The push refers to repository [docker.io/nithissh070800/django.nv]
ee07e764b3af: Pushed 
465f6302f900: Pushed 
8a2550f49dd9: Pushed 
0c5c99074fa8: Mounted from library/python 
eb71c8b7b3b7: Mounted from library/python 
76d682e14461: Mounted from library/python 
d59c8eb8f9a4: Mounted from library/python 
34d5ebaa5410: Mounted from library/python 
1.0: digest: sha256:9dd0faeccde3ad03aa6d8e652ee744f6533e1685ed7e9e9cc7894165eaaaf4df size: 1997
```

### Stop the registry container and remove the django.nv:1.0 images to save the disk space

```bash
/django.nv# docker ps -a
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS                    NAMES
688bae7a6c50   registry:2   "/entrypoint.sh /etc…"   12 minutes ago   Up 12 minutes   0.0.0.0:5000->5000/tcp   registry
/django.nv# docker rm 688
Error response from daemon: You cannot remove a running container 688bae7a6c50dfba6936da17923501585b19ad1dfcb454d29924b57d7f16ccfb. Stop the container before attempting removal or force remove
/django.nv# docker stop 688
688
/django.nv# docker rm 688
688
/django.nv# docker rmi django.nv:1.0
Untagged: django.nv:1.0
```