
## Installing Sandstorm 

```sh
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | tee /etc/apt/sources.list.d/nodesource.list
apt update
apt install nodejs -y
npm install -g @sandworm/audit
```

## Running a Scanner 

```sh
# Go into the folder that you wanted to scan 
cd webapp

# Install the dependencies 
npm install

# Run the scanner 
sandstorm audit 
```

## Ignoring the CSV output 

```sh
sandworm audit --skip-csv
```

## Filtering Vulns from JSON report 

```sh
cat sandworm/owasp-nodejs-goat@1.3.0-report.json | jq '.dependencyVulnerabilities'
```

## Filtering the name and severity of the packages that are vulnerable 

```sh
cat sandworm/owasp-nodejs-goat@1.3.0-report.json | jq '.dependencyVulnerabilities[] | "\(.name) \(.severity)"'
```

## Skipping license related issues 

```sh
sandworm audit --skip-license-issues
```

### Filtering based on severity and grabbing the advisories IDs

```sh
cat sandworm/owasp-nodejs-goat@1.3.0-report.json | jq '.dependencyVulnerabilities[] | select(.severity == "critical") | .githubAdvisoryId'
```

## Fixing a Particular issues 

```sh
sandworm resolve --issueId GHSA-v8w9-2789-6hhr
```