
ZAP is an open-source web application security scanner to perform security testing (Dynamic Testing) on web applications. OWASP ZAP is the flagship OWASP project used extensively by penetration testers. ZAP can also run in a daemon mode for hands-off scans for CI/CD pipeline and provides extensive API (SDK) and a REST API to help users create custom scripts.

## Running a zap baseline 

```sh
docker run --rm softwaresecurityproject/zap-stable:2.13.0 zap-baseline.py --help
```

## Scanning a target 

```sh
docker run --rm softwaresecurityproject/zap-stable:2.13.0 zap-baseline.py -t https://prod-oiahxczk.lab.practical-devsecops.training
```

## Scanning a target and output in JSON 

```sh
docker run --user $(id -u):$(id -g) -w /zap -v $(pwd):/zap/wrk:rw --rm softwaresecurityproject/zap-stable:2.13.0 zap-baseline.py -t https://prod-oiahxczk.lab.practical-devsecops.training -J zap-output.json
```

