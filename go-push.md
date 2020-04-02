# gateway

1.创建一个连接管理器

```go
connMgr = &ConnMgr{
		buckets: make([]*Bucket, G_config.BucketCount),
		jobChan: make([]chan*PushJob, G_config.BucketCount),
		dispatchChan: make(chan*PushJob, G_config.DispatchChannelSize),
	}
```

* bucket包含一组用户ws连接，将所有连接打散到一个buckets数组
* jobChan,每个bucket对应一个jobChan,用来存放要发往客户端的消息。
* dispatchChan ，需要分发的消息

1.1.遍历buckets，为每一个bucket对应的jobchan开启多个协程，获取job分发到bucket中

1.2.初始化分发协程, 用于将消息扇出给各个Buckets数组



2 .初始化websocket服务器

* 创建请求路由handleConnect，接收请求wsUpgrader.Upgrade转换成ws协议

* 创建唯一标识， G_wsServer.curConnId，原子操作加 1

* 初始化websockect读写协程

  * 创建封装的WSConnection对象

  * ```go
    wsConnection = &WSConnection{
    		wsSocket: wsSocket,
    		connId: connId,
    		inChan: make(chan *common.WSMessage, G_config.WsInChannelSize),
    		outChan: make(chan *common.WSMessage, G_config.WsOutChannelSize),
    		closeChan: make(chan byte),
    		lastHeartbeatTime: time.Now(),
    		rooms: make(map[string]bool),
    	}
    ```

  * 启动读协程，不断从wsconnection读取消息，**写入inchan**

  * 启动写协程，循环从**outChan读取消息**，调用wsSocket.WriteMessage，**发送给客户端**

* 调用wsConn.WSHandle()，开始处理websocket消息

  * 连接加入管理器connMgr，根据connId mod bucket长度，存入bucket的map中

* 每个websock连接启动一个协程，进行心跳检测

  * 使用time.NewTime()函数，每隔1秒, 检查一次连接是否健康
  * 检测时，需要加锁wsConnection.mutex.Lock() （防止lastHeartbeatTime并发访问）
  * 判断当前时间 和 wsConnection.lastHeartbeatTime间隔 是否大于心跳间隔时间。是 则心跳超时，关闭wsConnection.Close()
  * 判断连接关闭变量wsConnection.isClosed

* websocks请求处理

  * 循环阻塞读取客户端发来的消息wsConnection.ReadMessage()
  * 解析消息成{"type": "PING", "data": {...}}的包
  * 三种请求类型，对应不同的处理方式

  ```
  // 1,收到PING则响应PONG: {"type": "PING"}, {"type": "PONG"}
  // 2,收到JOIN则加入ROOM: {"type": "JOIN", "data": {"room": "chrome-plugin"}}
  // 3,收到LEAVE则离开ROOM: {"type": "LEAVE", "data": {"room": "chrome-plugin"}}
  ```

  * ping  调用wsConnection.KeepAlive()更新心跳时间WSConnection.lastHeartbeatTime=now，返回pong包

    ```go
    bizResp = &common.BizMessage{
    		Type: "PONG",
    		Data: json.RawMessage(buf),
    	}
    ```

  * JOIN 相当于用户上线后，进入某个房间

    * 判断用户连接加入房间数是否超过限制  `len(wsConnection.rooms) >= G_config.MaxJoinRoom`
    * 判断用户是否已经加入此房间 `_, existed = wsConnection.rooms[bizJoinData.Room]; existed`
    * 建立房间 到连接WSConnection的关系，加入对应bucket对象中的rooms数组，并且将连接加入room `room.id2Conn[wsConn.connId] = wsConn`
    * 建立连接 到 房间的关系  `wsConnection.rooms[bizJoinData.Room] = true`

  * 处理LEAVE请求，用户离开房间

    * 删除房间 到 连接的关系，根据wsConnection，找到所属bucket，再找到room对象，删除包含的连接`delete(room.id2Conn, wsConn.connId)`
    * 判断room包含连接数为0，表示房间无人，则从bucket删除对应room

  

     

​        



