
tfsec uses static analysis of your terraform templates to spot potential security issues.

## Install tfsec

```sh
wget -O /usr/local/bin/tfsec https://github.com/aquasecurity/tfsec/releases/download/v0.55.0/tfsec-linux-amd64
chmod +x /usr/local/bin/tfsec
```

## Scanning a directory 

```sh
tfsec aws
```

## Resulting in an JSON output 

```sh
tfsec aws -f json | tee tfsec-output.json
```