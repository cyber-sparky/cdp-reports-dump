
## Complete pipeline that integrates PHPstan into Gitlab CI/CD with allow failure 

```yml
image: docker:20.10  # To run all jobs in this pipeline, use the latest docker image

services:
  - docker:dind       # To run all jobs in this pipeline, use a docker image that contains a docker daemon running inside (dind - docker in docker). Reference: https://forum.gitlab.com/t/why-services-docker-dind-is-needed-while-already-having-image-docker/43534

stages:
  - build
  - integration
  - prod

sast:
  stage: build
  script:
    # Download PHPStan docker container
    - docker pull phpstan/phpstan
    # Run docker container, please refer docker security course, if this doesn't make sense to you.
    - docker run -v $(pwd):/src --rm phpstan/phpstan analyse --error-format=json /src | tee phpstan-output.json
  artifacts:
    paths: [phpstan-output.json]
    when: always
  allow_failure: true   #<--- allow the build to fail but don't mark it as such

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