
## Gitlab workflow for running bundle-audit 

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

build:
  stage: build
  script:
    - echo "This is a build step"

test:
  stage: test
  script:
    - echo "This is a test step"

bundler-audit:
  stage: test
  script:
    - docker run --rm -v $(pwd):/src -w /src hysnsec/bundle-audit check --format json --output bundle-audit-output.json
  artifacts:
    paths: [bundle-audit-output.json]
    when: always # What is this for?
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
    - echo "This is a deploy step"
  when: manual # Continuous Delivery
```

## Allow Failure 

```yml
bundler-audit:
  stage: test
  script:
    - docker run --rm -v $(pwd):/src -w /src hysnsec/bundle-audit check --format json --output bundle-audit-output.json
  artifacts:
    paths: [bundle-audit-output.json]
    when: always # What is this for?
    expire_in: one week
  allow_failure: true  #<--- allow the build to fail but don't mark it as such
```

## Complete pipeline of integrating bundler-audit 

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

build:
  stage: build
  script:
    - echo "This is a build step"

test:
  stage: test
  script:
    - echo "This is a test step"

bundler-audit:
  stage: test
  script:
    - docker run --rm -v $(pwd):/src -w /src hysnsec/bundle-audit check --format json --output bundle-audit-output.json
  artifacts:
    paths: [bundle-audit-output.json]
    when: always # What is this for?
    expire_in: one week
  allow_failure: true  #<--- allow the build to fail but don't mark it as such

integration:
  stage: integration
  script:
    - echo "This is an integration step"
    - exit 1
  allow_failure: true # Even if the job fails, continue to the next stages

prod:
  stage: prod
  script:
    - echo "This is a deploy step"
  when: manual # Continuous Delivery
```