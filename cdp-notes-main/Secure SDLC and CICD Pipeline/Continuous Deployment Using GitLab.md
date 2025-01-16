
# Simple CI/CD Pipeline

In a thin line, This pipeline carries multiple jobs and the key part is it is utilising the docker in the docker as a service and docker with version 20.10 as base image here 

```yml
image: docker:20.10  # To run all jobs in this pipeline, use the latest docker image

services:
  - docker:dind       # To run all jobs in this pipeline, use a docker image that contains a docker daemon running inside (dind - docker in docker). Reference: https://forum.gitlab.com/t/why-services-docker-dind-is-needed-while-already-having-image-docker/43534

stages:
  - build
  - test
  - release
  - preprod
  - integration
  - prod

build:
  stage: build
  image: python:3.6
  before_script:
   - pip3 install --upgrade virtualenv
  script:
   - virtualenv env                       # Create a virtual environment for the python application
   - source env/bin/activate              # Activate the virtual environment
   - pip install -r requirements.txt      # Install the required third party packages as defined in requirements.txt
   - python manage.py check               # Run checks to ensure the application is working fine

test:
  stage: test
  image: python:3.6
  before_script:
   - pip3 install --upgrade virtualenv
  script:
   - virtualenv env
   - source env/bin/activate
   - pip install -r requirements.txt
   - python manage.py test taskManager

integration:
  stage: integration
  script:
    - echo "This is an integration step"
    - exit 1
  allow_failure: true # Even if the job fails, continue to the next stages

prod:
  stage: prod
  script:
    - echo "This is a deploy step."
  when: manual # Continuous Delivery
  ```

# Build Docker images in CI/CD pipeline

## Basic Syntax of `.gitlab-ci.yml`
- Continuous Delivery involves human approval.
- Continuous Deployment is automatic.
- Use `when: manual` in GitLab CI to create a continuous delivery pipeline. Jobs with a manual tag require manual initiation.

### Example `.gitlab-ci.yml` File
```yaml
image: docker:20.10  # Use the latest Docker image for all jobs

services:
  - docker:dind  # Docker-in-Docker for running Docker commands

stages:
  - build
  - test
  - release
  - preprod
  - integration
  - prod

build:
  stage: build
  image: python:3.6
  before_script:
    - pip3 install --upgrade virtualenv
  script:
    - virtualenv env
    - source env/bin/activate
    - pip install -r requirements.txt
    - python manage.py check

test:
  stage: test
  image: python:3.6
  before_script:
    - pip3 install --upgrade virtualenv
  script:
    - virtualenv env
    - source env/bin/activate
    - pip install -r requirements.txt
    - python manage.py test taskManager

integration:
  stage: integration
  script:
    - echo "This is an integration step"
    - exit 1
  allow_failure: true  # Continue to next stages even if this job fails

prod:
  stage: prod
  script:
    - echo "This is a deploy step."
  when: manual  # Manual initiation required
```

### Key Points:
- **Stages:** Define the steps in the pipeline (e.g., build, test, release, etc.).
- **Images:** Specify Docker images to run the jobs.
- **Scripts:** Define the commands to run in each job.
- **Manual Jobs:** Use `when: manual` for jobs requiring manual execution.

### Running Tools:
- **Native Installation:** Direct installation on the system.
- **Package Manager/Binary:** Installing via package manager or using binary files.
- **Docker:** Running the tool within a Docker container is recommended for CI/CD.

### Adding a Release Job
To containerize and release the application, add a `release` job after the `test` job:
```yaml
release:
  stage: release
  script:
    - docker build -t $CI_REGISTRY_IMAGE .  # Build Docker image
    - docker push $CI_REGISTRY_IMAGE        # Push image to registry
```

- **Predefined Variables:** GitLab provides predefined variables like `$CI_REGISTRY_IMAGE` for authentication.

