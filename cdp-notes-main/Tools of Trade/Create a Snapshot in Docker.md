# Docker Save vs Docker Export

**Running a Container:**
```sh
docker run -d --name ubuntu -i ubuntu:20.04
```
- **Description:** Runs a container named `ubuntu` from the `ubuntu:20.04` image.

**Executing Commands in the Container:**
```sh
docker exec -it ubuntu bash
apt update && apt install -y nginx
exit
```

**Creating an Image Snapshot:**
```sh
docker save ubuntu > ubuntu-save.tar
```
- **Description:** Saves the `ubuntu` image into a file named `ubuntu-save.tar`.

**Exporting a Container Snapshot:**
```sh
docker export ubuntu > ubuntu-export.tar
```
- **Description:** Exports the `ubuntu` container into a file named `ubuntu-export.tar`.

**Viewing File Sizes:**
```sh
ls -l --block-size=M
```
- **Output:**
```sh
-rw-r--r-- 1 root root 157M Dec 13 00:14 ubuntu-export.tar
-rw-r--r-- 1 root root 63M Dec 13 00:14 ubuntu-save.tar
```

**Importing a Snapshot:**
```sh
docker load -i ubuntu-save.tar
```
- **Output:**
```sh
Loaded image: ubuntu:20.04
```

```sh
docker import ubuntu-export.tar ubuntu-export
```
- **Output:**
```sh
sha256:2c3ddfbb51d9ee94371f1dd496a2e037ea291bf3027856134f9effb5f5f73620
```

**Viewing Images:**
```sh
docker images
```
- **Output:**
```sh
REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
ubuntu-export   latest    2c3ddfbb51d9   2 seconds ago    161MB
ubuntu          20.04     5a214d77f5d7   2 months ago     63.1MB
```

**Running the Exported Image:**
```sh
docker run -d --name ubuntu -i ubuntu-export
```
- **Error:**
```sh
docker: Error response from daemon: No command specified.
See 'docker run --help'.
```

**Checking Image History:**
```sh
docker history ubuntu-export
```
- **Output:**
```sh
2c3ddfbb51d9   5 minutes ago                161MB     Imported from -
```

**Comparing with Original Image History:**
```sh
docker history ubuntu:20.04
```
- **Output:**
```sh
IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
5a214d77f5d7   2 months ago   /bin/sh -c #(nop)  CMD ["bash"]                 0B
<missing>      2 months ago   /bin/sh -c #(nop) ADD file:0d82cd095966e8ee7…   63.1MB
```

**Notes:**
- `docker export` flattens the image and doesn't save USER, EXPOSE, RUN, and other Dockerfile syntax.
- `docker save` stores image layers and metadata.