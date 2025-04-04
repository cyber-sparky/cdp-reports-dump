
## Particular job in CircleCI for running bandit 

```yml
  sast:
    machine: true
    steps:
      - checkout

      - run: docker run --rm -v $(pwd):/src hysnsec/bandit -r /src -f json -o /src/bandit-output.json

      - store_artifacts:
          path: bandit-output.json
          destination: bandit-artifact
```

Once after defining the job name, add it to workflows 

```yml
workflows:
  version: 2
  django:
    jobs:
      - build
      - test:
          requires:
            - build
      - sast:
          requires:
            - test
      - integration:
          requires:
            - sast
      - prod:
          type: approval
          requires:
            - integration
```

## Complete pipeline for bandit to integrate into circleCI along with allowfailure 

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

  sast:
    machine: true
    steps:
      - checkout

      - run: docker run --rm -v $(pwd):/src hysnsec/bandit -r /src -f json -o /src/bandit-output.json || true

      - store_artifacts:
          path: bandit-output.json
          destination: bandit-artifact
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
          when: on_fail           # Even if the job fails, continue to the next stages

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
      - sast:
          requires:
            - test
      - integration:
          requires:
            - sast
      - prod:
          type: approval
          requires:
            - integration
```