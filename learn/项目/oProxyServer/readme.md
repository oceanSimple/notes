# ♾️项目结构

> 说明:
> - 如果你要使用http, 那么端口号默认是8080, 如果开启了https, 则使用的是443端口号. 默认配置为不开启https
- oProxyServer: 反向代理服务器项目
- server_8000: 测试服务器, 端口为8000
- server_8001: 测试服务器, 端口为8001

# ♾️项目说明

本项目采用Cangjie语言开发, 实现了基础的反向代理服务器功能.
- 配置文件: 提供直接修改文件以及api动态更新配置文件两种方式
- 开启https功能: 用户可以将申请的https证书放在项目文件中, 开启https
- 身份验证模块: 客户端在访问除配置文件api外, 需要在header中加上身份验证才准许访问
- 请求转发: 及网关功能, 将接受的请求根据配置文件进行转发
- 负载均衡: 提供几种默认的负载均衡模式, 可在location中选择
- 静态缓存: 保存 `GET` 请求返回的 `HTML` 静态缓存
- 自定义插件: 开发者可以便捷的开发其他插件, 并投入使用
- 路由重写: 通过使用正则表达式配置rewrite, 达到路由重写的效果





## 💫配置文件
- 直接修改文件: 直接前往 `./oProxyServer/src/config.json` 文件进行修改. 
	- 注意, 当前版本需要严格遵循配置文件编写要求, 不能随便删减字段
	- 修改完配置文件后, 需要重启服务才能生效
- 调用内置api动态修改
	- 调用api时, 请求头要包含 `oproxykey` 字段, 用来判断身份
	- 使用api修改配置, 无需重启服务即可生效

## 💫https

- 首先将申请证书后, 得到的 `.crt` 和 `.key` 文件放在 `./oProxyServer/src` 目录下
- 修改配置文件
	- https: 更改为true
	- httpsOptions: key 和 cert 字段分别设置成我们 `.crt` 和 `.key` 的完整文件名
- 当然你也可以通过配置文件api动态修改配置文件
- 最后重启服务器, 你将启动监听 `https://0.0.0.0:443` 的服务器

## 💫身份验证

> 身份验证模块采用jwt

- 在配置location时, 可自由选择是否开启jwt功能(true 或者 false)
- jwt身份码
	- 既可以通过配置文件api获得: /oproxy/admin/jwt/sign (记得加上`oproxykey`)
	- 也可以通过其他服务, 根据配置文件中的 `jwt-secret` 私钥进行生成
- 在调用服务时, 请求头配置 `Authorization` , 值为 `Bearer ` + jwt身份码(注意中间有个空格)


## 💫请求转发

> 请求转发主要依赖配置文件中的 `location` 和 `upstream`

- 首先配置upstream
```json
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

- 配置location
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

## 💫静态缓存

> - 项目采用的是内存缓存的方法, 默认存储个数为20个
> - 缓存替换方法默认采用LRU
> - 暂时只保存 `GET`  + `HTML` 的相应文件





## 💫负载均衡

提供了几种基本的负载均衡算法
- round-robin(轮询算法): 平均分配调用机会
- random(随机算法): 随机调用目标服务
- smooth-weighted-round-robin(平滑加权轮询算法): 加权轮询算法的改良版, 可以保证根据服务器的权重分配请求，同时确保请求分布更加均匀，避免同一服务器在短时间内被连续选中。
- ip-hash(源地址哈希算法): 根据客户端的IP地址, 通过哈希算法, 选出一个固定的节点,使得同一个IP地址的请求, 始终访问同一个节点. 适用于购物车等需要保持会话的场景

## 💫自定义插件

> 在该文件夹下创建插件函数: `./oProxyServer/src/web_module/middleware`

```java
package o_proxy_server.web_module.middleware

import o_proxy_server.model.web.Context

protected func newHandler(ctx: Context): Unit {
}

```

> 在a.cj文件中应用插件: `./oProxyServer/src/web_module/middleware/a.cj`
> 使用handlers.add(newHandler)
> 注意, 代码行数顺序即为中间件的调用顺序, 而本项目自定义的Context类, 可以前往o_proxy_server.model.web.Context查看详情

```java
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

## 💫路由重写rewrite

> 下面是rewrite配置文件模板
> 该项目暂时只实现了 regex 和 replacement 部分
> 注意, rewrite模块在请求转发模块之前, 因此反向代理服务器会先进行重写, 再根据重写的路径进行请求转发

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

- regex: 即正则表达式部分, 推荐以^开头
- replacement: 重写目标路径. 支持$表达式(即正则表达式替换的部分会按照起始为1的序号进行填充)


> 比如
> 客户端访问路径: /user/12
> 重写路径: /profile?id=12



# ♾️配置文件api详情

> - 调用api时, 请求头要包含 `oproxykey` 字段, 用来判断身份!!!
> - 如果需要详细的示例, 请访问: https://apifox.com/apidoc/shared-d54dc3e2-5298-481a-8e91-6859d4f9789d


# ♾️设计思路

> 设计思路文档链接: https://www.yuque.com/ocean-0z5us/azabem/hqnewrb8ibb1zs3h?singleDoc# 《设计思路》

