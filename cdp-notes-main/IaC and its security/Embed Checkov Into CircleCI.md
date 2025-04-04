
## CircleCI workflow for integrating checkov

```yml
jobs:
  build:
    machine: true
    steps:
      - checkout
      - run: echo "This is a build step"

  test:
    machine: true
    steps:
      - checkout
      - run: echo "This is a test step"

  checkov:
    machine: true
    steps:
      - checkout

      - run: 
          command: docker run --rm -w /src -v $(pwd):/src bridgecrew/checkov -d aws -o json | tee checkov-output.json || true

      - store_artifacts:
          path: checkov-output.json
          destination: checkov-artifact
          when: always

  integration:
    machine: true
    steps:
      - checkout
      - run:
          command: |
            echo "This is an integration step"
            exit 1
          when: on_fail         # Even if the job fails, continue to the next stages

  prod:
    machine: true
    steps:
      - checkout
      - run: echo "This is a deploy step"

workflows:
  version: 2
  terraform:
    jobs:
      - checkov
      - test:
          requires:
            - checkov
      - integration:
          requires:
            - test
      - prod:
          type: approval
          requires:
            - integration
```