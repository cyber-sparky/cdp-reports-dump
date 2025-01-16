
## CircleCI workflow for integrating TFLint with allow-failure 

```yml
jobs:
  tflint:
    machine: true
    steps:
      - checkout

      - run: 
          command: docker run --rm -v $(pwd):/data -t ghcr.io/terraform-linters/tflint --chdir=aws -f json | tee tflint-output.json || true

      - store_artifacts:
          path: tflint-output.json
          destination: tflint-artifact
          when: always

  test:
    machine: true
    steps:
      - checkout
      - run: echo "This is a test step"

  integration:
    machine: true
    steps:
      - checkout
      - run:
          command: |
            echo "This is an integration step"
            exit 1

  prod:
    machine: true
    steps:
      - checkout
      - run: echo "This is a deploy step"

workflows:
  version: 2
  terraform:
    jobs:
      - tflint
      - test:
          requires:
            - tflint
      - integration:
          requires:
            - test
      - prod:
          type: approval
          requires:
            - integration
```