
## Installing Trivy 

```sh
wget https://github.com/aquasecurity/trivy/releases/download/v0.48.1/trivy_0.48.1_Linux-64bit.deb && dpkg -i trivy_*.deb
trivy -h 
```

## Scan a filesystem 

```sh
trivy filesystem .
```

## Additonal Resources 

1. [Trivy for Vulnerability Scanning](https://sadilchamishka.medium.com/trivy-for-vulnerability-scanning-f9e967aea85f)
2. [Trivy Vulnerability Scanning - YouTube](https://www.youtube.com/watch?v=QXLpZruy6mM)
3. [Scanning Docker Images for Vulnerabilities Using Trivy for Effective Security Analysis](https://medium.com/@maheshwar.ramkrushna/scanning-docker-images-for-vulnerabilities-using-trivy-for-effective-security-analysis-fa3e2844db22)
4. [Shift Left Security in Your Cloud-Native Environment with Trivy](https://www.devoteam.com/expert-view/shift-left-security-in-your-cloud-native-environment-with-trivy/)
