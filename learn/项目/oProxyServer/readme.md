# ♾️网关模块
## 💫http处理器
### 入口 - httpServer
> package o_proxy_server.web_module.http_module.httpserver.cj
![[httpserver-class.png]]

> 成员变量
> - server: 标准库中http.Server, 用来启动http端口

> 成员函数
> - run: 启动监听器

### 路由处理器 - HttpDistributor
> 自定义路由处理器, 需要继承标准库中http.HttpRequestDistributor
```java
public class HttpDistributor <: http.HttpRequestDistributor {
    public func distribute(_: String): http.HttpRequestHandler {
        return RootHandler()
    }

    public func register(_: String, _: (http.HttpContext) -> Unit): Unit {
    }

    public func register(_: String, _: http.HttpRequestHandler): Unit {
    }
}

public class RootHandler <: http.HttpRequestHandler {
    public func handle(ctx: http.HttpContext): Unit {
        let myCtx = middleware.Context(ctx.request, ctx.responseBuilder)
        myCtx.next()
    }
}
```

> 在构造server时注册自定义处理器
```java
public init() {
	server = http.ServerBuilder().addr(addr).port(port).distributor(HttpDistributor()).build()
}
```

## 💫插件模块
### 上下文 - Context(核心)
![[context-class.png]]



