
## Synk job in Github Actions

```yml
  oast-snyk:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - uses: actions/checkout@master      
      - uses: snyk/actions/setup@master
      - uses: actions/setup-go@v1
        with:
          go-version: "1.13"

      - uses: actions/setup-node@v2
        with:
          node-version: '10.x'

      - name: Install Node modules
        run: npm install

      - name: Run Snyk
        run: snyk test --json > snyk-results.json
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}

      - uses: actions/upload-artifact@v2
        with:
          name: Snyk               
          path: snyk-results.json
        if: always()        
```

## Allow Job Failure 

```yml
oast-snyk:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - uses: actions/checkout@master
      - uses: snyk/actions/setup@master
      - uses: actions/setup-go@v1
        with:
          go-version: "1.13"

      - uses: actions/setup-node@v2
        with:
          node-version: '10.x'

      - name: Install Node modules
        run: npm install

      - name: Run Snyk
        run: snyk test --json > snyk-results.json
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        continue-on-error: true             # allow the build to fail, similar to allow_failure: true

      - uses: actions/upload-artifact@v2
        with:
          name: Snyk
          path: snyk-results.json
        if: always() 
```

## Complete github actions pipeline for integrating synk into github actions 

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

  oast-snyk:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - uses: actions/checkout@master
      - uses: snyk/actions/setup@master
      - uses: actions/setup-go@v1
        with:
          go-version: "1.13"

      - uses: actions/setup-node@v2
        with:
          node-version: '10.x'

      - name: Install Node modules
        run: npm install

      - name: Run Snyk
        run: snyk test --json > snyk-results.json
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        continue-on-error: true             # allow the build to fail, similar to allow_failure: true

      - uses: actions/upload-artifact@v2
        with:
          name: Snyk
          path: snyk-results.json
        if: always()        # what is this for?

  integration:
    runs-on: ubuntu-20.04
    needs: oast-snyk
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