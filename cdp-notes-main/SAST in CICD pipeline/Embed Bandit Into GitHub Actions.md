
## Particular job of Github Actions for running bandit 

```yml
  sast:
    runs-on: ubuntu-20.04
    needs: build
    steps:
      - uses: actions/checkout@v2

      - run: docker run --rm -v $(pwd):/src hysnsec/bandit -r /src -f json -o /src/bandit-output.json

      - uses: actions/upload-artifact@v2
        with:
          name: Bandit
          path: bandit-output.json
        if: always()              
```

## Complete pipeline for integrating bandit into github actions pipeline with allow failure 

```yml
name: Django                                  # workflow name

on:
  push:
    branches:                                 # similar to "only" in GitLab
      - main

jobs:
  build:
    runs-on: ubuntu-20.04                    # similar to "image" in GitLab
    steps:
      - uses: actions/checkout@v2

      - name: Setup python
        uses: actions/setup-python@v2
        with:
          python-version: '3.6'

      - run: |
          pip3 install --upgrade virtualenv
          virtualenv env
          source env/bin/activate
          pip install -r requirements.txt
          python manage.py check

  test:
    runs-on: ubuntu-20.04
    needs: build
    steps:
      - uses: actions/checkout@v2

      - name: Setup python
        uses: actions/setup-python@v2
        with:
          python-version: '3.6'

      - run: |
          pip3 install --upgrade virtualenv
          virtualenv env
          source env/bin/activate
          pip install -r requirements.txt
          python manage.py test taskManager

  sast:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - uses: actions/checkout@v2

      - run: docker run --rm -v $(pwd):/src hysnsec/bandit -r /src -f json -o /src/bandit-output.json
        continue-on-error: true             # allow the build to fail, similar to allow_failure: true

      - uses: actions/upload-artifact@v2
        with:
          name: Bandit
          path: bandit-output.json
        if: always()                        # what is this for?

  integration:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - run: echo "This is an integration step"
      - run: exit 1
        continue-on-error: true

  prod:
    runs-on: ubuntu-20.04
    needs: integration
    steps:
      - run: echo "This is a deploy step."
```