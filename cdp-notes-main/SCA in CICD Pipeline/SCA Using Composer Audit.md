
## Installing Composer 

```sh
apt update && apt install -y php7.4 php7.4-gd php7.4-intl php7.4-xsl php7.4-mbstring
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php --install-dir=/usr/local/bin --filename=composer
php -r "unlink('composer-setup.php');"
composer audit -h
```

## Running the Scan

Go into the repository that you wanted to scan and run these commands:

```sh
composer install
composer audit 
```

## Getting the output in JSON format 

```sh
composer audit -f json | tee results.json
```

