# Working With Command Outputs


Linux allows saving command output to a file or sending it to another command.

## Redirect Output to a File

Save output of a command to a file:

```sh
cat /etc/passwd > mypasswd.txt
```

Verify the content:

```sh
cat mypasswd.txt
```

**Output:**
```
root:x:0:0:root:/root:/bin/bash
...
sshd:x:101:65534::/run/sshd:/usr/sbin/nologin
```

## Append Output to a File

Append content to an existing file:

```sh
cat /etc/passwd >> mypasswd.txt
```

Verify the content:

```sh
cat mypasswd.txt
```

## Using `echo` for Redirection

Print a string:

```sh
echo "this is a string"
```

Print the value of a variable:

```sh
echo $HOSTNAME
```

Redirect a string to a file:

```sh
echo "this is a string" > file.txt
```

Verify the content:

```sh
cat file.txt
```

**Output:**
```
this is a string
```

# Piping the Output

Piping allows sending a command’s output to another command for further processing.

## Extracting Usernames

Use `cat` and `cut` to extract usernames from `mypasswd.txt`:

```sh
cat mypasswd.txt | cut -d ':' -f 1
```

## Searching for a Process

Use `ps` and `grep` to search for a running process (e.g., bash):

```sh
ps -aux | grep bash
```

**Output:**
```
root         1  0.0  0.0  21340  4660 pts/0    Ss   14:29   0:00 bin/bash
root       624  0.0  0.0  13216  1156 pts/0    S+   14:30   0:00 grep --color=auto bash
```

Review the output of `ps -aux` without `grep` for comparison.

More info: [Introduction to pipes in Linux](https://opensource.com/article/18/8/introduction-pipes-linux)