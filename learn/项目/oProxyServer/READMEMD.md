
<div align="center">
<h1>o_proxy_server</h1>
</div>

<p align="center">
<img alt="" src="https://img.shields.io/badge/release-v1.0.1-brightgreen" style="display: inline-block;" />
<img alt="" src="https://img.shields.io/badge/cjc-v0.58.3-brightgreen" style="display: inline-block;" />
<img alt="" src="https://img.shields.io/badge/cjcov-0.0%25-brightgreen" style="display: inline-block;" />
<img alt="" src="https://img.shields.io/badge/state-孵化/毕业-brightgreen" style="display: inline-block;" />
<img alt="" src="https://img.shields.io/badge/domain-HOS/Cloud-brightgreen" style="display: inline-block;" />
</p>

## 介绍
**反向代理服务器** 作为客户端与后端服务器之间的中介，负责接收请求、转发至后端并将响应返回客户端，同时实现负载均衡、安全防护和性能优化等功能。

**o_proxy_server** 是使用**仓颉**语言实现的反向代理服务器, 实现了请求转发、身份验证、负载均衡、路径重写等功能

  

### 项目特性
- 支持https
- 支持动态热重载配置文件
- 支持多种常见负载均衡算法
- 支持开发者便携增加插件
- 支持身份验证功能
- 支持静态缓存功能
- 支持日志功能

  

### 项目计划

- 2025年3月 - 发布`0.1.0`版本
- 2025年4月 - 发布`1.0.0`版本

  

## 项目架构

```
├── CHANGELOG.md        # 变更日志

├── README.md           # 整体介绍

├── config.json         # 配置文件

├── static

	└── log             # 日志文件存储目录

└── src                 # 源码目录

	├── test            # 单元测试代码

    ├── config          # 配置文件读写

    ├── global          # 全局变量

    ├── log             # 日志 Builder

    ├── model           # 公共类

    ├── tool            # 工具包

    └── web_module      # 服务器核心部分

        ├── admin_api   # 配置文件热重载api

        ├── balance     # 负载均衡算法

        ├── cache       # 静态缓存实现

        ├── http_module # http服务器模块

        ├── middleware  # 插件模块

        ├── rewrite     # 路径重写模块

        ├── tcp_module  # tcp服务器模块

        └── util        # 工具包

  

```

### 接口说明
`IServer`
```cj
public interface IServer {
    /**
     * 构建服务器
     * @param addr 监听地址
     * @param port 服务器端口
     */
    func buildServer(addr: String, port: UInt16): Unit

    /**
     * 更新为https服务器
     * @param crtName 证书文件名
     * @param keyName 私钥文件名
     */
    func updateToHttpsServer(crtName: String, keyName: String): Unit

    /**
     * 启动服务器
     */
    func run(): Unit
}
```
## 使用说明
### 引入依赖
在 `cjpm.toml` 中的 `[dependencies]` 内添加如下内容：
```
jwt4cj = {git = "https://gitcode.com/Cangjie-TPC/jwt4cj.git"}
```

添加后 cjpm.toml 如下所示：
```
[dependencies]
  jwt4cj = {git = "https://gitcode.com/Cangjie-TPC/jwt4cj.git"}
  
[package]
  cjc-version = "0.58.3"
  compile-option = ""
  description = "nothing here"
  link-option = ""
  name = "o_proxy_server"
  output-type = "executable"
  override-compile-option = ""
  src-dir = ""
  target-dir = ""
  version = "1.0.0"
  package-configuration = {}
```


### 编译构建
```shell
cpm update
cpm build
```

### 功能示例
#### 修改配置文件示例
功能示例描述:
直接打开 `./src/config.json` 文件进行配置修改

注意需要严格按照示例来改写配置文件

#### 动态热重载配置
> 注意, 使用该功能的api时, 必须在请求头 `header` 中附上 `oproxykey` 字段以及密钥(密钥在配置文件中设置)


功能示例描述:

查看name为ping的upstream配置

调用示例如下:
```
curl --location --request GET 'http://localhost:8080/oproxy/admin/upstream/ping' \
--header 'oproxykey: ocean-proxy-server'
```

