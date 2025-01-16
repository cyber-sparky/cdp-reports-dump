
## Github actions workflow for embedding retireJS into the CI/CD pipeline

```yml
  oast-frontend:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - uses: actions/checkout@v2

      - uses: actions/setup-node@v2
        with:
          node-version: '10.x'

      - run: npm install

      - run: docker run --rm -v $(pwd):/src -w /src hysnsec/retire --outputformat json --outputpath retirejs-report.json --severity high

      - uses: actions/upload-artifact@v2
        with:
          name: RetireJS
          path: retirejs-report.json
        if: always()                        # step or job should always run, regardless of the success or failure of previous steps or jobs in the workflow
```

## Allow Job Failure

```yml
  oast-frontend:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - uses: actions/checkout@v2

      - uses: actions/setup-node@v2
        with:
          node-version: '10.x'

      - run: npm install

      - run: docker run --rm -v $(pwd):/src -w /src hysnsec/retire --outputformat json --outputpath retirejs-report.json --severity high
        continue-on-error: true             # allow the build to fail, similar to "allow_failure: true" in GitLab

      - uses: actions/upload-artifact@v2
        with:
          name: RetireJS
          path: retirejs-report.json
        if: always() # Job continously runs regardless off success or failure of particular step
```

