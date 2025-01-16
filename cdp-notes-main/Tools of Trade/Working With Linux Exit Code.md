# Exit code basics

- **Definition**: Indicates whether a program or script executed successfully or unsuccessfully.
- **Convention**:
  - **Exit Code 0**: Successful execution.
  - **Non-zero Exit Code**: Unsuccessful execution.

## Checking Exit Codes

- **Command**: `echo $?`
- **Usage**: Displays the exit code of the last executed command.

## Examples

1. **Successful Command**:
   ```bash
   ls
   ```
   - **Output**:
     ```
     bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
     ```
   - **Exit Code**:
     ```bash
     echo $?
     ```
     - **Output**:
       ```
       0
       ```

2. **Unsuccessful Command**:
   ```bash
   ls non-existent-dir
   ```
   - **Output**:
     ```
     ls: cannot access 'non-existent-dir': No such file or directory
     ```
   - **Exit Code**:
     ```bash
     echo $?
     ```
     - **Output**:
       ```
       2
       ```

3. **Removing a File**:
   - **Create a File**:
     ```bash
     touch newfile
     ```
   - **Remove the File**:
     ```bash
     rm newfile
     ```
   - **Exit Code**:
     ```bash
     echo $?
     ```
     - **Output**:
       ```
       0
       ```

4. **Removing a Non-existent File**:
   ```bash
   rm newfile
   ```
   - **Output**:
     ```
     rm: cannot remove 'newfile': No such file or directory
     ```
   - **Exit Code**:
     ```bash
     echo $?
     ```
     - **Output**:
       ```
       1
       ```

5. **Executing a Non-existent Command**:
   ```bash
   gibberish
   ```
   - **Output**:
     ```
     bash: gibberish: command not found
     ```
   - **Exit Code**:
     ```bash
     echo $?
     ```
     - **Output**:
       ```
       127
       ```

### Key Points

- **Zero Exit Code**: Indicates success.
- **Non-Zero Exit Code**: Indicates failure (e.g., 1, 2, 127).

### Note for CI/CD Systems

- CI/CD systems like GitLab use exit codes to determine the success or failure of build jobs.

### Reference

- **Common Exit Codes Documentation**: [TLDP Exit Codes](https://tldp.org/LDP/abs/html/exitcodes.html)

# Exit codes advanced

## Exit Codes in Security Tools

- **Zero Exit Code**: No vulnerabilities found, indicating success.
- **Non-Zero Exit Code**: Vulnerabilities found, indicating failure.

## Developer Choices

- Security tool developers choose different non-zero exit codes to indicate vulnerabilities.
  - Examples: `1`, `13`, `255`
- Some tools may return zero regardless of vulnerabilities, requiring custom scripts to interpret results.

### Example: Mock Security Tool

- **Purpose**: Simulate a security tool's exit code logic.
- **Behavior**:
  - Returns `0` if no vulnerabilities are found.
  - Returns `99` if vulnerabilities are found.

### Creating the Mock Tool

1. **Create Script**:
   ```bash
   cat > myfaketool << EOL
   #!/bin/bash
   vulncount=\$((0 + \$RANDOM % 3)) # Randomly fake vulnerability count
   if [ \$vulncount -eq 0 ]; then
       echo "No Vulnerabilities"
       exit 0
   else
       echo "Vulnerabilities found: \$vulncount"
       exit 99
   fi
   EOL
   ```

2. **Make Script Executable**:
   ```bash
   chmod +x myfaketool
   ```

3. **Run Script and Check Exit Code**:
   - **Run**:
     ```bash
     ./myfaketool
     ```
   - **Check Exit Code**:
     ```bash
     echo $?
     ```

### Example Outputs

1. **Vulnerabilities Found**:
   - **Command**:
     ```bash
     ./myfaketool
     ```
   - **Output**:
     ```
     Vulnerabilities found: 1
     ```
   - **Exit Code**:
     ```bash
     echo $?
     ```
     - **Output**:
       ```
       99
       ```

2. **No Vulnerabilities Found**:
   - **Command**:
     ```bash
     ./myfaketool
     ```
   - **Output**:
     ```
     No Vulnerabilities
     ```
   - **Exit Code**:
     ```bash
     echo $?
     ```
     - **Output**:
       ```
       0
       ```

### Key Points

- **Zero Exit Code**: Indicates success (no vulnerabilities).
- **Non-Zero Exit Code**: Indicates failure (vulnerabilities found).
- **Developer Choice**: Non-zero exit codes can vary based on developer preference.

# Exit Status of Piped Commands

## What is a Pipe (|)?
- **Definition**: A method to link two programs so the output of the first is the input for the second.
- **Efficiency**: Eliminates the need for intermediate files.

## Example Usage:
```bash
program_A | program_B
```
- **Explanation**: Output of `program_A` is sent to `program_B`.

#### Pipes and Exit Codes
- **Issue**: When using pipes, the exit status of the last command in the pipeline is returned.
  
#### Example Scenarios:
1. **Simple Directory Listing**:
    ```bash
    ls | tee output.txt
    ```
    ```bash
    echo $?
    ```
    - **Output**: 0 (indicating success)

2. **Non-Existent Command**:
    ```bash
    non_existent_command | tee output.txt
    ```
    ```bash
    echo $?
    ```
    - **Output**: Non-zero (indicating failure)

#### Checking Exit Codes in a Pipeline:
- **Example**:
    ```bash
    pdso-exit.sh 2>&1 | tee script_output
    ```
    ```bash
    echo $?
    ```
    - **Output**: 0 (exit status of `tee`, not `pdso-exit.sh`)

## Example with Custom Script:
1. **Creating Script**:
    ```bash
    cat > hello_world.sh <<"EOF"
    echo "Hello World"
    exit $1
    EOF
    chmod +x hello_world.sh
    ```

2. **Running Script**:
    - **Without Parameter**:
        ```bash
        sh hello_world.sh
        echo $?
        ```
        - **Output**: 0
    - **With Parameter 3**:
        ```bash
        sh hello_world.sh 3
        echo $?
        ```
        - **Output**: 3

## Adding More Pipe Commands:
- **Example**:
    ```bash
    sh hello_world.sh 5 | grep "Hello World"
    echo $?
    ```
    - **Output**: 0 (exit status of `grep`)

- **Unsuccessful Search**:
    ```bash
    sh hello_world.sh 5 | grep "Hello World" | grep "Hello Universe"
    echo $?
    ```
    - **Output**: 1 (exit status of last `grep`)

### Recap:
- **Zero Exit Code**: Indicates success.
- **Non-Zero Exit Code**: Indicates failure.
- **Security Tooling**:
  - **Zero Exit Code**: No vulnerabilities found.
  - **Non-Zero Exit Code**: Vulnerabilities found.
  - **Variety in Non-Zero Codes**: Could be `1`, `13`, `99`, `255`, etc.