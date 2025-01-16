
## Simple workflow in CI/CD workflow 

```yml
jobs:
  build:
    docker:
      - image: python:3.6                   # similar to "image" in GitLab
    steps:
      - checkout
      - run: |                              # similar to "script" in GitLab
          pip install -r requirements.txt
          python manage.py check

  test:
    docker:
      - image: python:3.6
    steps:
      - checkout
      - run: |
          pip install -r requirements.txt
          python manage.py test taskManager

  integration:
    docker:
      - image: python:3.6
    steps:
      - checkout
      - run:
          command: |
            echo "This is an integration step"
            exit 1

  prod:
    docker:
      - image: python:3.6
    steps:
      - checkout
      - run: echo "This is a deploy step."

workflows:
  version: 2
  django:
    jobs:
      - build
      - test:
          requires:
            - build 
      - integration:
          requires:
            - test
      - prod:
          type: approval
          requires:
            - integration
```

## Adding safety in our CI/CD

```yml
oast:
  machine: true
  steps:
    - checkout
    - run: docker run -v $(pwd):/src --rm hysnsec/safety check -r /src/requirements.txt --json | tee oast-results.json
    - store_artifacts:
        path: oast-results.json
        destination: safety-artifact
```

## Integrating safety in our workflow 

```yml
workflows:
  version: 2
  django:
    jobs:
      - build
      - test:
          requires:
            - build
      - oast:
          requires:
            - test
      - integration:
          requires:
            - oast
      - prod:
          type: approval
          requires:
            - integration
```

## Allow Failure 

1. **Purpose**:
   - In the early stages of security testing (DevSecOps Maturity Levels 1 and 2), allow jobs to fail without failing the entire build. This approach accommodates the inherent uncertainty of security scans, such as false positives.

2. **Using `|| true` in CircleCI**:
   - Append `|| true` to commands to ensure that even if the command returns an error, it will not cause the job to fail. This is useful when you do not want to halt the pipeline due to errors that are not critical.

3. **CircleCI Job Configuration for `oast`**:
   - Configure the `oast` job to perform a safety check and capture the output, while ensuring it does not cause the build to fail if the tool finds security issues:
     ```yaml
     oast:
       machine: true
       steps:
         - checkout
         - run:
             command: docker run -v $(pwd):/src --rm hysnsec/safety check -r /src/requirements.txt --json | tee oast-results.json || true
         - store_artifacts:
             path: oast-results.json
             destination: safety-artifact
             when: always
     ```

4. **Full Pipeline Configuration**:
   - The complete pipeline includes other stages such as build, test, integration, and production. Here’s how the entire configuration would look:
     ```yaml
     jobs:
       build:
         docker:
           - image: python:3.6
         steps:
           - checkout
           - run: |
               pip install -r requirements.txt
               python manage.py check

       test:
         docker:
           - image: python:3.6
         steps:
           - checkout
           - run: |
               pip install -r requirements.txt
               python manage.py test taskManager

       oast:
         machine: true
         steps:
           - checkout
           - run:
               command: docker run -v $(pwd):/src --rm hysnsec/safety check -r /src/requirements.txt --json | tee oast-results.json || true
           - store_artifacts:
               path: oast-results.json
               destination: safety-artifact
               when: always

       integration:
         docker:
           - image: python:3.6
         steps:
           - checkout
           - run:
               command: |
                 echo "This is an integration step"
                 exit 1
               when: on_fail

       prod:
         docker:
           - image: python:3.6
         steps:
           - checkout
           - run: echo "This is a deploy step"

     workflows:
       version: 2
       django:
         jobs:
           - build
           - test:
               requires:
                 - build
           - oast:
               requires:
                 - test
           - integration:
               requires:
                 - oast
           - prod:
               type: approval
               requires:
                 - integration
     ```

5. **Monitoring and Execution**:
   - Changes to the repository automatically trigger the pipeline. You can monitor and review the pipeline's results in your CircleCI account by navigating to Projects, selecting the `django.nv` repository, and viewing the desired pipeline output.