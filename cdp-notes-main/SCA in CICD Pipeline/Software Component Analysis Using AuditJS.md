
## Before Install AuditJS 

```bash
mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | tee /etc/apt/sources.list.d/nodesource.list
apt update
```

## Install AuditJS

```bash
npm install -g auditjs
```

> auditjs provides you with the ability to use two different indices i.e., either Nexus IQ Server or Sonatype OSS Index.

## Scan and output it in JSON format 

```sh
auditjs ossi 
auditjs ossi -q -j | tee auditjs-output.json
```