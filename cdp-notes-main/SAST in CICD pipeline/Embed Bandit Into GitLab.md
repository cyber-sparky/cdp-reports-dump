
## Gitlab workflow for integrating bandit 

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

sast:
  stage: build  # we moved this job from test stage to build stage, by replacing the text test to build
  script:
    # Download bandit docker container
    - docker pull hysnsec/bandit
    # Run docker container, please refer docker security course, if this doesn't make sense to you.
    - docker run --user $(id -u):$(id -g) -v $(pwd):/src --rm hysnsec/bandit -r /src -f json -o /src/bandit-output.json
  artifacts:
    paths: [bandit-output.json]
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
sast:
  stage: build
  script:
    - docker pull hysnsec/bandit  # Download bandit docker container
    # Run docker container, please refer docker security course, if this doesn't make sense to you.
    - docker run --user $(id -u):$(id -g) -v $(pwd):/src --rm hysnsec/bandit -r /src -f json -o /src/bandit-output.json
  artifacts:
    paths: [bandit-output.json]
    when: always
  allow_failure: true   #<--- allow the build to fail but don't mark it as such
```

## Challenges 

### Parser to show the output in pretty format based on severity 

```py
import json
import sys
from termcolor import colored

def parse_bandit_results(input_file):
    with open(input_file, 'r') as file:
        data = json.load(file)
        for result in data['results']:
            color = 'white'
            severity = result['issue_severity']
            if severity == 'HIGH':
                color = 'red'
            elif severity == 'MEDIUM':
                color = 'blue'
            elif severity == 'LOW':
                color = 'yellow'
            
            print(colored(f"{result['test_name']} ({result['test_id']}): {result['issue_text']} in {result['filename']} line {result['line_number']}", color))

if __name__ == "__main__":
    input_file = sys.argv[1]  # Expects a file path as a command-line argument
    parse_bandit_results(input_file)
```

