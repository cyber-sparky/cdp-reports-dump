
## Bundler-audit job in github actions

```yml
  bundler-audit:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - uses: actions/checkout@v2

      - run: docker run --rm -v $(pwd):/src -w /src hysnsec/bundle-audit check --format json --output bundle-audit-output.json

      - uses: actions/upload-artifact@v2
        with:
          name: bundler-audit
          path: bundle-audit-output.json
        if: always() 
```

## Allow failure 

```yml
bundler-audit:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - uses: actions/checkout@v2

      - run: docker run --rm -v $(pwd):/src -w /src hysnsec/bundle-audit check --format json --output bundle-audit-output.json
        continue-on-error: true             # allow the build to fail, similar to "allow_failure: true" in GitLab

      - uses: actions/upload-artifact@v2
        with:
          name: bundler-audit
          path: bundle-audit-output.json
        if: always()
```

## Final pipeline that runs bundler-audit in github actions 

```yml
name: rails                                  # workflow name

on:
  push:
    branches:                                 # similar to "only" in GitLab
      - main

jobs:
  build:
    runs-on: ubuntu-20.04                    # similar to "image" in GitLab
    steps:
      - uses: actions/checkout@v2

      - run: echo "This is a build step"

  test:
    runs-on: ubuntu-20.04
    needs: build
    steps:
      - uses: actions/checkout@v2

      - run: echo "This is a test step"

  bundler-audit:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - uses: actions/checkout@v2

      - run: docker run --rm -v $(pwd):/src -w /src hysnsec/bundle-audit check --format json --output bundle-audit-output.json
        continue-on-error: true             # allow the build to fail, similar to "allow_failure: true" in GitLab

      - uses: actions/upload-artifact@v2
        with:
          name: bundler-audit
          path: bundle-audit-output.json
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
