
## Install Trufflehog 

```sh
wget https://github.com/trufflesecurity/trufflehog/releases/download/v3.79.0/trufflehog_3.79.0_linux_amd64.tar.gz
tar -xvf trufflehog_3.79.0_linux_amd64.tar.gz
chmod +x trufflehog
mv trufflehog /usr/local/bin/
```

## Scan a Git repo using remote URL and output in JSON 

```sh
trufflehog git http://gitlab-ce-oiahxczk.lab.practical-devsecops.training/root/django-nv.git --json
```

## Same using SSH 

```sh
trufflehog git git@gitlab-ce-oiahxczk:root/django-nv.git --json | tee secret.json
```

