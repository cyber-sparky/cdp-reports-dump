
## Gitlab workflow for integrating terrascan with allowfailure 

```yml
image: docker:20.10  # To run all jobs in this pipeline, use the latest docker image

services:
  - docker:dind       # To run all jobs in this pipeline, use a docker image that contains a docker daemon running inside (dind - docker in docker). Reference: https://forum.gitlab.com/t/why-services-docker-dind-is-needed-while-already-having-image-docker/43534

stages:
  - validate
  - build
  - test
  - release
  - preprod
  - integration
  - prod

terrascan:
  stage: validate
  image:
    name: accurics/terrascan:latest
    entrypoint: ["/bin/sh", "-c"]
  script:
    - /go/bin/terrascan scan . -o json > terrascan-output.json
  artifacts:
    paths: [terrascan-output.json]
    when: always
  allow_failure: true

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