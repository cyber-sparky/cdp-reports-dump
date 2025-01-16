
## Workflow file for gitlab to embed synk into gitlab 

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

oast-snyk:
  stage: build
  image: node:alpine3.10
  before_script:
    - wget -O snyk https://github.com/snyk/cli/releases/download/v1.1156.0/snyk-alpine
    - chmod +x snyk
    - mv snyk /usr/local/bin/
  script:
    - npm install
    - snyk auth $SNYK_TOKEN
    - snyk test --json > snyk-results.json
    - cat snyk-results.json
  artifacts:
    paths:
      - snyk-results.json
    when: always
    expire_in: one week

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

## Allow failures 

Setting `allow_failure` to true means even the one job is failed.. it gets allowed and moves to the next job

```yml
oast-snyk:
  stage: build
  image: node:alpine3.10
  before_script:
    - wget -O snyk https://github.com/snyk/cli/releases/download/v1.1156.0/snyk-alpine
    - chmod +x snyk
    - mv snyk /usr/local/bin/
  script:
    - npm install
    - snyk auth $SNYK_TOKEN
    - snyk test --json > snyk-results.json
    - cat snyk-results.json
  artifacts:
    paths:
      - snyk-results.json
    when: always
  allow_failure: true #<--- allow the build to fail but don't mark it as such
```