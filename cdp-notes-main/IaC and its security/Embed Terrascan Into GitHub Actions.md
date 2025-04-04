
## Github workflow for integrating terrascan with allowfailure 

```yml
name: Terraform                               # workflow name

on:
  push:                                       
    branches:                                 # similar to "only" in GitLab
      - main

jobs:
    terrascan:
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Run Terrascan
        id: terrascan
        uses: accurics/terrascan-action@v1
        with:
          iac_type: 'terraform'
          iac_version: 'v14'
          policy_type: 'aws'
          only_warn: true
          iac_dir: 'aws'

  test:
    runs-on: ubuntu-20.04
    needs: build
    steps:
      - run: echo "This is a test step"

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
      - run: echo "This is a deploy step"
```