
## Github workflow for integrating zap using docker 

```yml
  zap_baseline:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - run: |
           docker pull softwaresecurityproject/zap-stable:2.14.0
           docker run --user root --rm -v $(pwd):/zap/wrk:rw -w /zap softwaresecurityproject/zap-stable:2.14.0 zap-baseline.py -t ${{ secrets.PROD_URL }} -J zap-output.json
        continue-on-error: true

      - uses: actions/upload-artifact@v2
        with:
          name: ZAP Scan
          path: zap-output.json
        if: always() 
```

##  Github workflow from marketplace 

```yml
  zap_baseline:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          ref: main

      - name: ZAP Scan
        uses: zaproxy/action-baseline@v0.4.0
        with:
          token: ${{ secrets.TOKEN }}
          docker_name: 'softwaresecurityproject/zap-stable:2.14.0'
          target: ${{ secrets.PROD_URL }}
        continue-on-error: true
```