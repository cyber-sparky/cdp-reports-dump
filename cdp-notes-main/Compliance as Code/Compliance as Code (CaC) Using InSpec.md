
- Chef InSpec is an open-source framework for testing and auditing your applications and infrastructure. 
- Chef InSpec works by comparing the actual state of your system with the desired state that you express in easy-to-read and easy-to-write Chef InSpec code. 
- Chef InSpec detects violations and displays findings in the form of a report, but puts you in control of remediation. Installing 

## Installing Inspec

```sh
wget https://packages.chef.io/files/stable/inspec/5.22.29/ubuntu/18.04/inspec_5.22.29-1_amd64.deb
dpkg -i inspec_5.22.29-1_amd64.deb
```

## Run the Inspec profile

```sh
# This command prevents from SSH from prompts like yes/no
echo "StrictHostKeyChecking accept-new" >> ~/.ssh/config

# Running against prod server 
inspec exec https://github.com/dev-sec/linux-baseline.git -t ssh://root@prod-oiahxczk -i ~/.ssh/id_rsa --chef-license accept


    # The first parameter tells the Inspec profile that we need to run against the server
    # -t tells the target machine
    # -i flag used to specify the ssh-key since we are using login in via ssh
    # --chef-license accept tells that we are accepting license this commands prevent the inspec from prompting YES or NO question
```

## Challenges 

### Use hysnsec/inspec docker image to perform continuous compliance scanning with https://github.com/dev-sec/linux-baseline profile and embed it as part of the prod stage with job name as inspec

```yml
inspec:
  stage: prod
  before_script:
    - mkdir -p ~/.ssh
    - echo "$DEPLOYMENT_SERVER_SSH_PRIVKEY" | tr -d '\r' > ~/.ssh/id_rsa
    - chmod 600 ~/.ssh/id_rsa
    - eval "$(ssh-agent -s)"
    - ssh-add ~/.ssh/id_rsa
    - ssh-keyscan -t rsa $DEPLOYMENT_SERVER >> ~/.ssh/known_hosts
  script:
    - docker run --rm -v ~/.ssh:/root/.ssh -v $(pwd):/share hysnsec/inspec exec https://github.com/dev-sec/linux-baseline.git -t ssh://root@$DEPLOYMENT_SERVER -i ~/.ssh/id_rsa --chef-license accept
```

### The job should only be triggered when changes are pushed to the main branch (don’t use rules attribute)

Just add `only:main` to existing workflow as follows 

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

job:
  stage: build
  script:
    - echo "I'm a job"

inspec:
  stage: prod
  only:
    main
  before_script:
    - mkdir -p ~/.ssh
    - echo "$DEPLOYMENT_SERVER_SSH_PRIVKEY" | tr -d '\r' > ~/.ssh/id_rsa
    - chmod 600 ~/.ssh/id_rsa
    - eval "$(ssh-agent -s)"
    - ssh-add ~/.ssh/id_rsa
    - ssh-keyscan -t rsa $DEPLOYMENT_SERVER >> ~/.ssh/known_hosts
  script:
    - docker run --rm -v ~/.ssh:/root/.ssh -v $(pwd):/share hysnsec/inspec exec https://github.com/dev-sec/linux-baseline.git -t ssh://root@$DEPLOYMENT_SERVER -i ~/.ssh/id_rsa --chef-license accept
```

### Save the output as JSON file at /share/inspec-output.json and upload it using artifacts attribute

```yml
inspec:
  stage: prod
  only:
    - main
  environment: production
  before_script:
    - mkdir -p ~/.ssh
    - echo "$DEPLOYMENT_SERVER_SSH_PRIVKEY" | tr -d '\r' > ~/.ssh/id_rsa
    - chmod 600 ~/.ssh/id_rsa
    - eval "$(ssh-agent -s)"
    - ssh-add ~/.ssh/id_rsa
    - ssh-keyscan -t rsa $DEPLOYMENT_SERVER >> ~/.ssh/known_hosts
  script:
    - docker run --rm -v ~/.ssh:/root/.ssh -v $(pwd):/share hysnsec/inspec exec https://github.com/dev-sec/linux-baseline.git -t ssh://root@$DEPLOYMENT_SERVER -i ~/.ssh/id_rsa --chef-license accept --reporter json:/share/inspec-output.json
  artifacts:
    paths: [inspec-output.json]
    when: always
```

