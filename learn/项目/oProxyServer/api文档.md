# 初始

## 说明

在配置文件`config.json`中, 配置`adminKey`, 该key即为使用系统api的密钥, 每次调用api时都需要附带

## apifox的测试文档

[api在线文档](https://apifox.com/apidoc/shared-d54dc3e2-5298-481a-8e91-6859d4f9789d/269586875e0)

# default

## GET 测试api

|   |   |
|---|---|
|路径|/oproxy/admin/default/ping|
|请求头|oproxykey: ocean-proxy-server|

响应示例

pong

## GET 获取配置文件(json格式)

|     |                                   |
| --- | --------------------------------- |
| 路径  | /oproxy/admin/default/config/json |
| 请求头 | oproxykey: ocean-proxy-server     |

响应示例

```
{
  "adminKey": "ocean-proxy-server",
  "port": 8080,
  "apiKey": "api-key",
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
    },
    {
      "name": "pong",
      "group": [
        {
          "host": "localhost:8002",
          "weight": 1
        },
        {
          "host": "localhost:8003",
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
      "balance": "polling"
    },
    {
      "path": "/pong",
      "upstream": "pong",
      "jwt": false,
      "balance": "polling"
    }
  ]
}
```

## GET 恢复默认设置
将配置文件恢复成默认配置, 慎用!!!

|     |                                            |
| --- | ------------------------------------------ |
| 路径  | /oproxy/admin/default/restoreDefaultConfig |
| 请求头 | oproxykey: ocean-proxy-server              |


# upstream

## GET 通过name获取upstream

|   |   |
|---|---|
|路径|/oproxy/admin/upstream/:name|
|请求头|oproxykey: ocean-proxy-server|

响应示例

```
{
  "name": "ping",
  "counter": 0,
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
```

## POST 新建upstream

|   |   |
|---|---|
|路径|/oproxy/admin/upstream|
|请求头|oproxykey: ocean-proxy-server|

body示例

```
{
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
}
```

## DELETE 删除upstream

|   |   |
|---|---|
|路径|/oproxy/admin/upstream/:path|
|请求头|oproxykey: ocean-proxy-server|

# location

## GET 通过路径获取location

|   |   |
|---|---|
|路径|/oproxy/admin/location/:path|
|请求头|oproxykey: ocean-proxy-server|

响应示例

```
{
  "path": "/ping",
  "upstream": "ping",
  "headerHost": "default-header-host",
  "balance": "polling",
  "jwt": true
}
```

## POST 新建location

|   |   |
|---|---|
|路径|/oproxy/admin/location|
|请求头|oproxykey: ocean-proxy-server|

body示例

```
{
    "path": "/addTest",
    "upstream": "ping",
    "balance": "polling",
    "jwt": true
}
```

## DELETE 删除location

|   |   |
|---|---|
|路径|/oproxy/admin/location/:path|
|请求头|oproxykey: ocean-proxy-server|

# rewrite

## GET 获取rewrite-global信息

|   |   |
|---|---|
|路径|/oproxy/admin/rewrite/global|
|请求头|oproxykey: ocean-proxy-server|

响应示例

```
{
    "maxRewrites": 10,
    "debug": false,
    "cacheEnabled": true
}
```

## GET 根据regex信息获取rewrite

特殊说明, 该api的参数同路径参数. 之所以api用*, 是因为参数里面也带有/

示例: /oproxy/admin/rewrite/regex/^/mobile/(.*)

|   |   |
|---|---|
|路径|/oproxy/admin/rewrite/regex/*|
|请求头|oproxykey: ocean-proxy-server|

响应示例

```
{
    "conditions": [
        {
            "variable": "$http_user_agent",
            "pattern": "Mobile",
            "operators": "~*"
        }
    ],
    "regex": "^/mobile/(.*)",
    "replacement": "/m/$1",
    "flags": [
        "break"
    ]
}
```

## POST 创建rewrite

|   |   |
|---|---|
|路径|/oproxy/admin/rewrite/create|
|请求头|oproxykey: ocean-proxy-server|

请求体示例

```
{
    "conditions": [
        {
            "variable": "$http_user_agent",
            "pattern": "Mobile",
            "operators": "~*"
        }
    ],
    "regex": "^/testAdd/(.*)",
    "replacement": "/ping/ping",
    "flags": [
        "break"
    ]
}
```

## DELETE 删除rewrite

同GET

|   |   |
|---|---|
|路径|/oproxy/admin/rewrite/regex/*|
|请求头|oproxykey: ocean-proxy-server|

# https
## GET 是否开启https
> flag只有两种值: true || false

|     |                               |
| --- | ----------------------------- |
| 路径  | /oproxy/admin/https/:flag     |
| 请求头 | oproxykey: ocean-proxy-server |

## POST 更改httpsOptions

|     |                               |
| --- | ----------------------------- |
| 路径  | /oproxy/admin/httpsOptions    |
| 请求头 | oproxykey: ocean-proxy-server |
body示例
```
{
  "https": true,
  "key": "test-https-key-file-name",
  "cert": "test-https-cert-file-name"
}
```

# jwt
## GET 签发jwt

|     |                               |
| --- | ----------------------------- |
| 路径  | /oproxy/admin/jwt/sign        |
| 请求头 | oproxykey: ocean-proxy-server |

## GET 校验JWT

|     |                               |
| --- | ----------------------------- |
| 路径  | /oproxy/admin/jwt/verify      |
| 请求头 | oproxykey: ocean-proxy-server |
|     | Authorization: Bearer jwtcode |
