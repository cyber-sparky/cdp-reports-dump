
## Github Actions workflow for TFLint

```yml
  tflint:
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - uses: terraform-linters/setup-tflint@v1
        name: Setup TFLint
        with:
          tflint_version: latest

      - name: Run TFLint
        run: tflint --chdir=aws -f json > tflint-output.json
        continue-on-error: true

      - uses: actions/upload-artifact@v2
        with:
          name: TFLint
          path: tflint-output.json
        if: always()
```

