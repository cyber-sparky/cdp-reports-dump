
PHPStan is a SAST tool that can find insecure code patterns in your PHP applications by identifying potential bugs, including security issues. It uses static analysis to check the code for errors, undefined variables, and other issues that could lead to vulnerabilities.

## Installing PHPStan 

```sh
apt update && apt install composer -y
apt install php-xml -y
composer require --dev phpstan/phpstan
mv vendor/phpstan/phpstan/phpstan.phar /usr/local/bin/phpstan
phpstan --help
```

## Running a scan 

```sh
phpstan analyse .
```

## Run a scan and output in JSON 

```sh
phpstan analyse --error-format=json . | tee phpstan-output.json
```

