
KICS, or “Keeping Infrastructure as Code Secure” is an open-source project developed by Checkmarx, designed to offer static code analysis for Infrastructure as Code (IaC).

## Pulling the docker 

```sh
docker pull checkmarx/kics:v1.7.11
```

## Running the scan 

```sh
docker run -t -v $(pwd):/path checkmarx/kics:v1.7.11 scan --path "/path"
```

## Output in HTML format 

```sh
docker run -t -v $(pwd):/path checkmarx/kics:v1.7.11 scan --path "/path" --output-path "/path" --report-formats html
```

