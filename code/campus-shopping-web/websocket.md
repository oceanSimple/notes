```ad-note
下载包
```
```shell
go get github.com/gorilla/mux v1.8.1
go get github.com/gorilla/websocket v1.5.1
```
# ♾️消息类型
## 💫Message类
```go
type Message struct {  
    Type     uint64 `json:"type"`     // 消息类型  
    Sender   uint64 `json:"sender"`   // 发送者的id  
    Receiver uint64 `json:"receiver"` // 接收者的id  
    Content  string `json:"content"`  // 消息内容  
}
```

> 消息种类

```go
const (  
    CONNECT       = iota // 建立连接  
    SEND                 // 1 to 1 发送消息  
    SystemMessage        // 系统消息  
    BROADCAST            // 广播消息  
    CLOSE                // 关闭连接  
)
```

## 💫消息示例
```ad-note
客户端消息
```
1. 发送消息

> - 服务器将该消息原封不动直接转发给接收方。
> - 消息发送后，会收到服务器消息返回（成功发送 or 对方不在线）

```json
{
    "type": 1,
    "content": "hello i am ws1",
    "receiver": 2,
    "send": 1
}
```

```ad-note
系统消息
```

1. 成功发送消息
```json
{
    "type": 2,
    "sender": 0,
    "receiver": 2,
    "content": "success send"
}
```
2. 对方不在线
```json
{
    "type": 2,
    "sender": 0,
    "receiver": 2,
    "content": "not online"
}
```

# ♾️Hub
```go
type Hub struct {  
    Clients    map[uint64]*Client // 客户端集合  
    Broadcast  chan []byte        // 广播消息处理  
    Register   chan *Client       // 注册chan  
    Unregister chan uint64        // 注销chan  
}
```
- clients：用于保存所有连接
- 剩下的管道用于监听事件（ps：Clients对象只能由hub进行操作，避免多线程问题）

```ad-info
监听管道事件函数
```
```go
func (h *Hub) Run() {  
    for {  
       select {  
       case client := <-h.Register:  
          // 注册客户端  
          h.Clients[client.UserId] = client  
       case userId := <-h.Unregister:  
          if client, ok := h.Clients[userId]; ok {  
             // 关闭客户端连接  
             err := client.WsConn.Close()  
             if err != nil {  
                log.Println("关闭连接失败", err)  
             }  
             // 注销客户端  
             delete(h.Clients, userId)  
             // 关闭发送消息的channel  
             close(client.Send)  
          }  
       case message := <-h.Broadcast:  
          for _, v := range h.Clients {  
             v.Send <- message  
          }  
       }  
    }  
}
```

# ♾️Client
```go
const (  
    // Time allowed to write a message to the peer.  
    writeWait = 10 * time.Second  
  
    // Time allowed to read the next pong message from the peer.  
    pongWait = 10 * time.Second  
  
    // Send pings to peer with this period. Must be less than pongWait.  
    pingPeriod = (pongWait * 9) / 10  
  
    // Maximum message size allowed from peer.  
    maxMessageSize = 512  
)
```
```go
type Client struct {  
    Hub    *Hub            // 客户端所属的hub  
    UserId uint64          // 客户端id  
    WsConn *websocket.Conn // websocket连接  
    Send   chan []byte     // 发送消息的channel  
}
```
- Hub：将全局hub的地址复制给每个client，方便client通过hub的管道触发事件
- UserId：标识符
- WsConn：ws连接对象
- Send：发送消息的管道。当该管道有消息时，直接推送给客户端

```ad-info
client的写协程
```
```go
func (client *Client) WritePump() {  
    // 开启一个定时器，每隔一段时间向客户端发送一个ping消息  
    ticker := time.NewTicker(pingPeriod)  
  
    defer func() {  
       ticker.Stop()  
       err := client.WsConn.Close()  
       if err != nil {  
          log.Println("关闭连接失败", err)  
       }  
       log.Printf("客户端主动关闭连接: %d", client.UserId)  
    }()  
  
    // 监听channel  
    for {  
       select {  
       // 监听发送消息的channel：Send（有消息就推送给客户端）  
       case message, ok := <-client.Send:  
          // 判断通道是否关闭  
          if !ok {  
             // 如果通道关闭，发送关闭消息，通知客户端关闭连接  
             client.WsConn.WriteMessage(websocket.CloseMessage, []byte{})  
             return  
          }  
  
          // 设置写超时时间  
          client.WsConn.SetWriteDeadline(time.Now().Add(writeWait))  
  
          // 写消息  
          client.WsConn.WriteMessage(websocket.TextMessage, message)  
  
       case <-ticker.C:  
          // 设置写超时时间  
          client.WsConn.SetWriteDeadline(time.Now().Add(writeWait))  
          // 发送ping消息  
          if err := client.WsConn.WriteMessage(websocket.PingMessage, nil); err != nil {  
             return  
          }  
       }  
    }  
}
```

