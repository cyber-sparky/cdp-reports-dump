
## Github workflow for integrating tfsec with allowfailure 

```yml
name: Terraform                               # workflow name

on:
  push:                                       
    branches:                                 # similar to "only" in GitLab
      - main

jobs:
  build:
    runs-on: ubuntu-20.04                    # similar to "image" in GitLab
    steps:
      - run: echo "This is a build step"  tfsec:
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Terraform security scan
        uses: aquasecurity/tfsec-action@v1.0.0
        with:
          soft_fail: true

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