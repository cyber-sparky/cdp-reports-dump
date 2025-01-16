
Pylint is a static code analysis tool for Python which looks for programming errors, helps enforce a coding standard, sniffs out code smells and offers simple refactoring suggestions. 

## Installing pylint 

```sh
pip3 install pylint
```

## Running a scan 

```sh
pylint taskManager/*.py
```

## Running and getting a output in JSON 

```sh
pylint taskManager/*.py -f json | tee output.json
```

## Reduce FP by creating a dotfile with rules you wanna ignore 

```sh
cat > .pylintrc <<EOF
[MASTER]
disable=missing-module-docstring,import-error
EOF
```

