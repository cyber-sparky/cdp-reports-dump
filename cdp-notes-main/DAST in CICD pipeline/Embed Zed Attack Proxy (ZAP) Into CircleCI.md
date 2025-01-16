
## CircleCI for integrating zip with a allowfailure 

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

  zap_baseline:
    machine: true
    steps:
      - checkout

      - run: |
          docker pull softwaresecurityproject/zap-stable:2.14.0
          docker run --user root --rm -v $(pwd):/zap/wrk:rw -w /zap softwaresecurityproject/zap-stable:2.14.0 zap-baseline.py -t ${PROD_URL} -J zap-output.json || true

      - store_artifacts:
          path: zap-output.json
          destination: zap-artifact

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
      - zap_baseline:
          requires:
            - test
      - prod:
          type: approval
          requires:
            - zap_baseline
```