响应结果:
```json
{
    "name":"ping",
    "counter":0,
    "group":[
        {
            "host":"localhost:8000",
            "weight":1,
            "currentWeight":1
        },
        {
            "host":"localhost:8001",
            "weight":1,
            "currentWeight":1
        }
    ]}
```


功能示例描述:

使用api新增upstream

调用示例如下:

```shell
curl --location --request POST 'http://localhost:8080/oproxy/admin/upstream' \
--header 'oproxykey: ocean-proxy-server' \
--header 'Content-Type: application/json' \
--data-raw '{
  "name": "add-test",
  "counter": 0,
  "group": [
    {
      "host": "localhost:test",
      "weight": 1
    },
    {
      "host": "localhost:tttt",
      "weight": 1
    }
  ]
}'
```

响应结果:
```text
[admin_api] create upstream success
```

#### 开启https模式
- 首先将申请证书后, 得到的 .crt 和 .key 文件放在 ./src 目录下
- 修改配置文件
    - https: 更改为`true`
    - httpsOptions: `key` 和 `cert` 字段分别设置成我们 .crt 和 .key 的完整文件名
- 重启服务器, 你将启动监听 https://0.0.0.0:443 的服务器

#### 身份验证功能
在location配置时， 可以开启jwt身份验证模式
```
{
    "path": "/ping",
    "upstream": "ping",
    "jwt": true,
    "balance": "round-robin"
},
```
开启后, 当客户端访问除修改配置文件的api外, 均需要在请求头的`Authorization`中,附带上身份验证码, 格式为`Bearer jwt`
```
curl --location --request GET 'http://localhost:8080/ping/hello' \
--header 'Authorization: Bearer ewogICJhbGciOiAiSFMyNTYiLAogICJ0eXAiOiAiSldUIgp9.ewogICJleHAiOiAxNzQxNjkyMDIyLAogICJpYXQiOiAxNzQxNjA1NjIyCn0.9zIZsFFzbLD2zsSl9qZ60pfU-y9HnuvOH6eoT0Lm4kc'
```
有两种方式获取身份验证
- 使用内置api: /oproxy/admin/jwt/sign
  - 注意，需要配置请求头请求头中的 `oproxykey`密钥字段
  - 该方式生成的jwt默认过期时间为24h
- 从配置文件中获取jwt密钥，自行生成jwt身份码

#### 请求转发功能
请求转发主要依赖配置文件中的 `upstream` 和 `loacation`

首先配置upstream
```
{
    "name": "ping",
    "group": [
        {
            "host": "localhost:8000",
            "weight": 1
        },
        {
            "host": "localhost:8001",
            "weight": 1
        }
    ]
},
```
- name是该upstream的标识符, 后面location也通过name使用upstream
- group.host: 转发的目标地址
- group.weight: 权重, 用于负载均衡模块

配置location
```json
{
    "path": "/ping",
    "upstream": "ping",
    "jwt": true,
    "balance": "round-robin"
},
```
  - path: 转发触发的前缀
  - upstream: 调用的upstream的name
  - jwt: 是否开启身份验证
  - balance: 负载均衡的方式选择

说明:
- 当客户端访问 `/ping/hello` 时, 服务器会将请求转发到`name`为`ping`的`upstream`对应的主机
- 服务器根据`balance`字段选择的负载均衡算法, 从`upstream`中挑选出一个主机进行请求转发

例如:
- 根据我们的负载均衡算法`round-robin(轮询)`
- 我们访问`http://localhost:8080/ping/hello`后
  - 第一次转发到`http://localhost:8000/hello`
  - 第二次转发到`http://localhost:8001/hello`
  - 第三次转发到`http://localhost:8000/hello`
  - 第四次转发到`http://localhost:8001/hello`

#### 负载均衡算法
提供了几种基本的负载均衡算法，用户可以在location配置时选择。
- round-robin(轮询算法): 平均分配调用机会
- random(随机算法): 随机调用目标服务
- smooth-weighted-round-robin(平滑加权轮询算法): 加权轮询算法的改良版, 可以保证根据服务器的权重分配请求，同时确保请求分布更加均匀，避免同一服务器在短时间内被连续选中。
- ip-hash(源地址哈希算法): 根据客户端的IP地址, 通过哈希算法, 选出一个固定的节点,使得同一个IP地址的请求, 始终访问同一个节点. 适用于购物车等需要保持会话的场景

