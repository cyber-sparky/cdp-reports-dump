
## Gitlab workflow for integrating trivy 

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

trivy:
  stage: test
  script:
    - docker run --rm -v $(pwd):/src hysnsec/trivy fs . --exit-code 1 -f json -o trivy-report.json
  artifacts:
    paths: [trivy-report.json]
    when: always # What is this for?
    expire_in: one week

integration:
  stage: integration
  script:
    - echo "This is an integration step."
    - exit 1
  allow_failure: true # Even if the job fails, continue to the next stages

prod:
  stage: prod
  script:
    - echo "This is a deploy step."
  when: manual # Continuous Delivery
```

## Allow Failure 

```yml
trivy_scanning:
  stage: test
  script:
    - docker run --rm -v $(pwd):/src hysnsec/trivy fs . --exit-code 1 -f json -o trivy-report.json
  artifacts:
    paths: [trivy-report.json]
    when: always # What is this for?
    expire_in: one week
  allow_failure: true   #<--- allow the build to fail but don't mark it as such
```

##  Complete pipeline that integrates trivy with a allow failure 

```yml
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

trivy:
  stage: test
  script:
    - docker run --rm -v $(pwd):/src hysnsec/trivy fs . --exit-code 1 -f json -o trivy-report.json
  artifacts:
    paths: [trivy-report.json]
    when: always # What is this for?
    expire_in: one week
  allow_failure: true   #<--- allow the build to fail but don't mark it as such

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