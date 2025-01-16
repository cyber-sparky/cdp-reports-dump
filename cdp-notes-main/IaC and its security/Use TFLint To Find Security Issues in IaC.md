
TFLint is a framework to find possible errors (like illegal instance types) for Major Cloud providers (AWS/Azure/GCP, warn about deprecated syntax, unused declarations and enforce best practices, naming conventions.

## Install TFLint

```sh
curl https://raw.githubusercontent.com/terraform-linters/tflint/master/install_linux.sh | bash
```

## Run the TFLint 

Performing a scan on particular folder called `aws`

```sh
tflint --chdir=aws
```

