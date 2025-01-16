# Introduction To Grep

Here are the notes on grep formatted in the KISS method:

### Exploring `grep`

**Definition:**  
Grep is a command-line utility in Unix and Linux used for searching and filtering text. Grep stands for “global regular expression print”. It searches text patterns within files. You supply it with a list of files (or input stream), a pattern (or regular expression), and it will print out every line that matches the pattern.

**Flags/Options:**

| Flag | Description |
|------|-------------|
| `-v` | Invert match: Selects all lines not containing the specified pattern. |
| `-i` | Ignore case: Ignores case distinctions in both the pattern and the input files. |
| `-r` or `-R` | Recurse: Reads all files under each directory, recursively. |
| `-l` | Only the names of files containing the pattern are listed, not the lines containing the occurrences. |
| `-c` | Suppresses normal output and gives the count of lines matching the pattern for each input file. |

**Basic Syntax:**  
```sh
grep pattern filename
```
- **Pattern:** What you are searching for.
- **Filename:** Where you want to search for it.

**Note:**  
Keep in mind that Unix is case-sensitive, which means that when you’re using grep, it matters whether you use capital or lower-case letters. If you wish to ignore case, use the `-i` option.

# Basic Usage And Syntax Of Grep

**Basic Syntax:**
```sh
grep [options] pattern [file...]
```

**Syntax Elements:**

| Element     | Description                                      |
|-------------|--------------------------------------------------|
| `grep`      | The command used to search for a pattern in files. |
| `[options]` | Optional flags that control the behavior of grep. |
| `pattern`   | The text or regular expression you are searching for. |
| `[file…]`   | The file or files where the search will take place. |

**Example:**
```sh
grep 'struct' program.c
```
- **Description:** Searches for the pattern `struct` inside the file named `program.c`. All the lines that contain the word `struct` will be displayed.

### Basic Usage

**Simple Text Search:**
```sh
echo 'Hello, World!' | grep 'World'
```
- **Description:** Finds the `World` pattern in the input furnished by the `echo` command.

**Note:**
- The `grep` command is case-sensitive. `World` is different from `world`.
- Use the `-i` option with `grep` to perform a case-insensitive search.

**Simple Word Search:**
```sh
echo "I love learning DevSecOps in a super way." | grep "DevSecOps"
```
- **Description:** Highlights the word `DevSecOps` if found in the input string.

**Using `grep` With a File:**
1. Create a file with content:
    ```sh
    cat > file1.txt <<EOF
    We love DevSecOps because it integrates security practices within the DevOps process. DevSecOps involves introducing security earlier into the lifecycle of application development—rather than being the last stage in development cycles. This method drastically minimizes vulnerabilities and brings security closer to IT and business objectives. DevSecOps enhances collaboration and opens up a fast, more secure delivery ecosystem. It allows for speedier delivery with significantly less risk, providing a framework for a more secure software development process. Embracing a DevSecOps approach ensures a cultural shift, where every stakeholder in the project takes ownership of security, leading to safer and more secure code deployments.
    EOF
    ```

2. Search for `DevSecOps` in `file1.txt`:
    ```sh
    grep "DevSecOps" file1.txt
    ```

3. Search for a specific sentence in `file1.txt`:
    ```sh
    grep "DevSecOps involves introducing security earlier into the lifecycle of application development—rather than being the last stage in development cycles" file1.txt
    ```

# Advanced Grep Features

**Creating Files:**
```sh
mkdir json-folder
cat >> json-folder/file1.json <<EOF
{
  "name": "John",
  "age": 30,
  "city": "New York",
  "professions": ["Writer", "Actor"]
}
EOF

cat >> json-folder/file2.json <<EOF
{
  "name": "Jane",
  "age": 28,
  "city": "Los Angeles",
  "professions": ["Actor", "Director"]
}
EOF

ls json-folder
```

**Output:**
```sh
file1.json  file2.json
```

**Search Command:**
```sh
grep "Actor" json-folder/*
```

**Output:**
```sh
json-folder/file1.json:  "professions": ["Writer", "Actor"]
json-folder/file2.json:  "professions": ["Actor", "Director"]
```

#### Recursive Search

**Command:**
```sh
grep -r "Actor" json-folder/
```

**Output:**
```sh
json-folder/file1.json:  "professions": ["Writer", "Actor"]
json-folder/file2.json:  "professions": ["Actor", "Director"]
```

#### Counting Matches

**Command:**
```sh
grep -c "Actor" json-folder/*
```

**Output:**
```sh
json-folder/file1.json:1
json-folder/file2.json:1
```

#### Invert Match

**Command:**
```sh
grep -v "Actor" json-folder/file1.json
```

**Output:**
```sh
{
  "name": "John",
  "age": 30,
  "city": "New York",
}
```

#### Displaying Matches Before and After with `grep`

**Flags Description:**

| Flag | Description |
|------|-------------|
| `-A` | Displays the matching line and the specified number of lines after it. |
| `-B` | Displays the matching line and the specified number of lines before it. |
| `-C` | Displays the matching line and the specified number of lines before and after it. |

**Creating a File:**
```sh
echo -e "Line1\nLine2\nLine3\nLine4\nLine5\nLine6\nLine7\nLine8\nLine9\nLine10" > line.txt
```

**Using `-A` Flag:**
```sh
grep -A5 "Line2" line.txt
```

**Output:**
```sh
Line2
Line3
Line4
Line5
Line6
Line7
```

**Using `-B` Flag:**
```sh
grep -B3 "Line10" line.txt
```

**Output:**
```sh
Line7
Line8
Line9
Line10
```

**Using `-C` Flag:**
```sh
grep -C2 "Line5" line.txt
```

**Output:**
```sh
Line3
Line4
Line5
Line6
Line7
```

#### Consult the `man` Page

- **Command:** `man grep`
- **Description:** Consult the man page for more options and examples to fully master what `grep` has to offer.

# Displaying Matches In A Tool With Grep

| Task                            | Command                                             | Description |
|---------------------------------|-----------------------------------------------------|-------------|
| Docker --help Command           | `docker --help`                                     | Display the help options for Docker. |
| Search for Image-related Options| `docker --help \| grep "images"`                    | Search for Docker help options related to images. |
| Case-Insensitive Search         | `docker --help \| grep -i "docker"`                 | Perform a case-insensitive search for the term "docker" in the help options. |

# Additional Resources

- [How to change default shell in Linux](https://www.cyberciti.biz/link/bash-changing-default-shell-in-linux/)
- [UNIX / Linux: 10 Netstat Command Examples](https://www.thegeekstuff.com/2010/03/netstat-command-examples/)
- [Linux Command Tutorials](https://ryanstutorials.net/linuxtutorial/commandline.php)
- [Regular Expressions](https://www.regular-expressions.info/)
- [Grep Manual](https://www.gnu.org/software/grep/manual/grep.html)