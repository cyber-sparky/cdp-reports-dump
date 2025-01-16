
## Setting up a docker registry 

```yml
image: docker:20.10

services:
  - docker:dind

stages:
  - build
  - deploy
  - test

build:
  stage: build
  before_script:
   - echo $CI_REGISTRY_PASSWORD | docker login -u $CI_REGISTRY_USER --password-stdin $CI_REGISTRY
  script:
   - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .  # Build the application into Docker image
   - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA        # Push the image into registry

prod:
  stage: deploy
  image: kroniak/ssh-client:3.6
  environment: production
  needs:
    - build
  only:
    - main
  before_script:
   - mkdir -p ~/.ssh
   - echo "$PROD_SSH_PRIVKEY" > ~/.ssh/id_rsa
   - chmod 600 ~/.ssh/id_rsa
   - eval "$(ssh-agent -s)"
   - ssh-add ~/.ssh/id_rsa
   - ssh-keyscan -t rsa $PROD_HOSTNAME >> ~/.ssh/known_hosts
  script:
   - echo
   - |
      ssh $PROD_USERNAME@$PROD_HOSTNAME << EOF
        docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
        docker rm -f django.nv
        docker run -d --name django.nv -p 8000:8000 $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
      EOF
```

## Setting up a job for tools like nikto, nmap and sslyze 

```yml 
nikto:
  stage: test
  script:
    - docker pull hysnsec/nikto
    - docker run --rm -v $(pwd):/tmp hysnsec/nikto -h http://prod-oiahxczk.lab.practical-devsecops.training -o /tmp/nikto-output.xml
  artifacts:
    paths: [nikto-output.xml]
    when: always

sslyze:
  stage: test
  script:
    - docker pull hysnsec/sslyze
    - docker run --rm -v $(pwd):/tmp hysnsec/sslyze prod-oiahxczk.lab.practical-devsecops.training:443 --json_out /tmp/sslyze-output.json
  artifacts:
    paths: [sslyze-output.json]
    when: always

nmap:
  stage: test
  script:
    - docker pull hysnsec/nmap
    - docker run --rm -v $(pwd):/tmp hysnsec/nmap prod-oiahxczk -oX /tmp/nmap-output.xml
  artifacts:
    paths: [nmap-output.xml]
    when: always
```

## Complete pipeline with a allow failure 

```yml
image: docker:20.10  # To run all jobs in this pipeline, use the latest docker image

services:
  - docker:dind       # To run all jobs in this pipeline, use a docker image that contains a docker daemon running inside (dind - docker in docker). Reference: https://forum.gitlab.com/t/why-services-docker-dind-is-needed-while-already-having-image-docker/43534

stages:
  - build
  - deploy
  - test

build:
  stage: build
  before_script:
   - echo $CI_REGISTRY_PASSWORD | docker login -u $CI_REGISTRY_USER --password-stdin $CI_REGISTRY
  script:
   - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .  # Build the application into Docker image
   - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA        # Push the image into registry

prod:
  stage: deploy
  image: kroniak/ssh-client:3.6
  environment: production
  needs:
    - build
  only:
    - main
  before_script:
   - mkdir -p ~/.ssh
   - echo "$PROD_SSH_PRIVKEY" > ~/.ssh/id_rsa
   - chmod 600 ~/.ssh/id_rsa
   - eval "$(ssh-agent -s)"
   - ssh-add ~/.ssh/id_rsa
   - ssh-keyscan -t rsa $PROD_HOSTNAME >> ~/.ssh/known_hosts
  script:
   - echo
   - |
      ssh $PROD_USERNAME@$PROD_HOSTNAME << EOF
        docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
        docker rm -f django.nv
        docker run -d --name django.nv -p 8000:8000 $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
      EOF

nikto:
  stage: test
  script:
    - docker pull hysnsec/nikto
    - docker run --rm -v $(pwd):/tmp hysnsec/nikto -h http://prod-oiahxczk.lab.practical-devsecops.training -o /tmp/nikto-output.xml
  artifacts:
    paths: [nikto-output.xml]
    when: always
  allow_failure: true

sslscan:
  stage: test
  script:
    - docker pull hysnsec/sslyze
    - docker run --rm -v $(pwd):/tmp hysnsec/sslyze prod-oiahxczk.lab.practical-devsecops.training:443 --json_out /tmp/sslyze-output.json
  artifacts:
    paths: [sslyze-output.json]
    when: always
  allow_failure: true

nmap:
  stage: test
  script:
    - docker pull hysnsec/nmap
    - docker run --rm -v $(pwd):/tmp hysnsec/nmap prod-oiahxczk -oX /tmp/nmap-output.xml
  artifacts:
    paths: [nmap-output.xml]
    when: always
  allow_failure: true
```

