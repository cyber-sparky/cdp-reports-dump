
## Script to run dependency check 

```sh 
#!/bin/sh

DATA_DIRECTORY="$PWD/data"
REPORT_DIRECTORY="$PWD/reports"

if [ ! -d "$DATA_DIRECTORY" ]; then
  echo "Initially creating persistent directories"
  mkdir -p "$DATA_DIRECTORY"
  chmod -R 777 "$DATA_DIRECTORY"

  mkdir -p "$REPORT_DIRECTORY"
  chmod -R 777 "$REPORT_DIRECTORY"
fi

docker run --rm \
  --volume $(pwd):/src \
  --volume "$DATA_DIRECTORY":/usr/share/dependency-check/data \
  --volume "$REPORT_DIRECTORY":/reports \
  hysnsec/dependency-check \
  --scan /src \
  --format "JSON" \
  --project "Webgoat" \
  --failOnCVSS 4 \
  --out /reports
```

## Gitlab workflow for running the above script

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

odc-backend:
  stage: test
  image: gitlab/dind:latest
  script:
    - chmod +x ./run-depcheck.sh
    - ./run-depcheck.sh
  artifacts:
    paths:
      - reports/dependency-check-report.json
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

## Allow failure 

```yml
odc-backend:
  stage: test
  image: gitlab/dind:latest
  script:
    - chmod +x ./run-depcheck.sh
    - ./run-depcheck.sh
  artifacts:
    paths:
      - reports/dependency-check-report.json
    when: always
    expire_in: one week
  allow_failure: true #<--- allow the build to fail but don't mark it as such
```

## Complete pipeline for integrating dependency check onto the Gitlab 

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

odc-backend:
  stage: test
  image: gitlab/dind:latest
  script:
    - chmod +x ./run-depcheck.sh
    - ./run-depcheck.sh
  artifacts:
    paths:
      - reports/dependency-check-report.json
    when: always
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

