
## CircleCI workflow for integrating nuclei 

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

  nuclei:
    machine: true
    steps:
      - checkout

      - run: |
          docker run --user $(id -u):$(id -g) -w /nuclei -v $(pwd):/nuclei:rw --rm projectdiscovery/nuclei:v2.9.6 -u https://prod-oiahxczk.lab.practical-devsecops.training -j -o nuclei-output.json

      - store_artifacts:
          path: nuclei-output.json
          destination: nuclei-artifact

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
      - nuclei:
          requires:
            - test
      - prod:
          type: approval
          requires:
            - nuclei
```