# ♾️结构设计
💫 server
- Connections：所有连接的map，k是userId，v是Connection对象
- DelChan：监听操作

💫 Connection
- WsConn：ws成功连接后的对象
- Sc：消息管道
- UserId：用户id

## 1. 建立连接
1. 客户端发送ws连接请求`ws://127.0.0.1:8080/ws/1`
2. 服务器收到路由请求，将http升级到websocket
3. 从路径中拿到userId：1，用ws连接、userId以及new的Sc生成Connection对象，存入server的Connections
4. 开启该connection的写协程和读协程监听

## 2. 转发消息
1. 客户1向服务器发送消息
```json
{
    "type": 1,
    "content": "hello i am ws1",
    "receiver": 2,
    "send": 1
}
```
2. 客户1对应的connection的读协程获得message
3. 遍历server.connections，获取receiver对应的conn
4. 将message写入conn.Sc，即通知该conn要发送消息了


# ♾️定义消息结构体
```go
const (  
    CONNECT = iota // 建立连接  
    SEND           // 发送消息  
    RECEIVE        // 接收消息  
    CLOSE          // 关闭连接  
)  
  
type Message struct {  
    Type     uint64 `json:"type"`     // 消息类型  
    Sender   uint64 `json:"sender"`   // 发送者的id  
    Receiver uint64 `json:"receiver"` // 接收者的id  
    Content  string `json:"content"`  // 消息内容  
}
```

# ♾️实体类
## Connection
```go
type Connection struct {  
    WsConn *websocket.Conn // websocket连接  
    Sc     chan []byte     // 发送消息的channel  
    UserId uint64          // 用户id  
}
```
- UserId：Connection的归属
- WsConn：ws连接对象
- Sc：写协程堵塞等待这个通道，一旦有其他协程向该通道写入数据，写协程就将数据发送给客户端

```ad-info
这是ws连接的读协程，当接收到客户端发送的消息后，根据消息的type进行不同的处理
```

> 发送消息的逻辑：从server的connection列表中获取目标conn，然后往该conn的Sc中写入数据。这样目标conn的写协程会收到任务，将数据发送给目标。
```go
// 持续监听websocket连接消息，并将消息写入管道  
func (c *Connection) reader() {  
    for {  
       // 第一个参数为消息类型(text or binary)，第二个参数为消息内容  
       _, msg, err := c.WsConn.ReadMessage()  
       if err != nil {  
          // 当websocket连接关闭时，关闭sc管道  
          server.DelConn <- c.UserId  
          break  
       }  
       // 将json消息解析为Message结构体  
       var message Message  
       err = json.Unmarshal(msg, &message)  
       if err != nil {  
          panic(err)  
       }  
       switch message.Type {  
       case SEND:  
          // 将消息发送给接收者  
          if v, ok := server.Connections[message.Receiver]; ok {  
             v.Sc <- msg  
          }  
       case CLOSE:  
          // 关闭连接  
          server.DelConn <- c.UserId  
       default:  
          fmt.Println("unknown message type")  
       }  
    }  
}
```

```ad-info
这是ws连接的写协程
```
```go
// 持续监听管道消息，并将消息写入websocket连接  
func (c *Connection) writer() {  
    for message := range c.Sc {  
       err := c.WsConn.WriteMessage(websocket.TextMessage, message)  
       if err != nil {  
          break  
       }  
    }  
    // 当连接的sc管道关闭时，关闭websocket连接  
    err := c.WsConn.Close()  
    if err != nil {  
       panic(err)  
    }  
}
```

## Server
```go
type Server struct {  
    Connections map[uint64]*Connection // 保存所有连接  
    DelConn     chan uint64            // 根据发送的userId删除连接  
}
```
- Connections：保存所有在线的用户连接
- DelConn：用来触发事件（里面内容可选）
> chan的好处是对Connections的写操作全部交给一个协程进行，避免多线程问题

```go
func (server *Server) Run() {  
    // 循环监听channel  
    for {  
       select {  
       case userId := <-server.DelConn:  
          // 删除连接(首先判断连接是否存在，若关闭不存在的channel会引发panic)  
          if v, ok := server.Connections[userId]; ok {  
             delete(server.Connections, userId)  
             close(v.Sc)  
          }  
       }  
    }  
}
```

# ♾️请求处理
> 用来升级http协议
```go
var upgrader = websocket.Upgrader{  
    ReadBufferSize:  512,  
    WriteBufferSize: 512,  
    CheckOrigin:     func(r *http.Request) bool { return true },  
}
```
> 升级http协议，建立ws连接，并保存到server
```go
// Upgrade 建立连接  
func Upgrade(w http.ResponseWriter, r *http.Request) {  
    var err error  
    // 从请求路径中拿到userId  
    vars := mux.Vars(r)  
    id, err := strconv.Atoi(vars["id"])  
    if err != nil {  
       panic(err)  
    }  
    // 升级http协议到websocket协议  
    ws, err := upgrader.Upgrade(w, r, nil)  
    if err != nil {  
       panic(err)  
    }  
    // 初始化连接  
    var conn = &Connection{  
       WsConn: ws,  
       Sc:     make(chan []byte),  
       UserId: uint64(id),  
    }  
    // 将连接加入到server中  
    server.Connections[uint64(id)] = conn  
    // 启动连接的读写协程  
    go conn.writer()  
    go conn.reader()  
}
```

# ♾️主函数
```go
func main() {  
    router := mux.NewRouter()  
    router.HandleFunc("/ws/{id}", model.Upgrade)  
    go model.GetServer().Run()  
    //启动http服务  
    if err := http.ListenAndServe("127.0.0.1:8080", router); err != nil {  
       fmt.Println("err:", err)  
    }  
}
```