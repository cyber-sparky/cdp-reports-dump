
## Embed OSV Scanner In CI/CD Pipeline

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

osv-scanner:
  stage: test
  image: golang:1.21.1-alpine3.18
  before_script:
    - apk add npm
    - npm install
  script:
    - go install github.com/google/osv-scanner/cmd/osv-scanner@v1
    - osv-scanner .

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

## Getting the output 

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

osv-scanner:
  stage: test
  image: golang:1.21.1-alpine3.18
  before_script:
    - apk add npm
    - npm install
  script:
    - go install github.com/google/osv-scanner/cmd/osv-scanner@v1
    - osv-scanner --json . > osv-report.json
  artifacts:
    paths: [osv-report.json] # Output in JSON format
    when: always 

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

## Allow Job failure 

```yml
osv-scanner:
  stage: test
  image: golang:1.21.1-alpine3.18
  before_script:
    - apk add npm
    - npm install
  script:
    - go install github.com/google/osv-scanner/cmd/osv-scanner@v1
    - osv-scanner --json . > osv-report.json
  artifacts:
    paths: [osv-report.json]
    when: always 
  allow_failure: true  #<--- allow the build to fail but don't mark it as such
```

## Final Pipeline of OSV scanner that is integrated with gitlab 

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

osv-scanner:
  stage: test
  image: golang:1.21.1-alpine3.18
  before_script:
    - apk add npm
    - npm install
  script:
    - go install github.com/google/osv-scanner/cmd/osv-scanner@v1
    - osv-scanner --json . > osv-report.json
  artifacts:
    paths: [osv-report.json]
    when: always 
  allow_failure: true

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