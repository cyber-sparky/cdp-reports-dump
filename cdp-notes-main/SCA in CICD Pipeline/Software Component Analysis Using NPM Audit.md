
## Install npm 

```bash
curl -sL https://deb.nodesource.com/setup_14.x | bash -
apt install nodejs -y
npm audit -h
```

## Run a Scan and output into JSON 

```sh
npm audit --json | tee results.json
```