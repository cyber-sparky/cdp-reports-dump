
## Scanning Specific NPM 

```sh
osv-scanner -L package-lock.json
```

## Config for setting up to remove False positives 

```sh
cat > /webapp/config.toml <<EOF
[[IgnoredVulns]]
id = "PYSEC-2024-47"
reason = "false positive"

[[IgnoredVulns]]
id = "PYSEC-2024-28"
reason = "false positive"
EOF
```

## Running the scanner with config 

```sh
osv-scanner --config=/webapp/config.toml -L requirements.txt
```

