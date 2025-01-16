
## Inspect a docker profile 

```sh
inspec exec https://github.com/dev-sec/cis-docker-benchmark.git --chef-license accept
```

## Create a docker container and inspect 

```sh
docker run -d --name alpine -it alpine /bin/sh
inspec exec https://github.com/dev-sec/linux-baseline.git --chef-license accept -t docker://alpine
```

