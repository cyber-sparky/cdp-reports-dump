# Docker Network Drivers Overview

| Network Driver | Descriptions |
|----------------|--------------|
| **bridge**     | Default network driver. Containers on the same bridged network can communicate with each other but are isolated from containers on other networks. All containers can access the external network through NAT. |
| **host**       | Uses the host’s networking directly. Exposes containers' ports on the host’s IP address. Provides high performance but reduces isolation between host and containers. |
| **macvlan**    | Each container on a macvlan network gets its own MAC address and can appear as a physical device on the network, enhancing container network isolation. Requires careful configuration to avoid network conflicts. |
| **none**       | Disables networking completely. Containers cannot communicate with each other or the external network. Useful in scenarios where networking is not required. |



## Docker Network Management Commands

| Command                        | Description |
|--------------------------------|-------------|
| `docker network create <name>` | Creates a new network with the specified name. |
| `docker network ls`            | Lists all networks on the Docker host. |
| `docker network inspect <name>`| Displays detailed information about a network. |
| `docker network connect <network> <container>` | Connects a container to a network. |
| `docker network disconnect <network> <container>` | Disconnects a container from a network. |
| `docker network rm <name>`     | Removes a network. |

## Bridged Network

1. **Create a network:**
   ```bash
   docker network create mynetwork
   ```

2. **Run a container attached to the network:**
   ```bash
   docker run -d --name ubuntu --network mynetwork -it ubuntu:20.04
   ```

3. **Verify network details:**
   ```bash
   docker network inspect mynetwork
   ```

4. **Test network connectivity within the container:**
   ```bash
   docker exec ubuntu apt update
   ```

5. **Clean up:**
   ```bash
   docker rm -f ubuntu
   docker network rm mynetwork
   ```

### Additional Resources

