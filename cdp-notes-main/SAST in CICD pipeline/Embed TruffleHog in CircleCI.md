
## Circle CI workflow for running trufflehog 

```yml
  secret_scanning:
    machine: true
    steps:
      - checkout

      - run: docker run --rm -v $(pwd):/src hysnsec/trufflehog filesystem /src --json > trufflehog-output.json

      - store_artifacts:
          path: trufflehog-output.json
          destination: trufflehog-artifact
```

Then, we can add it to workflow as well 

```yml
workflows:
  version: 2
  django:
    jobs:
      - build
      - test:
          requires:
            - build
      - secret_scanning:
          requires:
            - test
      - integration:
          requires:
            - secret_scanning
      - prod:
          type: approval
          requires:
            - integration
```

## Complete pipeline of integrating trufflehog into cirlceCI along with a allow failure 

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

  secret_scanning:
    machine: true
    steps:
      - checkout

      - run:
          command: docker run --rm -v $(pwd):/src hysnsec/trufflehog filesystem /src --json > trufflehog-output.json || true

      - store_artifacts:
          path: trufflehog-output.json
          destination: trufflehog-artifact
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
      - secret_scanning:
          requires:
            - test
      - integration:
          requires:
            - secret_scanning
      - prod:
          type: approval
          requires:
            - integration
```