- 开启了一个ticker定时器，每个pingWaits触发一次，然后写协程就向客户端发送ping消息（心跳检测机制）
- 监听Send管道，一旦接收到消息，直接转发给客户端

```ad-info
client的读协程
```
```go
func (client *Client) ReadPump() {  
    // 结束时关闭连接，注销客户端  
    defer func() {  
       client.Hub.Unregister <- client.UserId  
    }()  
  
    client.WsConn.SetReadLimit(maxMessageSize) // 设置读取消息的最大长度  
  
    // ping每隔9s发送一次，如果10s内没有收到pong消息，就关闭连接  
    client.WsConn.SetReadDeadline(time.Now().Add(pongWait)) // 设置读消息的超时时间  
  
    // 设置pong消息处理函数  
    client.WsConn.SetPongHandler(func(string) error {  
       // 如果收到pong消息，重置读消息的超时时间  
       client.WsConn.SetReadDeadline(time.Now().Add(pongWait))  
       return nil  
    })  
  
    for {  
       var message []byte  
       _, message, err := client.WsConn.ReadMessage() // 读取消息  
  
       // 判断是否读取消息失败  
       if err != nil {  
          // 如果读取消息失败，判断是否是连接被关闭的错误  
          if websocket.IsUnexpectedCloseError(err, websocket.CloseGoingAway, websocket.CloseAbnormalClosure) {  
             log.Printf("error: %v", err)  
          }  
          // 中止循环  
          break  
       }  
  
       // 将json消息解析为Message结构体  
       var msg Message  
       if err := json.Unmarshal(message, &msg); err != nil {  
          log.Println("json unmarshal error:", err)  
          return  
       }  
  
       // 根据消息类型进行不同的处理  
       switch msg.Type {  
       case 1:  
          if v, ok := client.Hub.Clients[msg.Receiver]; ok {  
             // 如果接收者在线，发送消息  
             v.Send <- message  
             // 给发送者返回发送成功的消息  
             client.Send <- SuccessSend(msg.Sender, msg.Receiver)  
          } else {  
             // 如果接收者不在线，给发送者返回不在线的消息  
             client.Send <- NotOnline(msg.Sender, msg.Receiver)  
          }  
       default:  
          client.Hub.Broadcast <- message  
       }  
    }  
}
```
- 因为写协程每9s会发送一个ping消息，因此如果我们若连续10s内每收到客户端发送的消息，说明连接出现了问题。关闭连接。
- 在pong处理函数中，每当我们收到pong消息回复后，就将读等待时长延长10s等待下一次pong回复
- 函数主体部分阻塞读与客户端的消息，如果是pong消息，由pong处理函数处理；如果是textMessage，项目规定了message的json格式，因此直接转换成Message对象，然后根据message的type进行不同处理。

# ♾️upgrader
> 协议升级函数

```go
func GetUpgrader() websocket.Upgrader {  
    var upgrader = websocket.Upgrader{  
       ReadBufferSize:  512,  
       WriteBufferSize: 512,  
       CheckOrigin:     func(r *http.Request) bool { return true },  
    }  
    return upgrader  
}
```

# ♾️main
```go
// hub 用于管理所有的连接  
var hub = component.NewHub()  
  
// upgrader 用于将http协议升级到websocket协议  
var upgrader = component.GetUpgrader()
```
```ad-info
将http协议升级成websocket，并将连接加入hub
```
```go
// Upgrade 建立连接  
func Upgrade(w http.ResponseWriter, r *http.Request) {  
    var err error  
    // 从请求路径中拿到userId  
    vars := mux.Vars(r)  
    id, err := strconv.Atoi(vars["id"])  
    if err != nil {  
       log.Println("请求参数不是数字", err)  
       return  
    }  
    // 升级http协议到websocket协议  
    ws, err := upgrader.Upgrade(w, r, nil)  
    if err != nil {  
       log.Println("升级协议失败", err)  
    }  
    // 初始化连接  
    var client = component.NewClient(hub, uint64(id), ws)  
    hub.Register <- client  
  
    // 启动连接的读写协程  
    go client.WritePump()  
    go client.ReadPump()  
}
```
```ad-info
main：开启ws监听服务
```
```go
func main() {  
    router := mux.NewRouter()  
    router.HandleFunc("/ws/{id}", Upgrade)  
    go hub.Run()  
    //启动http服务  
    if err := http.ListenAndServe("127.0.0.1:8080", router); err != nil {  
       fmt.Println("err:", err)  
    }  
}
```