### Fixing Access Issues
If the release job fails with `denied: access forbidden`, log into the registry with correct credentials:
```yaml
release:
  stage: release
  before_script:
    - echo $CI_REGISTRY_PASSWORD | docker login -u $CI_REGISTRY_USER --password-stdin $CI_REGISTRY
  script:
    - docker build -t $CI_REGISTRY_IMAGE .  # Build Docker image
    - docker push $CI_REGISTRY_IMAGE        # Push image to registry
```

### Note:
- **Predefined Variables:** Use GitLab's predefined variables for registry authentication, avoiding manual secret addition.

### Checking Results
- **Pipeline Status:** Once the pipeline completes, check if the release job passed.
- **Image Verification:** Verify the pushed image under `Packages & Registries` → `Container Registry` in your GitLab project.

# Deploy Application From CI/CD Pipeline

1. **Set Up DAST**: Running application needed on staging/production machine.

2. **Environment Variables**:
   - **PROD_USERNAME**: Username for production.
   - **PROD_HOSTNAME**: Hostname for production.
   - **PROD_SSH_PRIVKEY**: SSH private key for production.

3. **SSH Setup**:
   - SSH key location: `/root/.ssh/id_rsa`.
   - Command to view key: `more /root/.ssh/id_rsa`.

4. **Modify `.gitlab-ci.yml` for Prod Job**:
   ```yaml
   prod:
     stage: prod
     image: kroniak/ssh-client:3.6
     environment: production
     only:
       - main
     before_script:
      - mkdir -p ~/.ssh
      - echo "$PROD_SSH_PRIVKEY" > ~/.ssh/id_rsa
      - chmod 600 ~/.ssh/id_rsa
      - eval "$(ssh-agent -s)"
      - ssh-add ~/.ssh/id_rsa
      - ssh-keyscan -t rsa $PROD_HOSTNAME >> ~/.ssh/known_hosts
     script:
      - echo
      - |
         ssh $PROD_USERNAME@$PROD_HOSTNAME << EOF
           docker login -u ${CI_REGISTRY_USER} -p ${CI_REGISTRY_PASS} ${CI_REGISTRY}
           docker rm -f django.nv
           docker run -d --name django.nv -p 8000:8000 $CI_REGISTRY_IMAGE
         EOF
   ```

5. **Pipeline Stages**:
   - **Stages**: build, test, release, preprod, integration, prod.
   - **Release Job**:
     ```yaml
     release:
       stage: release
       before_script:
        - echo $CI_REGISTRY_PASSWORD | docker login -u $CI_REGISTRY_USER --password-stdin $CI_REGISTRY
       script:
        - docker build -t $CI_REGISTRY_IMAGE .  # Build Docker image
        - docker push $CI_REGISTRY_IMAGE        # Push image to registry
     ```

6. **Deployment**:
   - **Prod Job**:
     ```yaml
     prod:
       stage: prod
       image: kroniak/ssh-client:3.6
       environment: production
       only:
         - main
       before_script:
        - mkdir -p ~/.ssh
        - echo "$PROD_SSH_PRIVKEY" > ~/.ssh/id_rsa
        - chmod 600 ~/.ssh/id_rsa
        - eval "$(ssh-agent -s)"
        - ssh-add ~/.ssh/id_rsa
        - ssh-keyscan -t rsa $PROD_HOSTNAME >> ~/.ssh/known_hosts
       script:
        - echo
        - |
           ssh $PROD_USERNAME@$PROD_HOSTNAME << EOF
             docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
             docker rm -f django.nv
             docker run -d --name django.nv -p 8000:8000 $CI_REGISTRY_IMAGE
           EOF
     ```

7. **Check Results**:
   - Visit pipeline results: [GitLab CI Pipeline](https://gitlab-ce-oiahxczk.lab.practical-devsecops.training/root/django-nv/pipelines).
   - Verify running application: Log in to Task Manager with credentials:
     - **Username**: admin
     - **Password**: admin