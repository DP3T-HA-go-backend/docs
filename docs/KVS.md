[Back to Index](../README.md)

# KVS schemas, structure, configuration parameters


## Go and etcd

- ### `etcd/clientv3` is the Go official etcd client
- ### Install

```bash
> go get go.etcd.io/etcd/clientv3
```

## Command line to test etcd keys
```bash
ETCDCTL_API=3 etcdctl --endpoints=https://10.0.26.10:2379,https://10.0.26.11:2379,https://10.0.26.13:2379 --cacert=/etc/ssl/etcd/ssl/ca.pem --cert=/etc/ssl/etcd/ssl/node-node1.pem --key=/etc/ssl/etcd/ssl/node-node1-key.pem
```

## Necessary
Adding this in `go.mod`

```go
require google.golang.org/grpc v1.26.0
replace github.com/coreos/go-systemd => github.com/coreos/go-systemd/v22 v22.0.0
```

