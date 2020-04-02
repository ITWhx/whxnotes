# websocket协议

## 特点

*  Websocket是一个**持久化**的协议，相对于HTTP这种**非持久**的协议来说 
*  HTTP的生命周期通过Request来界定，也就是一个Request 一，Response，那么**在**HTTP1.0**中**，这次HTTP请求就结束了 
* 在HTTP1.1中进行了改进，使得有一个keep-alive，也就是说，在一个HTTP连接中，可以发送多个Request，接收多个Response。
  但是请记住 Request = Response ， 在HTTP中永远是这样，也就是说一个request只能有一个response。而且这个response也是**被动**的，不能主动发起。
*  Websocket是基于HTTP协议的，或者说**借用**了HTTP的协议来完成一部分握手。 
* Websocket协议服务端可以主动推送消息给客户端