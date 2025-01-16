
## Install Checkov 

```sh
pip3 install checkov==2.3.22
```

## Running the scan on single file using checkov 

```sh
checkov -f terraform/aws/s3.tf
```

## Running the scan on directory 

```sh
checkov -d terraform/aws
```

# Challenges 

## Scan the entire directory (/terraform) and save the output into a JSON file at /terraform/scan-result.json

```sh
checkov -d /terraform/ -o json > /terraform/scan-result.json
```

## Identify the number of failed checks from the checkov tool’s scan result?

```sh
jq ".[0].summary.failed" /terraform/scan-result.json
```

## Identify how a specific check can be skipped and skip the checks with codes CKV_AWS_18, CKV_AWS_21, CKV_AWS_20, CKV_AWS_52, CKV_AWS_19. Also, save the output into a JSON file at /terraform/scan-result-skipped.json

```sh
checkov -d /terraform/ -o json --skip-check CKV_AWS_18,CKV_AWS_21,CKV_AWS_20,CKV_AWS_52,CKV_AWS_19 > /terraform/scan-result-skipped.json
```