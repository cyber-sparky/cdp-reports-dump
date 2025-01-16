
## Install Semgrep 

```sh
pip3 install semgrep==1.30.0
```

## Scanning for a specific pattern on the language 

```sh
semgrep --lang python -e "os.system(...)" .

# --lang is the parameter to set which programming language that we want to scan.
# -e is the parameter to set the pattern for code search pattern, see the details here.
# webapp is a target directory where the source code is located.
```

## Scan and output into JSON and beautify it using jq

```sh
semgrep --lang python -e "os.system(...)"  . --json | jq
```

## Including a file 

```sh
semgrep --lang python -e "DEBUG =True" --include settings.py .
```

## Challenges 

### Scan all declarations of variables in webapp source code

```sh
semgrep --lang python -e '$X = $Y' .
```

### Scan all function calls that have request as an argument

```sh
semgrep --lang python -e '$FUNC(request)' .
```
