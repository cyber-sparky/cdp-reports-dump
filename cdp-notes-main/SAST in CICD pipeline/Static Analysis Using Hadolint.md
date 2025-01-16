
## Introduction about this tool

Hadolint checks a Dockerfile for Docker image best practices. The linter is parsing the Dockerfile into an AST and performs rules on top of the AST.

## Installing Hadolint 

```sh
wget https://github.com/hadolint/hadolint/releases/download/v1.18.0/hadolint-Linux-x86_64
mv hadolint-Linux-x86_64 /usr/local/bin/hadolint
chmod +x /usr/local/bin/hadolint
```

## Running the scan 

Running the scan revealed some sort of error in the dockerfile in line 9

```sh
/webapp# hadolint Dockerfile
Dockerfile:11 DL3018 Pin versions in apk add. Instead of `apk add <package>` use `apk add <package>=<version>`
```

## Fixing the result from hadolint 

we have to provide the package with an actual version of that package but here we have mentioned the package directly without the version 

Inspecting the dockerfile shows:

```sh
/webapp# cat -n Dockerfile
     1  # FROM specify the underlying operating system you will use to build the image
     2  FROM python:3-alpine3.15
     3
     4  # WORKDIR set the default working directory
     5  WORKDIR /app
     6
     7  # COPY startup script
     8  COPY . .
     9
    10  # RUN any required dependencies by running the necessary commands
    11  RUN apk add --no-cache gawk sed bash grep bc coreutils \
    12      && pip install -r requirements.txt \
    13      && chmod +x run.sh
    14
    15  # EXPOSE port 8000 for communication to/from server
    16  EXPOSE 8000
    17
    18  # CMD specifies the command to be executed when the container starts
    19  CMD ["/app/run.sh"]
```

Replace with this will actually gets fixed 

```sh
RUN apk add --no-cache gawk=5.0.1-r0 sed=4.7-r0 bash=5.0.11-r1 grep=3.3-r0 bc=1.07.1-r1 coreutils=8.31-r0
```