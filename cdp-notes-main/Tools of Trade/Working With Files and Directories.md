
# Understanding Directories

We can create new directories using the following command 

```bash
mkdir nithissh
```

Which will create a new directory named `nithissh`

You can list out the files inside the directory using `ls` 

```bash
ls nithissh/
```

You can move into the directory using `cd` 

```bash
cd nithissh/
```

You can also print out the present working directory using `pwd` where it shows exact path on where you are 

```bash
pwd 
```

You can come out of the current directory using `cd ..`

```bash
cd ..
```

You can delete the directory using `rmdir`

```bash
rmdir nithissh/
```

# Working with Files in Linux

**1. Creating and Editing a File**

Use `nano` to create and edit a file:

```sh
nano myfile
```

- Add text, save with `Ctrl + O`, and exit with `Ctrl + X`.

You can also copy-paste text into the terminal:

```sh
nano myfile
```

Paste "Hello world!", then save with `Ctrl + X`, `Y`, and `Enter`.

**2. Viewing a File**

Use `nano` to view a file:

```sh
nano myfile
```

Or use `cat` to read the file:

```sh
cat myfile
```

**3. Renaming a File**

Use `mv` to rename a file:

```sh
mv myfile newfile
```

Check the change with `ls`:

```sh
ls
```

**4. Deleting a File**

Use `rm` to delete a file:

```sh
rm newfile
```


# Creating Files with `cat` Command

The `cat` command is usually used to read a file, but you can also create files with it.

```sh
cat > filename <<EOL
Some text content
Some text content 2
Some text content 3
EOL
```

### Breakdown of the Command:

1. **Filename**: The name of the file to create, here it's `filename`.
2. **Here Document**: `<<EOL` is a special code block to redirect content.
3. **cat Command**: When followed by `>`, it creates a file with the specified content.

This method is useful for non-interactive environments like CI/CD systems.