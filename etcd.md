# etcd

## 搭建集群

在一台机器配置了3个容器，在机器上创建一个子网络，三台容器在一个网络里。

* 创建子网

```
docker network create --subnet=192.167.0.0/16 etcdnet 
```

* 单机版

```
docker run -d -p 2379:2379 -p 2380:2380 --restart=always --net etcdnet --ip 192.167.0.168 --name etcd0 quay.io/coreos/etcd /usr/local/bin/etcd --name etcd0 -advertise-client-urls http://192.167.0.168:2379 -listen-client-urls http://0.0.0.0:2379 -initial-advertise-peer-urls http://192.167.0.168:2380 -listen-peer-urls http://0.0.0.0:2380 -initial-cluster-token etcd-cluster -initial-cluster etcd0=http://192.167.0.168:2380 -initial-cluster-state new
```



* 启动三个etcd容器（集群版）

**etcd0**

```
docker run -d -p 2379:2379 -p 2380:2380 --restart=always --net etcdnet --ip 192.167.0.168 --name etcd0 quay.io/coreos/etcd /usr/local/bin/etcd --name etcd0 -advertise-client-urls http://192.167.0.168:2379 -listen-client-urls http://0.0.0.0:2379 -initial-advertise-peer-urls http://192.167.0.168:2380 -listen-peer-urls http://0.0.0.0:2380 -initial-cluster-token etcd-cluster -initial-cluster etcd0=http://192.167.0.168:2380,etcd1=http://192.167.0.170:2480,etcd2=http://192.167.0.172:2580 -initial-cluster-state new
```

**etcd1**

```
 docker run -d -p 2479:2479 -p 2480:2480 --restart=always --net etcdnet --ip 192.167.0.170 --name etcd1 quay.io/coreos/etcd /usr/local/bin/etcd --name etcd1 -advertise-client-urls http://192.167.0.170:2479 -listen-client-urls http://0.0.0.0:2479 -initial-advertise-peer-urls http://192.167.0.170:2480 -listen-peer-urls http://0.0.0.0:2480 -initial-cluster-token etcd-cluster -initial-cluster etcd0=http://192.167.0.168:2380,etcd1=http://192.167.0.170:2480,etcd2=http://192.167.0.172:2580 -initial-cluster-state new
```

**etcd2**

```
 docker run -d -p 2579:2579 -p 2580:2580 --restart=always --net etcdnet --ip 192.167.0.172 --name etcd2 quay.io/coreos/etcd /usr/local/bin/etcd --name etcd2 -advertise-client-urls http://192.167.0.172:2579 -listen-client-urls http://0.0.0.0:2579 -initial-advertise-peer-urls http://192.167.0.172:2580 -listen-peer-urls http://0.0.0.0:2580 -initial-cluster-token etcd-cluster -initial-cluster etcd0=http://192.167.0.168:2380,etcd1=http://192.167.0.170:2480,etcd2=http://192.167.0.172:2580 -initial-cluster-state new
```

* 测试命令

  ```
  插入key
  curl -L http://localhost:2379/v2/keys/name4 -XPUT -d value="hello"
  
  获取key
  curl -L http://localhost:2579/v2/keys/name4
  
  获取集群结点
  curl -L http://localhost:2479/v2/members
  
  
  ```

  