
## Install Brakeman 

```sh
apt update
apt install ruby-full -y
gem install brakeman -v 5.2.1
brakeman -h
```

## Running a scan using brakeman 

```sh
brakeman -f json | tee result.json
```

## To ignore False positives using brakeman 

Create a `.ignore ` file and add a fingerprint of the vuln you wanted to ignore 

```sh
cat > brakeman.ignore <<EOF
{
    "ignored_warnings": [
        {
          "fingerprint": "febb21e45b226bb6bcdc23031091394a3ed80c76357f66b1f348844a7626f4df",
          "note": "ignore XSS"
        }
    ]
}
EOF
```

Then we can run the scanner along with the `.ignore` file 

```sh
brakeman -f json -i brakeman.ignore | tee result.json
```

