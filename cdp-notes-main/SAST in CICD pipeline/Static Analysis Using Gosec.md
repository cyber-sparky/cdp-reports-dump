
## Install Golang

```sh
curl -s https://dl.google.com/go/go1.17.4.linux-amd64.tar.gz | tar xvz -C /usr/local
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

## Install gosec

```sh
curl -sfL https://raw.githubusercontent.com/securego/gosec/master/install.sh | sh -s -- -b $(go env GOPATH)/bin v2.4.0
```

## Running a scan 

```sh
gosec ./...
```

## To exclude specific rules while scanning 

```sh
gosec -exclude=<ruleId> ./...
```
