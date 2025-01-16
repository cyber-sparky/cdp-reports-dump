
## Install Gitleaks 

```sh
wget https://github.com/gitleaks/gitleaks/releases/download/v8.18.1/gitleaks_8.18.1_linux_x64.tar.gz && tar -xvzf gitleaks_8.18.1_linux_x64.tar.gz && mv gitleaks /usr/local/bin
```

## Running a scan in the current dir

```sh
gitleaks detect .
```

## Gitleaks with a output file 

```sh
gitleaks detect . --report-path gitleaks-output.txt
```

## Some advanced scanning by reducting the findings 

```sh
gitleaks detect . --report-path gitleaks-redact50.txt --redact=50
```