#### 自定义插件
在该文件夹下创建插件函数: `./src/web_module/middleware`
```cj
package o_proxy_server.web_module.middleware

import o_proxy_server.model.web.Context

protected func newHandler(ctx: Context): Unit {
}

```
在a.cj文件中应用插件: `./src/web_module/middleware/a.cj`使用`handlers.add(newHandler)`
- 注意, 代码行数顺序即为中间件的调用顺序
- 本项目自定义的Context类, 可以前往o_proxy_server.model.web.Context查看详情

```cj
public func getMiddlewareHandler(): ArrayList<(Context) -> Unit> {
    let handlers = ArrayList<(Context) -> Unit>()

    // admin api 插件
    handlers.add(adminApiApiHandler)

    // jwt 插件
    handlers.add(jwtHandler)

    // 路由重写插件
    handlers.add(rewriteHandler)

    // !!!注意, memoryCacheHandler 和 gatewayHandler 的顺序不能颠倒
    handlers.add(memoryCacheHandler)
    handlers.add(gatewayHandler)

    // 这个必须是最后一个
    // 退出函数
    handlers.add(exitHandler)

    return handlers
}
```

#### 路由重写
配置文件模板如下
- 该项目暂时只实现了 regex 和 replacement 部分
- rewrite模块在请求转发模块之前, 因此反向代理服务器会先进行重写, 再根据重写的路径进行请求转发

```json
{
  "conditions": [
    {
      "variable": "$http_user_agent",
      "pattern": "Mobile",
      "operators": "~*"
    }
  ],
  "regex": "^/user/(\\d+)",
  "replacement": "/profile?id=$1",
  "flags": [
    "last"
  ]
},
```

例如：
- 客户端访问路径: /user/12
- 重写路径: /profile?id=12

#### 静态缓存

- 项目采用的是内存缓存的方法, 默认存储个数为20个
- 缓存替换方法默认采用LRU
- 暂时只保存 `GET`  + `HTML` 的相应文件


#### 日志系统

日志系统用来记录服务器的每项操作.

需要在配置文件中进行配置

```json
"logInfo": {
	"strategy": "Always",
	"fileName": "log1.txt",
	"cacheLength": 100
}
```

- strategy: 日志写入策略, 仅支持以下三种策略. 非下列策略时, 默认使用 Always
	- Always: 每一条日志都写入文件
	- EveryNLog: 将日志写入缓存, 当缓存满时, 写入文件
	- No: 将所有日志写入缓存中, 只有当用户主动 flush 时, 才将缓存写入文件
- fileName: 日志文件的 name
- cacheLength: 日志缓存的大小

日志示例如下
```
[2025-04-10 15:07:27] [DEFAULT] HTTP server is running on http://0.0.0.0:8080
[2025-04-10 15:07:31] [INFO] [jwt] GET /ping/hello => 401, token is invalid
[2025-04-10 15:07:46] [INFO] [admin api] GET /oproxy/admin/default/config/json
[2025-04-10 15:10:37] [DEFAULT] HTTP server is running on http://0.0.0.0:8080
[2025-04-10 15:10:39] [INFO] [admin api] GET /oproxy/admin/default/config/json
```


  

## 约束与限制
- 本项目依赖于仓颉`0.58.3`版本
- 配置文件部分需要严格要求示例编写，不能随意删除字段

## 参与贡献
本项目基于 MIT License，欢迎给我们提交PR，欢迎参与任何形式的贡献。

本项目committer：[@oceanSimple](https://gitcode.com/oceanSimple)

This project is supervised by [@zhangyin_gitcode](https://gitcode.com/zhangyin_gitcode) (HUAWEI Developer Advocate).

![](https://raw.gitcode.com/SIGCANGJIE/homepage/attachment/uploads/9b648c07-efc2-4eb3-b02f-eab18c77beea/devadvocate.png)