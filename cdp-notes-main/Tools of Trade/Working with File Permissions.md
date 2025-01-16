# Understanding File Permissions

## Creating and Viewing a File

1. **Create a File**: 
   ```bash
   cat > myfile <<EOL
   ls
   EOL
   ```
   - Creates `myfile` with the content `ls`.

2. **Verify File Content**:
   ```bash
   cat myfile
   ```

## Checking File Permissions

1. **Using `ls -l`**:
   ```bash
   ls -l myfile
   ```

2. **Using `stat`**:
   ```bash
   stat myfile
   ```

## Making a File Executable

1. **Change Permissions**:
   ```bash
   chmod +x myfile
   ```

2. **Verify Executable Permissions**:
   - Using `ls -l`:
     ```bash
     ls -l myfile
     ```
     - Output Example: `-rwxr-xr-x 1 root root 3 Aug 13 20:36 myfile`
     - The `x` indicates executable permissions.

   - Using `stat`:
     ```bash
     stat myfile
     ```

## Running the Executable File

1. **Run the File**:
   ```bash
   ./myfile
   ```
   - Outputs the directory list since `ls` is inside `myfile`.

   - **Command Output Example**:
     ```bash
     bin   dev  home  lib64  mnt     opt   root  sbin  sys  usr
     boot  etc  lib   media  myfile  proc  run   srv   tmp  var
     ```

### Note
- The dot (`./`) before the command indicates the file is in the current directory.

### Additional Resources
- For more information on `chmod`: [chmod command in Linux](https://linuxize.com/post/chmod-command-in-linux/)

# Running Commands as Different Users

- **Purpose**: Run programs and utilities as another user (default: superuser).
- **Usage**:
  ```bash
  sudo <command>
  ```
- **Example**:
  ```bash
  sudo ls
  ```
- **Installation** (if `sudo` not found):
  ```bash
  apt-get install sudo
  ```

## Creating a User and Adding to `sudo` Group

1. **Create User `john`**:
   ```bash
   echo -e "pdevsecops\npdevsecops" | adduser --gecos "" john
   ```
   - **Password**: `pdevsecops`
   - **Output**:
     ```
     Adding user `john' ...
     Adding new group `john' (1000) ...
     Adding new user `john' (1000) with group `john' ...
     Creating home directory `/home/john' ...
     Copying files from `/etc/skel' ...
     Enter new password: Retype new password: passwd: password updated successfully
     ```

2. **Add `john` to `sudo` Group**:
   ```bash
   usermod -aG sudo john
   ```

## Switching to User `john`

1. **Become `john`**:
   ```bash
   sudo su - john
   ```
   - **Output**:
     ```
     To run a command as administrator (user "root"), use "sudo <command>".
     See "man sudo_root" for details.
     john@devsecops-box-oiahxczk:~$
     ```

## Accessing Sensitive Files

1. **Try Accessing `/etc/shadow`**:
   ```bash
   cat /etc/shadow
   ```
   - **Output**:
     ```
     cat: /etc/shadow: Permission denied
     ```

2. **Access `/etc/shadow` with `sudo`**:
   ```bash
   echo pdevsecops | sudo -S cat /etc/shadow
   ```
   - **Output**:
     ```
     root:*:18526:0:99999:7:::
     daemon:*:18526:0:99999:7:::
     bin:*:18526:0:99999:7:::
     sys:*:18526:0:99999:7:::
     ...
     john:$6$hJz8BHbB$WkIBAYHTABpbnwBfFWk6nwJHhEzoGjq4Big3ciZw1NtIMEp3lUMdm8NOUKTrG8fgzQI/WzNRtd3FB88ODoIJw/:18759:0:99999:7:::
     ```

### Note

- **sudo** allows running commands with elevated privileges.
- The password is automatically provided, avoiding exposure in plain text.

### Understanding `--gecos`

- **`--gecos`**: This option is used with `adduser` to set GECOS (General Electric Comprehensive Operating System) fields, which include user information such as:
  - Full name
  - Office number
  - Work phone number
  - Home phone number
- **Example**:
  ```bash
  adduser --gecos "John Doe,1234,5678,9012" john
  ```