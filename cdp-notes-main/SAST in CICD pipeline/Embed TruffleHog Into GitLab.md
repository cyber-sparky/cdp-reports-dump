
## Gitlab workflow for running trufflehog 

```yml
image: docker:20.10  # To run all jobs in this pipeline, use the latest docker image

services:
  - docker:dind       # To run all jobs in this pipeline, use a docker image which contains a docker daemon running inside (dind - docker in docker). Reference: https://forum.gitlab.com/t/why-services-docker-dind-is-needed-while-already-having-image-docker/43534

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

git-secrets:
  stage: build
  script:
    - docker pull hysnsec/trufflehog
    - docker run --user $(id -u):$(id -g) -v $(pwd):/src --rm hysnsec/trufflehog filesystem /src --fail
```

## Checking on where exactly the scan is being performed 

Using `git status` it is possible to check where the secret scanning being peformed whether it is on the particular commit or branch 

We can implement the same in our workflow 

```yml
git-secrets:
  stage: build
  script:
    - apk add git
    - docker pull hysnsec/trufflehog
    - docker run --user $(id -u):$(id -g) -v $(pwd):/src --rm hysnsec/trufflehog filesystem /src --fail
    - git status
```

## Allow Failure 

```yml
git-secrets:
  stage: build
  script:
    - docker run -v $(pwd):/src --rm hysnsec/trufflehog filesystem /src --fail --json | tee trufflehog-output.json
  artifacts:
    paths: [trufflehog-output.json]
    when: always  # What is this for?
    expire_in: one week
  allow_failure: true   #<--- allow the build to fail but don't mark it as such
```

## Final pipeline that integrates trufflehog and allows the job to fail 

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

git-secrets:
  stage: build
  script:
    - docker run -v $(pwd):/src --rm hysnsec/trufflehog filesystem /src --fail --json | tee trufflehog-output.json
  artifacts:
    paths: [trufflehog-output.json]
    when: always  # What is this for?
    expire_in: one week
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