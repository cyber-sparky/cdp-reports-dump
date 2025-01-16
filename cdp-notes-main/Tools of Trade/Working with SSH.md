# Getting Started with SSH

## Logging into a Remote Machine

Assuming the necessary keys are already set up for the connection, you can log into a remote machine using the following command:

```bash
ssh -i ~/.ssh/id_rsa root@prod-oiahxczk
```

- **Options Explained**:
  - `-i`: Specifies the private key to use for logging into the remote machine.
  - `root`: The user we want to log in as.
  - `prod-oiahxczk`: The hostname of the server.

**Example Output**:
```text
The authenticity of host 'prod-xhdfmttw (10.x.x.x)' can't be established.
ECDSA key fingerprint is SHA256:pB1enuLtdg6tIPn3Jm+zBkNFPSbm5s7Ym5js8FMyaOE.
Are you sure you want to continue connecting (yes/no)? yes
```

- **Key Fingerprint**:
  - This fingerprint ensures you are connecting to the intended machine.
  - For automation, you can avoid the host authenticity check by adding the target host’s fingerprint to a list of known hosts.

```bash
ssh-keyscan -t rsa prod-oiahxczk >> ~/.ssh/known_hosts
```

- **Verify Connection**:
  - After accepting the authenticity warning, use `hostname` to verify the connection to the remote machine.

```bash
hostname
```

- **Log Out**:
  - Exit the production machine using the `exit` command.

```bash
exit
```

## Running Commands Remotely

You can use SSH to run commands remotely on the remote machine:

```bash
ssh root@prod-oiahxczk "hostname"
```

- **Explanation**:
  - The command inside the quotes is executed on the remote machine.
  - The remote machine in this example is `prod-oiahxczk`.

**Example Output**:
```text
prod-oiahxczk
```

- The `hostname` command ran on the production machine, returning the production machine's hostname instead of the local machine's hostname.

## Summary

- **Logging In**:
  - Use `ssh -i ~/.ssh/id_rsa root@prod-oiahxczk` to log in with a specified private key.
  - Verify connection with `hostname`.
  - Exit with `exit`.

- **Running Commands Remotely**:
  - Use `ssh root@prod-oiahxczk "command"` to run commands on the remote machine.
  - Example: `ssh root@prod-oiahxczk "hostname"`.

- **Automation**:
  - Use `ssh-keyscan -t rsa prod-oiahxczk >> ~/.ssh/known_hosts` to avoid host authenticity prompts.


# Learning to Set Up SSH

SSH can be configured to allow password-less logins using public-private key pairs. Here’s how to set it up:

## 1. SSH Configuration for Password-less Login

Ensure the SSH server is configured to allow password-less login by modifying the SSH configuration file (`/etc/ssh/sshd_config`):

```bash
cat >> /etc/ssh/sshd_config <<EOF
RSAAuthentication yes
PubKeyAuthentication yes
EOF
```

After making these changes, restart the SSH server:

```bash
service sshd restart
```

## 2. Generating Public-Private Key Pairs

If you don't already have a key pair, generate a new pair using the RSA algorithm:

```bash
ssh-keygen -t rsa
```

- **Location and Passphrase**: You’ll be prompted for a location and a passphrase. The default locations are:
  - Private key: `~/.ssh/id_rsa`
  - Public key: `~/.ssh/id_rsa.pub`

## 3. Configuring the Remote Machine

To allow connections, the remote machine needs your public key. Use the `ssh-copy-id` command to add your public key to the remote machine:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub user@targetserver
```

- **Password Prompt**: You’ll need to provide the password for the user on the remote machine to complete authentication.
- **Adding the Public Key**: The `ssh-copy-id` command adds the public key to `~/.ssh/authorized_keys` on the remote machine.

## Recap of Key Points

| Key Point        | Details                                                                                 |
|------------------|-----------------------------------------------------------------------------------------|
| `sshd`           | The server needs to be configured to allow SSH.                                         |
| `ssh-keygen`     | Generates a new public-private key pair for authentication.                             |
| Private Key      | Never shared with anyone; it is only used during authentication.                        |
| Public Key       | Needs to be exported to `~/.ssh/authorized_keys` on the server you need to connect to.  |
| `ssh-copy-id`    | Helps in copying public keys to the `authorized_keys` file on the remote server.        |

## Example Workflow

1. **Configure SSH on the server**:
   ```bash
   cat >> /etc/ssh/sshd_config <<EOF
   RSAAuthentication yes
   PubKeyAuthentication yes
   EOF

   service sshd restart
   ```

2. **Generate a key pair on the client**:
   ```bash
   ssh-keygen -t rsa
   ```

3. **Copy the public key to the remote server**:
   ```bash
   ssh-copy-id -i ~/.ssh/id_rsa.pub user@targetserver
   ```

4. **Log into the remote server**:
   ```bash
   ssh -i ~/.ssh/id_rsa user@targetserver
   ```

With these steps, you should be able to set up password-less SSH logins, making remote administration simpler and more secure.