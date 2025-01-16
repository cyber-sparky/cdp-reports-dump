
# Introduction to CI/CD System

**Definition**: A CI/CD system, short for Continuous Integration/Continuous Deployment, automates the software development process. Its goal is to streamline building, testing, and deploying applications, enabling efficient developer workflows and quick, reliable updates.

**Workflow**:
1. **Code Integration**:
   - Developers regularly integrate code changes into a shared repository.
   - This triggers an automatic process.

2. **Build and Test**:
   - The system builds the code and runs tests.
   - It identifies any issues, allowing early detection and prompt resolution of bugs.

3. **Deployment**:
   - Once tests pass, the system automatically deploys the changes.
   - Deployment occurs across various environments like development, testing, and production.
   - Ensures consistent and reliable deployment, minimizing manual effort and errors.

**Tools and Technologies**:
- **Version Control Systems**: e.g., Git.
- **Build Servers**: e.g., Jenkins.
- **Automated Testing Frameworks**.
- **Deployment Automation Tools**.

These tools collaborate to automate different stages of the development and deployment pipeline, enhancing efficiency and maintaining code quality.

# Understanding GitLab CI/CD

We will use GitLab as our CI/CD system because it is popular and easy to use. By learning the basics of YAML files, you can apply this knowledge to different CI/CD systems.

## What is YAML?
- YAML stands for "YAML Ain’t Markup Language."
- It is a human-readable data serialization language.
- Commonly used for configuration files and data storage/transmission.

