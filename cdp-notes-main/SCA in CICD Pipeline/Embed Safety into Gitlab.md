
# Challenges 

### Create a job named oast in the test stage using the safety tool. Make sure you are using hysnsec/safety docker image for this task. Understand the use of Docker’s -v (volume mount) flag/option. Ensure you follow the DevSecOps Gospel and best practices while embedding the safety tool.

```yml
oast:
  stage: test
  script:
    # We are going to pull the hysnsec/safety image to run the safety scanner
    - docker pull hysnsec/safety
    # third party components are stored in requirements.txt for python, so we will scan this particular file with safety.
    - docker run --rm -v $(pwd):/src hysnsec/safety check -r requirements.txt --json > oast-results.json
  artifacts:
    paths: [oast-results.json]
    when: always # What does this do?
  allow_failure: true
```