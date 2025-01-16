
## Gitlab workflow for integrating Gosec

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

sast:
  stage: build
  script:
    - docker run --rm -v $(pwd):/src -w /src securego/gosec -fmt json -out gosec-output.json ./...
  artifacts:
    paths: [gosec-output.json]
    when: always

test:
  stage: test
  script:
    - echo "This is a test step"

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

