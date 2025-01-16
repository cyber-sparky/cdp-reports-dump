
### Embed Retire.js in the test stage with job named oast-frontend. You can make use of any container image of your choice, including the node image.

```yml
oast-frontend:
  stage: test
  image: node:alpine3.10
  script:
    - npm install             # Install the dependencies before scans
    - npm install -g retire   # Install retirejs tool
    - retire --outputformat json --outputpath retirejs-report.json --severity high
```
### Save the output as JSON format with name retirejs-report.json and upload it using artifacts attribute

```yml
 artifacts:
    paths: [retirejs-report.json]
    when: always # What is this for?
    expire_in: one week
  allow_failure: true
```