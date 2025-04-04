
## Job for Bundler-audit on github actions 

```yml
  bundler-audit:
    machine: true
    steps:
      - checkout

      - run: docker run --rm -v $(pwd):/src -w /src hysnsec/bundle-audit check --format json --output bundle-audit-output.json

      - store_artifacts:
          path: bundle-audit-output.json
          destination: bundler-audit-artifact
```

## Adding it to Workflow 

```yml
workflows:
  version: 2
  rails:
    jobs:
      - build
      - test:
          requires:
            - build
      - bundler-audit:
          requires:
            - test
      - integration:
          requires:
            - bundler-audit
      - prod:
          type: approval
          requires:
            - integration
```

## Allow Failure 

```yml
  bundler-audit:
    machine: true
    steps:
      - checkout

      - run: 
          command: docker run --rm -v $(pwd):/src -w /src hysnsec/bundle-audit check --format json --output bundle-audit-output.json || true

      - store_artifacts:
          path: bundle-audit-output.json
          destination: bundler-audit-artifact
          when: always
```

## Complete pipeline of integrating bundler-audit into CircleCI

```yml
jobs:
  build:
    steps:
      - checkout
      - run: echo "This is a build step"    # similar to "script" in GitLab

  test:
    steps:
      - checkout
      - run: echo "This is a test step"

  bundler-audit:
    machine: true
    steps:
      - checkout

      - run: 
          command: docker run --rm -v $(pwd):/src -w /src hysnsec/bundle-audit check --format json --output bundle-audit-output.json || true

      - store_artifacts:
          path: bundle-audit-output.json
          destination: bundler-audit-artifact
          when: always

  integration:
    steps:
      - checkout
      - run:
          command: |
            echo "This is an integration step"
            exit 1
          when: always                    # Even if the job fails, continue to the next stages

  prod:
    steps:
      - checkout
      - run: echo "This is a deploy step."

workflows:
  version: 2
  rails:
    jobs:
      - build
      - test:
          requires:
            - build
      - bundler-audit:
          requires:
            - test
      - integration:
          requires:
            - bundler-audit
      - prod:
          type: approval
          requires:
            - integration
```