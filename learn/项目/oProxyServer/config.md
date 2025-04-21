# ♾️配置文件示例
```json
{
    "address": "0.0.0.0",
    "port": 8080,
    "apiKey": "api-key",
    "adminKey": "ocean-proxy-server",
    "https": false,
    "httpsOptions": {
        "key": "oceansimple.online.key",
        "cert": "oceansimple.online_bundle.crt"
    },
    "jwtSecret": "jwt-secret",
    "upstream": [
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
        }
    ],
    "location": [
        {
            "path": "/ping",
            "upstream": "ping",
            "jwt": true,
            "balance": "round-robin",
            "cors": {
                "allowOrigin": "*",
                "allowMethods": "GET, POST, PUT, DELETE, OPTIONS",
                "allowHeaders": "Content-Type, Authorization",
                "exposeHeaders": "",
                "maxAge": 3600,
                "allowCredentials": false
            }
        }
    ],
    "rewriteGlobal": {
        "maxRewrites": 10,
        "debug": false,
        "cacheEnabled": true
    },
    "rewrite": [
        {
            "regex": "/mobile/(.*)",
            "replacement": "/m/$1",
            "flags": [
                "break"
            ],
            "conditions": [
                {
                    "variable": "$http_user_agent",
                    "pattern": "Mobile",
                    "operators": "~*"
                }
            ]
        }
    ],
    "logInfo": {
        "strategy": "Always",
        "fileName": "log1.txt",
        "cacheLength": 100
    }
}
```

# ♾️基本配置

```json
{
    "address": "0.0.0.0",
    "port": 8080,
    "apiKey": "api-key",
    "adminKey": "ocean-proxy-server",
    "jwtSecret": "jwt-secret"
}
```

- address: 监听地址. eg: `0.0.0.0` 表示监听所有ip
- port: 本反向代理服务器监听的端口号
- apiKey: `待定`
- adminKey: 调用 admin api 时, 请求头`oproxykey`需要附带的作为身份证明的密钥
- jwtSecret: 开启jwt鉴权时的secret

# ♾️https
```json
{
    "https": false,
    "httpsOptions": {
        "key": "oceansimple.online.key",
        "cert": "oceansimple.online_bundle.crt"
    }
}
```

- https: 是否开启https
- httpsOptions: https 相关证书文件的路径
	- key: 密钥文件
	- cert: 证书文件

# ♾️upstream

```json
"upstream": [
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
	}
],
```

- name: upstream 的 name, 便于 location 使用
- group: 多个上游服务器
	- host: 主机ip + 端口号
	- weight: 权重值, 用于负载均衡


# ♾️location

```json
    "location": [
        {
            "path": "/ping",
            "upstream": "ping",
            "jwt": true,
            "balance": "round-robin",
            "cors": {
                "allowOrigin": "*",
                "allowMethods": "GET, POST, PUT, DELETE, OPTIONS",
                "allowHeaders": "Content-Type, Authorization",
                "exposeHeaders": "",
                "maxAge": 3600,
                "allowCredentials": false
            }
        }
    ],
```

- path: 转发路由前缀, 服务器根据url的前缀与location的path是否匹配, 确定是否进行转发
- upstream: 使用的 upstream 的 name
- jwt: 是否开启 jwt 鉴权功能
- balance: 负载均衡算法, 暂时仅支持以下算法. 非下列算法时, 默认使用轮询
	- round-robin: 普通轮询算法
	- smooth-weighted-round-robin: 平滑加权轮询算法
	- random: 随机算法
	- ip-hash: ip哈希算法, 保证同一个ip地址访问的是同一个后端服务器
- cors: 跨域配置. 功能暂不完善. `待定`

# ♾️rewriteGlobal
功能暂不完善, `待定`

```json
    "rewriteGlobal": {
        "maxRewrites": 10,
        "debug": false,
        "cacheEnabled": true
    },
```


# ♾️rewrite

```json
"rewrite": [
	{
		"regex": "/mobile/(.*)",
		"replacement": "/m/$1",
		"flags": [
			"break"
		],
		"conditions": [
			{
				"variable": "$http_user_agent",
				"pattern": "Mobile",
				"operators": "~*"
			}
		]
	}
],
```

- regex: 路径重写规则
- replacement: 目标重写路径
	- 注意, 我们通过 `$1` `$2` 语法, 将 regex 中的通配符部分, 添加到 replacement中
	- 通配符部分, 从左到右, 从 1 开始计数(非0)
- flags: `待定`
- conditions: `待定`

# ♾️logInfo

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
