
## Job in circleCI for running semgrep 

```yml
  semgrep:
    machine: true
    steps:
      - checkout
      - run: docker run --rm -v ${PWD}:/src returntocorp/semgrep semgrep --config auto --output semgrep-output.json --json
      - store_artifacts:
          path: semgrep-output.json
          destination: semgrep-artifact
```

## Complete pipeline for running semgrep with Allow failure in CircleCI

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

  semgrep:
    machine: true
    steps:
      - checkout
      - run: docker run --rm -v ${PWD}:/src returntocorp/semgrep semgrep --config auto --output semgrep-output.json --json || true
      - store_artifacts:
          path: semgrep-output.json
          destination: semgrep-artifact

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
      - semgrep:
          requires:
            - test
      - integration:
          requires:
            - semgrep
      - prod:
          type: approval
          requires:
            - integration
```