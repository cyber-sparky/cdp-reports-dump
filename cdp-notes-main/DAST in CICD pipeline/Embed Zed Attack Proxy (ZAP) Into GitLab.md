
## Complete Gitlab workflow of integrating Zap with allow failure 

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

zap-baseline:
  stage: integration
  before_script:
    - docker pull softwaresecurityproject/zap-stable:2.13.0
  script:
    - docker run --user $(id -u):$(id -g) -w /zap -v $(pwd):/zap/wrk:rw --rm softwaresecurityproject/zap-stable:2.13.0 zap-baseline.py -t https://prod-oiahxczk.lab.practical-devsecops.training -J zap-output.json
  after_script:
    - docker rmi softwaresecurityproject/zap-stable:2.13.0  # clean up the image to save the disk space
  artifacts:
    paths: [zap-output.json]
    when: always        # What does this do?
  allow_failure: true  # Optional
```

