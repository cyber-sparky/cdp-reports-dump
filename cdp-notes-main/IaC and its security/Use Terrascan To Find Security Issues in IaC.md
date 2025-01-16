
Terrascan allows us to detect compliance and security violations across Infrastructure as Code to mitigate risk before provisioning cloud-native infrastructure.


## Install Terrascan 

```sh
wget https://github.com/accurics/terrascan/releases/download/v1.12.0/terrascan_1.12.0_Linux_x86_64.tar.gz
tar -xvf terrascan_1.12.0_Linux_x86_64.tar.gz
chmod +x terrascan
mv terrascan /usr/local/bin/
```

## Scanning a directory 

```sh
terrascan scan -d gcp
```

## Challenges 

### Scan gcp resources in the source code and report issues with minimum severity of level High.

```sh
terrascan scan -d gcp --severity high
```
