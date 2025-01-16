
## Complete pipeline for integrating nuclei into github actions with a allowfailure 

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

  nuclei-scan:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - run: |
           docker run --user $(id -u):$(id -g) -w /nuclei -v $(pwd):/nuclei:rw --rm projectdiscovery/nuclei:v2.9.6 -u https://prod-oiahxczk.lab.practical-devsecops.training -j -o nuclei-output.json

      - uses: actions/upload-artifact@v2
        with:
          name: nuclei scan
          path: nuclei-output.json
        if: always()        

  prod:
    runs-on: ubuntu-20.04
    needs: nuclei-scan
    steps:
      - run: echo "This is a deploy step."
```