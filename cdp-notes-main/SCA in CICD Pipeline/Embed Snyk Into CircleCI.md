
## Job for running synk in CircleCI

```yml
  oast-snyk:
    docker:
      - image: node:alpine3.10
    steps:
      - checkout

      - run: 
          command: |
            wget -O snyk https://github.com/snyk/cli/releases/download/v1.1156.0/snyk-alpine
            chmod +x snyk
            mv snyk /usr/local/bin/
            npm install
            snyk auth $SNYK_TOKEN
            snyk test --json > snyk-results.json

      - store_artifacts:
          path: snyk-results.json
          destination: snyk-artifact
```

## Adding the job name to workflow 

```yml
workflows:
  version: 2
  django:
    jobs:
      - build
      - test:
          requires:
            - build
      - oast-snyk:
          requires:
            - test
      - integration:
          requires:
            - oast-snyk
      - prod:
          type: approval
          requires:
            - integration
```

## Allow Job failure

`|| true` syntax to not fail the build even though the tool found security issues.

```yml
 oast-snyk:
    docker:
      - image: node:alpine3.10
    steps:
      - checkout

      - run: 
          command: |
            wget -O snyk https://github.com/snyk/cli/releases/download/v1.1156.0/snyk-alpine
            chmod +x snyk
            mv snyk /usr/local/bin/
            npm install
            snyk auth $SNYK_TOKEN
            snyk test --json > snyk-results.json || true

      - store_artifacts:
          path: snyk-results.json
          destination: snyk-artifact
          when: always
```

## Complete Pipeline of integration in synk 

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

  oast-snyk:
    docker:
      - image: node:alpine3.10
    steps:
      - checkout

      - run: 
          command: |
            wget -O snyk https://github.com/snyk/cli/releases/download/v1.1156.0/snyk-alpine
            chmod +x snyk
            mv snyk /usr/local/bin/
            npm install
            snyk auth $SNYK_TOKEN
            snyk test --json > snyk-results.json || true

      - store_artifacts:
          path: snyk-results.json
          destination: snyk-artifact
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
          when: on_fail         # Even if the job fails, continue to the next stages

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
      - oast-snyk:
          requires:
            - test
      - integration:
          requires:
            - oast-snyk
      - prod:
          type: approval
          requires:
            - integration
```