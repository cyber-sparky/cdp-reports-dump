
Nuclei can help you ensure the security of complex networks. With vulnerability scans, nuclei can identify security issues on your network. Once configured, nuclei can provide detailed information on each vulnerability, including Severity, Impact, and Recommended remediation.

## Install Nuclei 

```sh
wget https://github.com/projectdiscovery/nuclei/releases/download/v2.9.6/nuclei_2.9.6_linux_amd64.zip
unzip nuclei_2.9.6_linux_amd64.zip
mv nuclei /usr/local/bin/nuclei
```

## Running a scan

```sh
nuclei -u https://prod-oiahxczk.lab.practical-devsecops.training
```

## Run a scan and output in JSON 

```sh
nuclei -u https://prod-oiahxczk.lab.practical-devsecops.training -j -o nuclei-output.json
```

## Nulcei using docker 

```sh
docker pull projectdiscovery/nuclei:v2.9.6
docker run --user $(id -u):$(id -g) -w /nuclei -v $(pwd):/nuclei:rw --rm projectdiscovery/nuclei:v2.9.6 -u https://prod-oiahxczk.lab.practical-devsecops.training -j -o nuclei-output.json

```