## Logging into GitLab
**Details:**
- **URL:** [GitLab URL](https://gitlab-ce-oiahxczk.lab.practical-devsecops.training/root/django-nv/-/blob/main/.gitlab-ci.yml)
- **Username:** root
- **Password:** pdso-training

## Viewing the Pipeline
- Visit the provided URL to see the running pipeline.
- Click on the job name to see the output.

## How the Pipeline Works
1. **Environment Setup:** The pipeline starts by creating the environment using a Docker image.
2. **Job Execution:**
   - **Example Log:**
     ```
     Running with gitlab-runner 16.4.0 (4e724e03)
     on gitlab-runner-77k0huze BxQ1DEzn, system ID: s_93fa9ab39c75
     Preparing the "docker" executor 00:43
     Using Docker executor with image docker:20.10 ...
     Pulling docker image gitlab/gitlab-runner-helper:x86_64-latest ...
     ```
   - The runner uses Docker as the executor.

## Understanding Executors
- Executors determine where and how CI/CD jobs are executed.
- GitLab provides different executor options based on your needs.

## Fetching Sources
- The pipeline fetches the sources from the Git repository.
- Example:
  ```
  $ rm -f .git/index.lock
  Fetching changes with git depth set to 20...
  Initialized empty Git repository in /builds/BxQ1DEzn/0/root/django-nv/.git/
  Checking out 8ee7dc23 as detached HEAD (ref is main)...
  ```

## Executing Scripts
- Scripts defined in the `.gitlab-ci.yml` file are executed.
- **Example Script:**
  ```yaml
  image: docker:20.10

  services:
    - docker:dind

  stages:
    - build
    - test
    - release
    - preprod
    - integration
    - prod

  job:
    stage: build
    script:
      - echo "I'm a job"
  ```

## Customizing Scripts
- Edit the `.gitlab-ci.yml` file to add custom commands.
- **Example:**
  ```yaml
  image: docker:20.10

  services:
    - docker:dind

  stages:
    - build
    - test
    - release
    - preprod
    - integration
    - prod

  job:
    stage: build
    script:
      - echo "I'm a job"
      - whoami
      - hostname
  ```
- **Example Output:**
  ```bash
  $ echo "I'm a job"
  I'm a job
  $ whoami
  root
  $ hostname
  runner-bxq1dezn-project-2-concurrent-0
  Job succeeded
  ```

## Verifying Container Execution
- Add the command `cat /proc/self/mountinfo` to verify the job runs in a container.

- **Example Output:**
  ```bash
  $ cat /proc/self/mountinfo
  7975 7553 0:510 / / rw,relatime master:3409 - overlay overlay rw,...
  Job succeeded
  ```

# Running Jobs in Docker Containers

We learned how to use Docker through the command line and different options. Now, we'll apply this knowledge in CI/CD. Understanding the `image` attribute in different scenarios is crucial.

### New CI/CD Pipeline Configuration
Replace the existing CI/CD pipeline by copying the following content:

```yaml
image: docker:20.10

services:
  - docker:dind

stages:
  - build
  - test
  - release
  - preprod
  - integration
  - prod

job:
  stage: build
  image: python:3.6
  script:
    - pip install -r requirements.txt
```

## Committing Changes
1. Click the **Commit changes** button.
2. Go back to the pipeline page to see the output.

### Example Pipeline Output

```plaintext
$ pip install -r requirements.txt
Collecting Django==3.0
  Downloading Django-3.0-py3-none-any.whl (7.4 MB)
Collecting pytz
  Downloading pytz-2023.3.post1-py2.py3-none-any.whl (502 kB)
Collecting sqlparse>=0.2.2
  Downloading sqlparse-0.4.4-py3-none-any.whl (41 kB)
Collecting asgiref~=3.2
  Downloading asgiref-3.4.1-py3-none-any.whl (25 kB)
Collecting typing-extensions
  Downloading typing_extensions-4.1.1-py3-none-any.whl (26 kB)
Installing collected packages: typing-extensions, sqlparse, pytz, asgiref, Django
Successfully installed Django-3.0 asgiref-3.4.1 pytz-2023.3.post1 sqlparse-0.4.4 typing-extensions-4.1.1
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
WARNING: You are using pip version 21.2.4; however, version 21.3.1 is available.
You should consider upgrading via the '/usr/local/bin/python -m pip install --upgrade pip' command.
Job succeeded
```

## Comparing YAML Files

**Previous YAML File:**

```yaml
image: docker:20.10

services:
  - docker:dind

stages:
  - build
  - test
  - release
  - preprod
  - integration
  - prod

job:
  stage: build
  script:
    - echo "I'm a job"
```

**New YAML File:**

```yaml
image: docker:20.10

services:
  - docker:dind

stages:
  - build
  - test
  - release
  - preprod
  - integration
  - prod

job1:
  stage: build
  image: python:3.6
  script:
    - pip install -r requirements.txt

job2:
  stage: build
  script:
    - apk add python3 py3-pip
    - pip install -r requirements.txt
```

## Differences Between job1 and job2
- **job1:**
  - Uses the `image` attribute to specify `python:3.6`.
  - Installs dependencies using `pip install -r requirements.txt`.

- **job2:**
  - Does not define an `image` attribute.
  - Uses the default `image: docker:20.10` defined at the top.
  - Installs `python3` and `pip` using `apk` (Alpine Linux package manager).

## Why Install python3-pip in job2?
- Without the `image` attribute, the job uses the default image (`docker:20.10`).
- The default image is based on Alpine Linux.
- `apk add python3 py3-pip` installs Python and pip, necessary for the script.

### Reference
For more details, refer to [GitLab CI/CD Docker Images](https://docs.gitlab.com/ee/ci/docker/using_docker_images.html#what-is-an-image).

# Docker-in-Docker in GitLab

## What is DinD?
Docker-in-Docker (DinD) allows you to run Docker containers inside a Docker container within your GitLab CI/CD pipeline. This enables full Docker functionality in the pipeline, allowing you to build, test, and deploy applications using Docker commands.

## Benefits of DinD
- **Isolated Environment:** Provides an isolated Docker runtime environment.
- **Replicate Local Setup:** Mimics the Docker environment of your local machine or dedicated Docker host.
- **Versatile Use Cases:** Useful for building and pushing Docker images, running Dockerized tests, and deploying applications using Docker.

## Using DinD in GitLab CI/CD
To use DinD, configure your pipeline job with the Docker image available at [Docker Hub](https://hub.docker.com/_/docker).

### Pipeline Configuration Example
Here is a basic `.gitlab-ci.yml` configuration to use DinD:

```yaml
image: docker:20.10

services:
  - docker:dind

stages:
  - build
  - test
  - release
  - preprod
  - integration
  - prod

job1:
  stage: build
  image: python:3.6
  script:
    - pip install -r requirements.txt

job2:
  stage: build
  script:
    - apk add python3 py3-pip
    - pip install -r requirements.txt
```

### Example: Running Docker Command in Pipeline
Replace the content in your `.gitlab-ci.yml` file with the following to run a Docker command:

```yaml
image: docker:20.10

services:
  - docker:dind

stages:
  - build
  - test
  - release
  - preprod
  - integration
  - prod

job1:
  stage: build
  script:
    - docker version
```

### Example Output of Docker Command

```bash
$ docker version
Client:
 Version:           20.10.24
 API version:       1.41
 Go version:        go1.19.7
 Git commit:        297e128
 Built:             Tue Apr  4 18:17:06 2023
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true
Server: Docker Engine - Community
 Engine:
  Version:          20.10.10
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.9
  Git commit:       e2f740d
  Built:            Mon Oct 25 07:41:08 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.5.10
  GitCommit:        2a1d4dbdb2a1030dc5b01e96fb110a9d9f150ecc
 runc:
  Version:          1.0.3
  GitCommit:        v1.0.3-0-gf46b6ba
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

### Using Safety Tool with DinD
Here’s how you can use the Safety tool to check for vulnerabilities in your `requirements.txt` file:

```yaml
image: docker:20.10

services:
  - docker:dind

stages:
  - build
  - test
  - release
  - preprod
  - integration
  - prod

job1:
  stage: build
  script:
    - docker run --rm -v $(pwd):/src hysnsec/safety check -r /src/requirements.txt --json
```

### Explanation of the Docker Command
| **Command**                      | **Description**                                                                                          |
|----------------------------------|----------------------------------------------------------------------------------------------------------|
| `docker run`                     | Runs a Docker container.                                                                                  |
| `--rm`                           | Automatically removes the container after it finishes running.                                            |
| `-v $(pwd):/src`                 | Sets up a volume mount. Binds the current working directory to the `/src` directory inside the container. |
| `hysnsec/safety`                 | Specifies the Docker image to run.                                                                        |
| `check -r /src/requirements.txt` | Arguments passed to the `safety check` command within the container. Checks the Python packages.          |
| `--json`                         | Outputs the results in JSON format.                                                                       |

### Comparing Two Methods
**Using Docker Image:**
```yaml
job1:
  stage: build
  script:
    - docker run --rm -v $(pwd):/src hysnsec/safety check -r /src/requirements.txt --json
```

**Installing Safety Tool:**
```yaml
job1:
  stage: build
  image: python:3.6
  script:
    - pip install safety
    - safety check -r /src/requirements.txt --json
```

## Shared Runners in GitLab CI/CD

### What are Shared Runners?
Shared Runners in GitLab are resources that can be used by multiple projects to run their CI/CD pipelines. They provide a centralized execution environment, eliminating the need for each project to have dedicated resources.

### Benefits of Shared Runners
- **Centralized Execution:** Allows multiple projects to use the same CI/CD infrastructure.
- **Resource Efficiency:** Utilizes available computing power and resources more effectively.
- **Easy Configuration:** Configured at the GitLab instance level and shared across different projects within that instance.

### Key Features
- **Instance-Level Configuration:** Shared Runners are configured once at the GitLab instance level.
- **Accessibility:** Can be accessed and used by different projects within the same GitLab instance.
- **Scalability:** Leverages the full capabilities of the CI/CD infrastructure, providing a scalable solution for running pipelines.

### Use Cases
- **CI/CD Pipelines:** Run build, test, and deploy jobs for multiple projects.
- **Resource Management:** Efficiently manage and utilize available resources across projects.
- **Consistent Environment:** Provide a consistent and standardized execution environment for all projects.