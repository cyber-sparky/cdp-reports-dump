
## Start scan with an interactive UI 

```sh
inspec shell -t ssh://root@prod-oiahxczk -i ~/.ssh/id_rsa --chef-license accept
```

## Analyzing path enviornment variables 

```sh
inspec> os_env('PATH')
=> Environment variable PATH
inspec> os_env('PATH').content
=> "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"
```

