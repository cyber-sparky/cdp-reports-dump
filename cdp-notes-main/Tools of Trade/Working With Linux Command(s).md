# Basic File System Commands

## 1. Directory Listing

`ls` lists files and directories.

```sh
ls
```

**Output:**
```
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

`ls -l` displays files with details.

```sh
ls -l
```

**Output:**
```
total 76
drwxr-xr-x   1 root   root    4096 Jun 20 09:28 bin
...
drwxr-xr-x   1 root   root    4096 Jun 15 10:24 var
```

`ls` on a non-existent directory returns an error.

```sh
ls -l abracadabra
```

**Output:**
```
ls: cannot access 'abracadabra': No such file or directory
```

## 2. Create a New Directory

`mkdir` creates a directory.

```sh
mkdir my-directory
```

Running `mkdir` again on the same directory returns an error.

```sh
mkdir my-directory
```

**Output:**
```
mkdir: cannot create directory ‘my-directory’: File exists
```

## 3. Remove a Directory

`rmdir` removes a directory.

```sh
rmdir my-directory
```

Running `rmdir` again on the same directory returns an error.

```sh
rmdir my-directory
```

**Output:**
```
rmdir: failed to remove 'my-directory': No such file or directory
```

- Successful commands often have no output.
- Unsuccessful commands return an error message.

# Blocking and non-blocking command runs

Try Installing the Ansible with the following command 

```bash
pip3 install ansible==8.7.0 ansible-lint==4.3.7
```

When installing, you can observe it took longer than an expected time.. for that we can use `&` where it will run the command as background process and completes much faster 

```bash
/# pip3 install ansible==8.7.0 ansible-lint==4.3.7 &
[1] 1547
/# Requirement already satisfied: ansible==8.7.0 in /usr/local/lib/python3.9/dist-packages (8.7.0)
Requirement already satisfied: ansible-lint==4.3.7 in /usr/local/lib/python3.9/dist-packages (4.3.7)
Requirement already satisfied: ansible-core~=2.15.7 in /usr/local/lib/python3.9/dist-packages (from ansible==8.7.0) (2.15.9)
Requirement already satisfied: pyyaml in /usr/local/lib/python3.9/dist-packages (from ansible-lint==4.3.7) (6.0.1)
Requirement already satisfied: ruamel.yaml<1,>=0.15.37; python_version >= "3.7" in /usr/local/lib/python3.9/dist-packages (from ansible-lint==4.3.7) (0.18.6)
Requirement already satisfied: rich in /usr/local/lib/python3.9/dist-packages (from ansible-lint==4.3.7) (13.7.1)
Requirement already satisfied: importlib-resources<5.1,>=5.0; python_version < "3.10" in /usr/local/lib/python3.9/dist-packages (from ansible-core~=2.15.7->ansible==8.7.0) (5.0.7)
Requirement already satisfied: cryptography in /usr/local/lib/python3.9/dist-packages (from ansible-core~=2.15.7->ansible==8.7.0) (42.0.5)
Requirement already satisfied: jinja2>=3.0.0 in /usr/local/lib/python3.9/dist-packages (from ansible-core~=2.15.7->ansible==8.7.0) (3.1.3)
Requirement already satisfied: packaging in /usr/local/lib/python3.9/dist-packages (from ansible-core~=2.15.7->ansible==8.7.0) (23.2)
Requirement already satisfied: resolvelib<1.1.0,>=0.5.3 in /usr/local/lib/python3.9/dist-packages (from ansible-core~=2.15.7->ansible==8.7.0) (1.0.1)
Requirement already satisfied: ruamel.yaml.clib>=0.2.7; platform_python_implementation == "CPython" and python_version < "3.13" in /usr/local/lib/python3.9/dist-packages (from ruamel.yaml<1,>=0.15.37; python_version >= "3.7"->ansible-lint==4.3.7) (0.2.8)
Requirement already satisfied: markdown-it-py>=2.2.0 in /usr/local/lib/python3.9/dist-packages (from rich->ansible-lint==4.3.7) (3.0.0)
Requirement already satisfied: pygments<3.0.0,>=2.13.0 in /usr/local/lib/python3.9/dist-packages (from rich->ansible-lint==4.3.7) (2.18.0)
Requirement already satisfied: cffi>=1.12; platform_python_implementation != "PyPy" in /usr/local/lib/python3.9/dist-packages (from cryptography->ansible-core~=2.15.7->ansible==8.7.0) (1.16.0)
Requirement already satisfied: MarkupSafe>=2.0 in /usr/local/lib/python3.9/dist-packages (from jinja2>=3.0.0->ansible-core~=2.15.7->ansible==8.7.0) (2.1.5)
Requirement already satisfied: mdurl~=0.1 in /usr/local/lib/python3.9/dist-packages (from markdown-it-py>=2.2.0->rich->ansible-lint==4.3.7) (0.1.2)
Requirement already satisfied: pycparser in /usr/local/lib/python3.9/dist-packages (from cffi>=1.12; platform_python_implementation != "PyPy"->cryptography->ansible-core~=2.15.7->ansible==8.7.0) (2.21)

[1]+  Done                    pip3 install ansible==8.7.0 ansible-lint==4.3.7
```

As you can see in the above verbose command output, where it starts a process with id as `1547` and ends as `Done`

We can also use other tools like `nohup`