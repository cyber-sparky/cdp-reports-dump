
Nmap (“Network Mapper”) is a free and open source (license) utility for network discovery and security auditing. Many systems and network administrators also find it useful for tasks such as network inventory, managing service upgrade schedules, and monitoring host or service uptime.

## Installing Nmap 

```sh
apt-get update && apt-get install nmap -y
```

## Run a nmap scan and output in xml 

```sh
nmap prod-oiahxczk -oX nmap_out.xml
```

