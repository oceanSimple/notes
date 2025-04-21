
# ♾️消息格式

## 💫Message

```csharp
[method: JsonConstructor]  
public class Message(EMessageType type, string data) {  
    private static ulong _id;  
  
    [JsonPropertyName("data")] public string Data { get; set; } = data;  
  
    [JsonPropertyName("type")] public EMessageType Type { get; set; } = type;  
  
    [JsonPropertyName("id")] public ulong Id { get; set; } = _id++;  
  
    public override string ToString() {  
        return $"Type: {Type} \n Data: {Data}";  
    }  
  
    public string ToJson() {  
        return JsonSerializer.Serialize(this);  
    }  
}
```

> 属性

- EMessageType: 消息的种类, 目前主要是区分请求报文和响应报文
- Data: 报文的string格式. 当前仅支持 JSON 格式, 因此这里面存放的是报文的json格式字符串

## 💫OWSP

> 全称: ocean websocket protocol
> 本项目自定义的网络通信协议, 仿照HTTP协议

### 请求报文

> 包含`请求行`, `请求头`, `请求体`

```csharp
public class OwspRequest {  
    [JsonConstructor]  
    public OwspRequest(string method, string path, string version) {  
        OwspRequestLine = new OwspRequestLine(method, path, version);  
        Headers = new Dictionary<string, string>();  
        Body = "";  
    }  
  
    [JsonPropertyName("requestLine")] public OwspRequestLine OwspRequestLine { get; set; }  
    [JsonPropertyName("headers")] public Dictionary<string, string> Headers { get; set; }  
    [JsonPropertyName("body")] public string Body { get; set; }  
  
    public override string ToString() {  
        var headers = string.Join("\r\n", Headers.Select(h => $"{h.Key}: {h.Value}"));  
        return $"{OwspRequestLine}\r\n{headers}\r\n\r\n{Body}";  
    }  
  
    public string ToJson() {  
        return JsonSerializer.Serialize(this);  
    }  
}
```

> 请求行, 用来存放`请求方法`, `请求路径`, `协议版本`

示例如下
```
POST / OWSP/2.0
```

> 请求头, 用来存放请求配置信息

```
Content-Type: application/json
Key: Value
```

> 请求体, 存放要传递的数据


请求报文, Raw格式如下:
```
POST / OWSP/2.0
Content-Type: application/json
Key: Value

test request body
```

Json格式如下

```json
{"requestLine":{"method":"POST","path":"/","query":"OWSP/2.0"},"headers":{"Content-Type":"application/json","Key":"Value"},"body":"test request body"}
```

### 响应报文

> 包含`响应行`, `响应头`, `响应体`


```csharp
public class OwspResponse {  
    [JsonConstructor]  
    public OwspResponse() {  
        OwspResponseLine = new OwspResponseLine();  
        Headers = new Dictionary<string, string>();  
        Body = "";  
    }  
  
    [JsonPropertyName("responseLine")] public OwspResponseLine OwspResponseLine { get; set; }  
    [JsonPropertyName("headers")] public Dictionary<string, string> Headers { get; set; }  
    [JsonPropertyName("body")] public string Body { get; set; }  
  
    public override string ToString() {  
        var response = $"{OwspResponseLine}\r\n";  
        foreach (var header in Headers) response += $"{header.Key}: {header.Value}\r\n";  
  
        response += "\r\n" + Body;  
        return response;  
    }  
  
    public string ToJson() {  
        return JsonSerializer.Serialize(this);  
    }  
}
```

> 响应行, 存放`协议版本`, `响应码`, `响应状态`

```
OWSP/2.0 404 Not Found
```

> 响应头, 用来存放请求配置信息


> 响应体, 存放要传递的数据

```ad-warning
注意, 响应体的结构必须为ResponseResult格式
```

```csharp
public class ResponseResult {  
    [JsonConstructor]  
    public ResponseResult(int code, string data, string msg) {  
        Msg = msg;  
        Code = code;  
        Data = data;  
    }  
  
    [JsonPropertyName("msg")] public string Msg { get; set; }  
    [JsonPropertyName("code")] public int Code { get; set; }  
    [JsonPropertyName("data")] public string Data { get; set; }  
  
    public override string ToString() {  
        return $"msg: {Msg}, code: {Code}, data: {Data}";  
    }  
  
    public string ToJson() {  
        return JsonSerializer.Serialize(this);  
    }  
}
```


响应报文, Raw格式如下

```
OWSP/2.0 404 Not Found
Content-Type: application/json
Key: Value

msg: ok, code: 200, data: test response body
```

Json格式如下
```json
{"responseLine":{"Version":"OWSP/2.0","StatusCode":404,"ReasonPhrase":"Not Found"},"headers":{"Content-Type":"application/json","Key":"Value"},"body":{"msg":"ok","code":200,"data":"test response body"}}
```

## 💫消息种类

```csharp
public enum EMessageType {
    /// <summary>
    ///     System information
    /// </summary>
    OwspSystem = 0,

    /// <summary>
    ///     Use owsp request format
    /// </summary>
    OwspRequest = 1,

    /// <summary>
    ///     Use owsp response format
    /// </summary>
    OwspResponse = 2
}
```

消息分为三大类: 系统消息, 请求消息, 响应消息
- OwspSystem: 系统消息, 大部分为预制的系统消息
- OwspRequest: 请求报文
- OwspResponse: 响应报文


# ♾️请求消息































