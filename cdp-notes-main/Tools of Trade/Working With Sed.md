# Introduction to `sed`

**Definition:**  
`sed` (stream editor) is a powerful text manipulation tool in Unix and Linux, part of the POSIX standard. It performs various text transformations on an input stream (file or pipeline input).

**Key Operations:**

| Operation     | Description |
|---------------|-------------|
| Substitute (s) | Replaces the first text pattern in each line of a file. Example: `sed 's/unix/linux/' file.txt` replaces "unix" with "linux". |
| Delete (d)     | Removes a line in the file. Example: `sed '2d' file` deletes the second line of the file. |
| Print (p)      | Displays the line on the terminal. Example: `sed -n '5p' file` prints the fifth line of the file. |

**Additional Features:**
- **Regular Expressions:** Supports complex pattern matching and manipulation.
- **Efficiency:** Makes only one pass over the input(s), making it more efficient than most programming language scripts.

**Note:**  
`sed` is a powerful tool, but incorrect usage can result in data loss. Always back up your data before performing operations.

# The Usage And Syntax Of Sed

**Basic Syntax:**
```sh
sed options... [script] [input-file...]
```
- **[script]:** Set of commands `sed` will perform on the input text (optional).
- **options...:** Defines the behavior of `sed`.
- **[input-file...]:** Name of the file(s) that `sed` will read and apply transformations to.

**Basic Commands:**
- **p:** Print
- **d:** Delete
- **s:** Substitute

**Creating a File:**
```sh
cat > myfile.txt <<EOF
apple 
banana
grapefruit
apple
EOF
```

**Printing File Content:**
```sh
cat myfile.txt
```
- **Output:**
```sh
apple 
banana
grapefruit
apple
```

**Printing a Specific Line:**
```sh
sed -n '1p' myfile.txt
```
- **Output:**
```sh
apple
```
- **Description:** `-n` stops `sed` from printing every line by default, and `1p` prints the first line.

**Deleting a Specific Line:**
```sh
sed -i '1d' myfile.txt
```
- **Description:** `-i` edits the file in-place, and `1d` deletes the first line.

**Checking the Result:**
```sh
cat myfile.txt
```
- **Output:**
```sh
banana
grapefruit
apple
```

**Important Note:**
- `-i` option in `sed` allows changes to be saved directly to the file. Be careful with it as it can lead to data loss. When `-n` option is used, `sed` will not print anything unless explicitly told to do so.

**Substituting Text:**
```sh
sed 's/banana/mango/g' myfile.txt
```
- **Output:**
```sh
mango
grapefruit
apple
```
- **Description:** Replaces all occurrences of "banana" with "mango" in `myfile.txt`.

# Advanced Facets Of Sed

**Using Regular Expressions with `sed`:**

**Adding a String to the File:**
```sh
cat >> myfile.txt <<EOF
error error error
EOF
```

**Checking the File Content:**
```sh
cat myfile.txt
```
- **Output:**
```sh
banana
grapefruit
apple
error error error
```

**Deleting Lines with a Specific Pattern:**
```sh
sed '/error/d' myfile.txt
```
- **Output:**
```sh
banana
grapefruit
apple
```
- **Description:** Deletes any line from `myfile.txt` that contains the word "error".

# Additional Resources

- [GNU Sed manual](https://www.gnu.org/software/sed/manual/sed.html)
- [Sed - An Intro and tutorial - Bruce barett](http://www.grymoire.com/Unix/Sed.html)
- [Sed by Example](https://www.geeksforgeeks.org/sed-command-in-linux-unix-with-examples/)
- [Learn bash in y minutes](https://learnxinyminutes.com/docs/bash/)