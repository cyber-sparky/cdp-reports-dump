# Introduction to `awk`

**Definition:**  
`awk` is a powerful command-line tool in Unix and Linux used for filtering and manipulating data. Named after its creators (Aho, Weinberger, and Kernighan), it excels at handling structured data.

**Key Features:**

| Feature                    | Description |
|----------------------------|-------------|
| Field-Based Data Processing | Awk can read and parse each line based on a field separator. |
| String Manipulation        | Offers powerful utilities for manipulating text. |
| User-Defined Functions     | Allows creation and use of custom functions within awk. |
| Report Generation          | Can generate formatted reports from data. |

**Basic Syntax:**
```sh
awk 'pattern {action}' filename
```
- **Pattern:** Defines what you want to search for.
- **Action:** Specifies what to do when a match for the pattern is found.
- **Filename:** Name of the file(s) where the search will be performed.

# Basic Usage and Syntax Of Awk

**Awk Syntax:**

**Basic Syntax:**
```sh
awk '{print $1}' names.txt
```
- **Description:** Prints the first field in each line of the file named `names.txt`.

**Creating a File:**
```sh
cat > names.txt <<EOF
Kurt Donald Cobain
James Douglas Morrison
John Winston Lennon
EOF
```

**Printing the First Column:**
```sh
awk '{print $1}' names.txt
```
- **Output:**
```sh
Kurt
James
John
```

**Printing the Second Column:**
```sh
awk '{print $2}' names.txt
```
- **Output:**
```sh
Donald
Douglas
Winston
```

**Summary:**
- `print $1`: Prints the first field (column) from the file.
- `print $2`: Prints the second field (column) from the file.

# Advanced `awk` Features

**Printing Multiple Fields:**
```sh
awk '{print $2, $3}' names.txt
```
- **Description:** Prints the second and third columns of each line in the `names.txt` file.

**Pattern Matching:**
```sh
awk '$2 == "Douglas" {print $0}' names.txt
```
- **Description:** Prints lines where the second field is "Douglas".

**Printing Specific Fields:**
```sh
cat /etc/passwd | awk -F: '{print $1}'
```
- **Description:** Uses the colon as a field separator and prints the first field of each line (usernames) from `/etc/passwd`.

```sh
cat /etc/passwd | awk -F: '{print $1, $6}'
```
- **Description:** Prints both the usernames and their home directories from `/etc/passwd`.

**Calculating Field Sums:**

**Creating a File:**
```sh
cat > scores.txt <<EOF
Kurt 85
Jimmy 100
Lennon 92
EOF
```

**Calculating Sum:**
```sh
awk '{ sum += $2; } END { print "Total Score:", sum; }' scores.txt
```
- **Description:** Calculates and prints the total sum of scores (`Total Score: 277`).

# Additional Resources

- [GNU Awk User's Guide](https://www.gnu.org/software/gawk/manual/gawk.html)
- [Awk tutorial](https://www.tutorialspoint.com/awk/index.htm)
- [Awk - A Tutorial by Bruce Barnett](https://www.grymoire.com/Unix/Awk.html)