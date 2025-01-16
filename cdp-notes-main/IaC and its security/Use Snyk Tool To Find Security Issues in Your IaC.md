
CLI and build-time tool to find & fix known vulnerabilities in open-source dependencies, apart from Software Component Analysis (SCA), Snyk also support to perform SAST for Infrastructure as Code like Terraform.

## Installing synk 

```sh
wget -O /usr/local/bin/snyk https://github.com/snyk/cli/releases/download/v1.1105.0/snyk-linux
chmod +x /usr/local/bin/synk 
```

## Running the scanner and getting otput

```sh
snyk iac test aws --json > snyk-output.json
```

