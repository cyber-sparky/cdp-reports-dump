
## Github Actions workflow for integrating dependency check 

```yml
  odc-backend:
    runs-on: ubuntu-20.04
    name: depecheck_test
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Depcheck
        uses: dependency-check/Dependency-Check_Action@main
        id: Depcheck
        with:
          project: 'webgoat'
          path: '.'
          format: 'JSON'

      - name: Upload Test results
        uses: actions/upload-artifact@master
        with:
           name: Depcheck report
           path: ${{github.workspace}}/reports
```

