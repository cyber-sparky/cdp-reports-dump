
## Install detect-secrets 

```sh
pip3 install detect-secrets==1.4.0
```

## Running scann in current directory 

```sh
detect-secrets scan .
```

## Running a scan on a target folder 

```sh
detect-secrets scan /webapp
```

## Output into JSON 

```sh
detect-secrets scan . > secrets-output.json
```

## Auditing the result from detect-results 

```sh
detect-secrets audit secrets-output.json
```