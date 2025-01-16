
## Install Bandit 

```bash
pip3 install bandit==1.7.4
```

## Running a Scanner and output in JSON 

```bash
bandit -r . -f json | tee bandit-output.json
```

## Filtering based on severity 

To filter Low priority issues 

```sh
bandit -r . -l
```

To filter Medium priority issues

```sh
bandit -r . -ll
```

To filter High priority issues 

```sh
bandit -r . -lll
```