- [Docker Network Documentation](https://docs.docker.com/network/)
- [More about macvlan network driver](https://docs.docker.com/network/macvlan/)

## macvlan 

Here's the table without mentioning outputs and focusing on key details:

| **Topic**                            | **Details**                                                                                              |
|--------------------------------------|----------------------------------------------------------------------------------------------------------|
| **Create macvlan Network**           | Use `docker network create --driver macvlan mymacvlan`                                                   |
| **Check Network List**               | Verify using `docker network list`                                                                        |
|                                      | - Note the network `mymacvlan` with `macvlan` driver                                                      |
| **View Network Interfaces**           | Check with `ifconfig`                                                                                    |
|                                      | - Look for an interface starting with `dm-`                                                               |
| **Attach Network to Container**      | Connect using `docker run --network mymacvlan ...`                                                        |
| **Inspect Container Network Details**| Investigate with `docker inspect <container> -f "{{json .NetworkSettings.Networks }}" \| jq`             |
|                                      | - Note IP address, Gateway, MacAddress                                                                   |
| **Cleanup**                          | Remove container with `docker rm -f <container>`                                                          |
|                                      | Remove network with `docker network rm mymacvlan`                                                         |
| **Question**                         | What is the difference between bridge and macvlan driver?                                                 |
| **Learn more**                       | [Docker Macvlan Documentation](https://docs.docker.com/network/macvlan/)                                  |


## None 


| **Topic**                            | **Details**                                                                                              |
|--------------------------------------|----------------------------------------------------------------------------------------------------------|
| **Create `none` Network**            | Use `docker run -d --name ubuntu --network=none -it ubuntu:20.04`                                         |
| **Inspect Network Details**          | Check with `docker inspect ubuntu -f "{{json .NetworkSettings.Networks }}" \| jq`                        |
|                                      | - Note network `none` with no IP, Gateway, or MacAddress                                                  |
| **Test Network Connectivity**        | Execute `docker exec ubuntu apt update` to test connectivity                                               |
|                                      | - Results in failure to resolve external repositories                                                     |
| **Observation**                      | The `none` network isolates the container from external networks, including DNS servers.                  |

## Challenges

### Create a network with bridge driver called app and define 172.10.2.0/16 as a subnet

```bash
/# docker network create --subnet 172.10.2.0/16 app
c91fe3c2720992e1e2fb31d71d51ae15635584a7ae1d25b9d1b597dc1e716028
```

### Run the ubuntu:20.04 container in the background and ensure it remains running so that it can later be attached to a network and give this container the name myubuntu

```bash
/# docker run -d -it --name myubuntu ubuntu:20.04
96733b5a65c0bde43fa27f6d4398ef78306caf4dbbdb4e1da5b2705077762562
```

### Attach app network to the myubuntu container

```bash
/# docker network connect app myubuntu
/# docker inspect myubuntu
[
    {
        "Id": "96733b5a65c0bde43fa27f6d4398ef78306caf4dbbdb4e1da5b2705077762562",
        "Created": "2024-07-08T11:06:19.359595905Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 5160,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2024-07-08T11:06:19.910995225Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5f5250218d28ad6612bf653eced407165dd6475a4daf9210b299fed991e172e9",
        "ResolvConfPath": "/var/lib/docker/containers/96733b5a65c0bde43fa27f6d4398ef78306caf4dbbdb4e1da5b2705077762562/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/96733b5a65c0bde43fa27f6d4398ef78306caf4dbbdb4e1da5b2705077762562/hostname",
        "HostsPath": "/var/lib/docker/containers/96733b5a65c0bde43fa27f6d4398ef78306caf4dbbdb4e1da5b2705077762562/hosts",
        "LogPath": "/var/lib/docker/containers/96733b5a65c0bde43fa27f6d4398ef78306caf4dbbdb4e1da5b2705077762562/96733b5a65c0bde43fa27f6d4398ef78306caf4dbbdb4e1da5b2705077762562-json.log",
        "Name": "/myubuntu",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "private",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": null,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/957e1675a997af7a129473fd4a83e88d972bd060ecae65b5a78a0748c756b7b7-init/diff:/var/lib/docker/overlay2/6cd2d2fe4e31b044430737437ef3bde99d4aead883e21abb4cfb27f45f0e4aaf/diff",
                "MergedDir": "/var/lib/docker/overlay2/957e1675a997af7a129473fd4a83e88d972bd060ecae65b5a78a0748c756b7b7/merged",
                "UpperDir": "/var/lib/docker/overlay2/957e1675a997af7a129473fd4a83e88d972bd060ecae65b5a78a0748c756b7b7/diff",
                "WorkDir": "/var/lib/docker/overlay2/957e1675a997af7a129473fd4a83e88d972bd060ecae65b5a78a0748c756b7b7/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "96733b5a65c0",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "ubuntu:20.04",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.version": "20.04"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "14b0dca753a99f98962a34c6cb18c72011fbf6fac62259f711cbfb0fa0c0f672",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/14b0dca753a9",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "917a31ccd8f9c603dce8c34c851906b0e191469dbdc732ca92bd92f4815997cd",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "app": {
                    "IPAMConfig": {},
                    "Links": null,
                    "Aliases": [
                        "96733b5a65c0"
                    ],
                    "NetworkID": "c91fe3c2720992e1e2fb31d71d51ae15635584a7ae1d25b9d1b597dc1e716028",
                    "EndpointID": "02c32ffb487de75a7b476fc7d3f163f696af6ff70d7b538dc621516bbf97ef3a",
                    "Gateway": "172.10.0.1",
                    "IPAddress": "172.10.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:0a:00:02",
                    "DriverOpts": {}
                },
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "292e7719df573c8de97854364f16db314a6ce02efeda8e1a41bc296f44678404",
                    "EndpointID": "917a31ccd8f9c603dce8c34c851906b0e191469dbdc732ca92bd92f4815997cd",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
/# 
```

### What would happen if we remove the network driver without killing the container?

If we try to remove the network using the following `docker network rm` and network name is `app` and we are faced with the following error 

```bash
/# docker network rm app
Error response from daemon: error while removing network: network app id c91fe3c2720992e1e2fb31d71d51ae15635584a7ae1d25b9d1b597dc1e716028 has active endpoints
```

But if we stop the container and we are able to detach the network from the container 

```bash
/# docker stop myubuntu
myubuntu
/# docker network rm app
app
```