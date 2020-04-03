# docker网络

#### 查看docker网络

```
➜  / docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
bd9d87163b57        bridge              bridge              local
b2cc6f5037e8        host                host                local
2426d03c60e8        none                null                local
```

`docker run`运行容器时，可以通过`--net`指定上面三个中的一个网络，默认bridge

* `bridge`代表Docker在宿主机中创建的docker0网络
* `none`意味着不指定网络，使用这个网络的容器中没有网卡
* 指定`host`网络会将容器加入宿主机所在的网络中，在使用这个网络的容器中其网络配合和宿主机一样



#### 查看网络详情

```
➜  / docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "bd9d87163b57ef91223f28af6392f75824fa5b2e0ad23bfa3e572d9e1ad8292c",
        "Created": "2020-03-29T09:25:18.057984111Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
```

* 同一个子网内的容器可以进行通信
* 通过 **--link** 设置，使容器之间通过名字相互通信。（不建议使用）



### 自定义网络

#### bridge network

创建bridge 

```
➜  / docker network create --subnet=192.167.0.0/16 etcdnet
b1c43ddb48d2c2704e653415b67a000d14e4b729af0c633e8202647680fa5950
➜  / docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
bd9d87163b57        bridge              bridge              local
b1c43ddb48d2        etcdnet             bridge              local
b2cc6f5037e8        host                host                local
2426d03c60e8        none                null                local
➜  /
```

* 加入这个`bridge`网络的容器必须在同一个宿主机上，同一个网络中的容器之间可以直接通信，但不能和不同网络的容器通信。
* 用户自定义的`bridge`网络中，`--link`是不支持的
* 单个宿主机上创建一个小型网络，`bridge`网络是很有用的。但如果想创建更大的网络，尤其是跨越多个宿主机的网络，那就需要创建`overlay`网络。



#### 创建overlay网络

* `overlay`支持在多个主机之间创建网络
* `overlay`网络需要一个键值对存储服务(key-value store service)才能工作，Docker目前支持Consul，Etcd和ZooKeeper

