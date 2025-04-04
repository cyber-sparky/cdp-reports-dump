
## Github actions workflow for running semgrep 

```yml
  semgrep:
    name: semgrep/ci
    needs: test
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v4
      - run: docker run --rm -v "${PWD}:/src" returntocorp/semgrep semgrep --config auto --output semgrep-output.json --json
      - uses: actions/upload-artifact@v3
        with:
          name: semgrep
          path: semgrep-output.json
        if: always()   
```

## Allow build for failure 

```yml
  semgrep:
    name: semgrep/ci
    needs: test
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v4
      - run: docker run --rm -v "${PWD}:/src" returntocorp/semgrep semgrep --config auto --output semgrep-output.json --json
      - uses: actions/upload-artifact@v3
        with:
          name: semgrep
          path: semgrep-output.json
        continue-on-error: true             # allow the build to fail, similar to "allow_failure: true" in GitLab
        if: always()   
```

## Complete pipeline for running semgrep by allowing build to fail 

```yml
name: Django                                  # workflow name

on:
  push:                                       
    branches:                                 # similar to "only" in GitLab
      - main

jobs:
  build:
    runs-on: ubuntu-20.04                     # similar to "image" in GitLab
    steps:
      - uses: actions/checkout@v4

      - name: Setup python
        uses: actions/setup-python@v4
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
      - uses: actions/checkout@v4

      - name: Setup python
        uses: actions/setup-python@v4
        with:
          python-version: '3.6'

      - run: |
          pip3 install --upgrade virtualenv
          virtualenv env
          source env/bin/activate
          pip install -r requirements.txt
          python manage.py test taskManager

  semgrep:
    name: semgrep/ci
    needs: test
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v4
      - run: docker run --rm -v "${PWD}:/src" returntocorp/semgrep semgrep --config auto --output semgrep-output.json --json
      - uses: actions/upload-artifact@v3
        with:
          name: semgrep
          path: semgrep-output.json
        continue-on-error: true             # allow the build to fail, similar to "allow_failure: true" in GitLab
        if: always()                        # what is this for?

  integration:
    runs-on: ubuntu-20.04
    needs: semgrep
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