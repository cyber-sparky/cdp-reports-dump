
## Gitlab workflow for integrating nuclei with a allowfailure 

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

nuclei:
  stage: integration
  script:
    - docker run --user $(id -u):$(id -g) -w /nuclei -v $(pwd):/nuclei:rw --rm projectdiscovery/nuclei:v2.9.6 -u https://prod-oiahxczk.lab.practical-devsecops.training -j -o nuclei-output.json
  artifacts:
    paths: [nuclei-output.json]
    when: always 
  allow_failure: true
```