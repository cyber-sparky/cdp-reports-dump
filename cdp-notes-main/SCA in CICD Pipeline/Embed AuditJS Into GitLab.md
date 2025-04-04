
## Sample Workflow to Integrate AuditJS into Gitlab 

```yml
image: node:alpine3.10

cache:
  paths:
  - node_modules/

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
    - npm install

test:
  stage: test
  script:
    - echo "This is an test step"

auditjs:
  image: docker:dind
  stage: test
  script:
    - docker run --rm -v $(pwd):/src -w /src hysnsec/auditjs ossi -q -j | tee auditjs-output.json
  artifacts:
    paths: [auditjs-output.json]
    when: always # What is this for?
    expire_in: one week

integration:
  stage: integration
  script:
    - echo "This is an integration step"

prod:
  stage: prod
  script:
    - echo "This is a deploy step"
```

## Allow Failure 

```yml
auditjs:
  stage: test
  script:
    - docker run --rm -v $(pwd):/src -w /src hysnsec/auditjs ossi -q -j | tee auditjs-output.json
  artifacts:
    paths: [auditjs-output.json]
    when: always # What is this for?
    expire_in: one week
  allow_failure: true  #<--- allow the build to fail but don't mark it as such
```

## Complete Pipeline 

```yml
image: node:alpine3.10

cache:
  paths:
  - node_modules/

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
    - npm install

test:
  stage: test
  script:
    - echo "This is an test step"

auditjs:
  stage: test
  script:
    - docker run --rm -v $(pwd):/src -w /src hysnsec/auditjs ossi -q -j | tee auditjs-output.json
  artifacts:
    paths: [auditjs-output.json]
    when: always # What is this for?
    expire_in: one week
  allow_failure: true  #<--- allow the build to fail but don't mark it as such

integration:
  stage: integration
  script:
    - echo "This is an integration step"

prod:
  stage: prod
  script:
    - echo "This is a deploy step"
```