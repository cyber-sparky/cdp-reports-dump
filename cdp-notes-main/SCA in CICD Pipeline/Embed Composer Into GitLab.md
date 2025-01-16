
## Gitlab workflow for Integrating composer and composer audit 

```yml
image: docker:20.10  # To run all jobs in this pipeline, use the latest docker image

services:
  - docker:dind       # To run all jobs in this pipeline, use a docker image that contains a docker daemon running inside (dind - docker in docker). Reference: https://forum.gitlab.com/t/why-services-docker-dind-is-needed-while-already-having-image-docker/43534

stages:
  - build
  - integration
  - prod

oast-backend:
  stage: build  # we moved this job from test stage to build stage, by replacing the text test to build
  image: php:7.4
  before_script:
    - php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    - php composer-setup.php --install-dir=/usr/local/bin --filename=composer
    - php -r "unlink('composer-setup.php');"
    - apt update
    - apt install unzip
  script:
    - composer install
    - composer audit -f json | tee composer-output.json
  artifacts:
    paths: [composer-output.json]
    when: always

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

## Allow Failure 

```yml
oast-backend:
  stage: build  # we moved this job from test stage to build stage, by replacing the text test to build
  image: php:7.4
  before_script:
    - php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    - php composer-setup.php --install-dir=/usr/local/bin --filename=composer
    - php -r "unlink('composer-setup.php');"
    - apt update
    - apt install unzip
  script:
    - composer install
    - composer audit -f json | tee composer-output.json
  artifacts:
    paths: [composer-output.json]
    when: always
  allow_failure: true #<--- allow the build to fail but don't mark it as such
```

## Complete pipeline of integrating Composer into Gitlab 

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
  script:
    - echo "This is a build step"

test:
  stage: test
  script:
    - echo "This is a test step"

oast-backend:
  stage: build 
  image: php:7.4
  before_script:
    - php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    - php composer-setup.php --install-dir=/usr/local/bin --filename=composer
    - php -r "unlink('composer-setup.php');"
    - apt update
    - apt install unzip
  script:
    - composer install
    - composer audit -f json | tee composer-output.json
  artifacts:
    paths: [composer-output.json]
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