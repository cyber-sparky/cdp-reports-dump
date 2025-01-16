
## Pattern matching with Ellipsis operator

Ellipsis operator (…). This operator is used to match a sequence of zero or more arguments, statements, or characters in the given code.

```sh
semgrep --lang python -e 'os.system(...)' .
```

## Pattern matching with Metavariables

Metavariables can be used to track values across a specific code scope. This includes variables, functions, arguments, classes, object methods, imports, exceptions, and more especially when we don’t know the value or contents beforehand.

```sh
semgrep --lang python -e '$X = $Y' .
```

## fundamentals of building rules 

| **Field**                | **Type**       | **Description**                                                                 |
|--------------------------|----------------|---------------------------------------------------------------------------------|
| `id`                     | string         | Unique, descriptive identifier, e.g., `possible-command-injection`              |
| `message`                | string         | Message highlighting why this rule fired and how to remediate the issue, e.g., `Command injection attack` |
| `severity`               | string         | One of: `WARNING`, `ERROR`                                                      |
| `languages`              | array          | Any of: `go`, `java`, `javascript`, `python`, `typescript`                      |
| `pattern`                | string         | Find code matching this expression                                              |
| `patterns`               | array          | Logical AND of multiple patterns                                                |
| `pattern-either`         | array          | Logical OR of multiple patterns                                                 |
| `pattern-regex`          | string         | Search files for Python `re` compatible expressions                             |
|                          |                | **The following fields can be used inside `patterns` or `pattern-either` fields to target specific parts of the code** |
| `metavariable-regex`     | map            | Search metavariables for Python `re` compatible expressions                     |
| `pattern-not`            | string         | Logical NOT - remove findings matching this expression                          |
| `pattern-inside`         | string         | Keep findings that lie inside this pattern                                      |
| `pattern-not-inside`     | string         | Keep findings that do not lie inside this pattern                               |
| `pattern-where-python`   | string         | Remove findings matching this Python expression                                 |

## Rule for detecting os.system in python 

```sh
cat > command_injection.yaml <<EOF
rules:
- id: possible-command-injection
  patterns:
  - pattern: os.system(...)
  - pattern-not: os.system("...")
  message: Possible Command Injection
  languages:
  - python
  severity: WARNING
EOF
```

## Scanning with the rule 

```sh
semgrep --lang python -f command_injection.yaml .
```

## Rules of detecting inside a block using pattern-inside 

pattern-inside to search a pattern inside code blocks e.g., inside an if statement, a function call, a class or anything that contains a code block.

```sh
cat > find_project_db_get_call.yaml <<EOF
rules:
- id: find-get-project-db-value
  patterns:
  - pattern: Project.objects.get(...)
  - pattern-inside: |
      def \$FUNC(request):
        ...
  message: Get project db value
  languages:
  - python
  severity: WARNING
EOF
```

