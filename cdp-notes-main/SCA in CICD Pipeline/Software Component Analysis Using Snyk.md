
## Install Snyk

```sh
wget -O /usr/local/bin/snyk https://github.com/snyk/cli/releases/download/v1.984.0/snyk-linux
chmod +x /usr/local/bin/snyk
synk --help
```

## Run a scan and get a JSON output 

```sh
snyk test --json .
snyk test --json . > output.json
```

## In some cases, to avoid dependency related errors 

```sh
snyk test --strict-out-of-sync=false --json . > output.json
```

