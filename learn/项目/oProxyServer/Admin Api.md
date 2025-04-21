# ♾️描述
> 说明, 本api文档的写作风格借鉴于`apisix`
> https://apisix.apache.org/zh/docs/apisix/admin-api/#description

Admin API 是一组用于配置 o_proxy_server 路由、上游、路径重写、SSL 证书等功能的 RESTful API。

你可以通过 Admin API 来获取、创建、更新以及删除资源。同时得益于 o_proxy_server 的热加载能力，资源配置完成后 o_proxy_server 将会自动更新配置，无需重启服务。

当 o_proxy_server 启动时，Admin API 默认情况下将会占用前缀为 `/oproxy/admin` 的 API。

当你访问 admin api 时, 需要在请求头中附带 `oproxykey` 字段, 并填写密钥.

# ♾️default
默认配置, 即调用一些 admin api 的默认api

## 💫请求地址
请求地址：/oproxy/admin/default

| 请求方法 |                   请求URI                    | 请求body |          描述           |
| :--: | :----------------------------------------: | :----: | :-------------------: |
| GET  |         /oproxy/admin/default/ping         |   无    |      测试admin api      |
| GET  |     /oproxy/admin/default/config/json      |   无    | 获取json格式的config配置文件信息 |
| GET  | /oproxy/admin/default/restoreDefaultConfig |   无    |    将配置文件恢复为默认(慎用)     |

## 💫body请求参数

|    名称     | 必选项 |  类型  |        描述        |        示例值         |
| :-------: | :-: | :--: | :--------------: | :----------------: |
| oproxykey | 必选  | 身份验证 | 用来验证调用者的身份是否是管理者 | ocean-proxy-server |

## 💫使用示例
### 测试 admin api
> 请求示例
```shell
curl --location --request GET '/oproxy/admin/default/ping' \ 
--header 'oproxykey: ocean-proxy-server'
```

> 响应示例

格式: text
内容
```
pong
```

### 获取配置文件
> 请求示例

```shell
curl --location --request GET '/oproxy/admin/default/config/json' \ 
--header 'oproxykey: ocean-proxy-server'
```

> 响应示例

格式: json
内容
```json
{
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
            "balance": "round-robin"
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
    ]
}
```

### 恢复默认配置
> 请求示例

```shell
curl --location --request GET '/oproxy/admin/default/restoreDefaultConfig' \
--header 'oproxykey: ocean-proxy-server'
```

> 响应示例

格式: text
内容
```
[admin-api] restore default config success
```

# ♾️https
https配置, 即使用https的相关配置信息, 包括证书路径等

## 💫请求地址

请求地址：/oproxy/admin/https

| 请求方法 |           请求URI            | 请求body |      描述       |
| :--: | :------------------------: | :----: | :-----------: |
| GET  | /oproxy/admin/https/:flag  |   无    |  是否开启https功能  |
| POST | /oproxy/admin/httpsOptions | {...}  | 修改https相关文件路径 |

## 💫body请求参数

|    名称     | 必选项 |  类型  |        描述        |        示例值         |
| :-------: | :-: | :--: | :--------------: | :----------------: |
| oproxykey | 必选  | 身份验证 | 用来验证调用者的身份是否是管理者 | ocean-proxy-server |

## 💫使用示例
### 是否开启https
> 请求示例

```shell
curl --location --request GET '/oproxy/admin/https/true' \
--header 'oproxykey: ocean-proxy-server'
```

> 响应示例

格式: text
内容
```
[admin_api] update https state to TRUE success
```

### 证书文件路径
> 请求示例

```shell
curl --location --request POST '/oproxy/admin/httpsOptions' \
--header 'oproxykey: ocean-proxy-server' \
--header 'Content-Type: application/json' \
--data-raw '{
    "https": true,
    "key": "test-https-key-file-name",
    "cert": "test-https-cert-file-name"
}'
```

> 响应示例

格式: text
内容
```
[admin-api] update https options success
```


# ♾️location
路由配置, 可以通过定义一些规则来匹配客户端的请求，然后根据匹配结果加载并执行相应的插件，并把请求转发给到指定 Upstream（上游）。

## 💫请求地址

请求地址: /oproxy/admin/location

|  请求方法  |            请求URI             | 请求body |      描述      |
| :----: | :--------------------------: | :----: | :----------: |
|  GET   | /oproxy/admin/location/:path |   无    | 根据path获取路由信息 |
|  POST  |    /oproxy/admin/location    |   无    |    创建新的路由    |
| DELETE | /oproxy/admin/location/:path |   无    |  根据path删除路由  |
## 💫body请求参数

|    名称     | 必选项 |  类型  |        描述        |        示例值         |
| :-------: | :-: | :--: | :--------------: | :----------------: |
| oproxykey | 必选  | 身份验证 | 用来验证调用者的身份是否是管理者 | ocean-proxy-server |

## 💫使用示例
### 查看路由
> 请求示例

```shell
curl --location --request GET '/oproxy/admin/location/ping' \
--header 'oproxykey: ocean-proxy-server'
```

> 响应示例

格式: json
内容
```json
{
    "path": "/ping",
    "upstream": "ping",
    "headerHost": "default-header-host",
    "balance": "round-robin",
    "jwt": true
}
```

### 创建路由

> 请求示例

```shell
curl --location --request POST '/oproxy/admin/location' \
--header 'oproxykey: ocean-proxy-server' \
--header 'Content-Type: application/json' \
--data-raw '{
    "path": "/addTest",
    "upstream": "ping",
    "balance": "round-robin",
    "jwt": true
}'
```

> 响应示例

格式: text
内容
```
[admin-api] create location success
```

### 删除路由

> 请求示例

```shell
curl --location --request DELETE '/oproxy/admin/location/addTest' \
--header 'oproxykey: ocean-proxy-server'
```

> 响应示例

格式: text
内容
```
[admin-api] delete location success
```

# ♾️upstream
Upstream 是虚拟主机抽象，对给定的多个服务节点按照配置规则进行负载均衡。Upstream 的地址信息可以直接配置到 `location` 上.

## 💫请求地址

请求地址：/oproxy/admin/upstream

|  请求方法  |            请求URI             | 请求body |     描述     |
| :----: | :--------------------------: | :----: | :--------: |
|  GET   | /oproxy/admin/upstream/:name |   无    | 获取upstream |
|  POST  |    /oproxy/admin/upstream    | {...}  | 创建upstream |
| DELETE | /oproxy/admin/upstream/:name |   无    | 删除upstream |

## 💫body请求参数

|    名称     | 必选项 |  类型  |        描述        |        示例值         |
| :-------: | :-: | :--: | :--------------: | :----------------: |
| oproxykey | 必选  | 身份验证 | 用来验证调用者的身份是否是管理者 | ocean-proxy-server |


## 💫使用示例
### 查看upstream
> 请求示例

```shell
curl --location --request GET '/oproxy/admin/upstream/ping' \
--header 'oproxykey: ocean-proxy-server'
```

> 响应示例

格式: JSON
内容
```json
{
    "name": "ping",
    "counter": 0,
    "group": [
        {
            "host": "localhost:8000",
            "weight": 1,
            "currentWeight": 1
        },
        {
            "host": "localhost:8001",
            "weight": 1,
            "currentWeight": 1
        }
    ]
}
```

### 创建upstream
> 使用示例

```shell
curl --location --request POST '/oproxy/admin/upstream' \
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

> 响应示例

格式: text
内容
```
[admin_api] create upstream success
```

### 删除upstream
> 请求示例

```shell
curl --location --request DELETE '/oproxy/admin/upstream/add-test \
--header 'oproxykey: ocean-proxy-server'
```

> 响应示例

格式: text
内容
```
[admin_api] delete upstream success
```


# ♾️rewrite
路由重写相关配置

## 💫请求地址

请求地址：/oproxy/admin/rewrite

|  请求方法  |            请求URI             | 请求body |         描述         |
| :----: | :--------------------------: | :----: | :----------------: |
|  GET   | /oproxy/admin/rewrite/global |   无    | 获取rewrite global信息 |
|  GET   | /oproxy/admin/rewrite/regex  |   无    |    获取rewrite信息     |
|  POST  | /oproxy/admin/rewrite/create | {...}  |     创建rewrite      |
| DELETE |    /oproxy/admin/rewrite     |   无    |     删除rewrite      |

## 💫body请求参数

|    名称     | 必选项 |     类型     |        描述        |        示例值         |
| :-------: | :-: | :--------: | :--------------: | :----------------: |
| oproxykey | 必选  |    身份验证    | 用来验证调用者的身份是否是管理者 | ocean-proxy-server |
|   path    | 可选  | regex的name |   用来传输regex信息    |    /mobile/(.*)    |


## 💫使用示例
### rewrite global
> 请求示例

```shell
curl --location --request GET '/oproxy/admin/rewrite/global' \
--header 'oproxykey: ocean-proxy-server'
```

> 响应示例

格式: JSON
内容
```json
{
    "maxRewrites": 10,
    "debug": false,
    "cacheEnabled": true
}
```


### 获取rewrite信息

注意, 将path信息放在请求头中

> 请求示例

```shell
curl --location --request GET '/oproxy/admin/rewrite/regex' \
--header 'oproxykey: ocean-proxy-server' \
--header 'path: /mobile/(.*)'
```

> 响应示例

格式: JSON
内容
```json
{
    "conditions": [
        {
            "variable": "$http_user_agent",
            "pattern": "Mobile",
            "operators": "~*"
        }
    ],
    "regex": "/mobile/(.*)",
    "replacement": "/m/$1",
    "flags": [
        "break"
    ]
}
```


### 创建rewrite
> 请求示例
```shell
curl --location --request POST '/oproxy/admin/rewrite/create' \
--header 'oproxykey: ocean-proxy-server' \
--header 'Content-Type: application/json' \
--data-raw '{
  "conditions": [
    {
      "variable": "$http_user_agent",
      "pattern": "Mobile",
      "operators": "~*"
    }
  ],
  "regex": "/testAdd/(.*)",
  "replacement": "/ping/ping",
  "flags": [
    "break"
  ]
}'
```

> 响应示例

格式: text
内容
```
[admin-api] create rewrite success
```

### 删除rewrite
>请求示例

```shell
curl --location --request DELETE '/oproxy/admin/rewrite' \
--header 'oproxykey: ocean-proxy-server' \
--header 'path: /testAdd/(.*)'
```

> 响应示例

格式: text
内容
```
[admin-api] delete rewrite success
```


# ♾️jwt

## 💫请求地址

请求地址：/oproxy/admin/jwt

| 请求方法 |          请求URI           | 请求body |     描述      |
| :--: | :----------------------: | :----: | :---------: |
| GET  |  /oproxy/admin/jwt/sign  |   无    | 获得jwt token |
| GET  | /oproxy/admin/jwt/verify |   无    | 验证jwt token |

## 💫body请求参数

|      名称       | 必选项 |  类型  |        描述        |        示例值         |
| :-----------: | :-: | :--: | :--------------: | :----------------: |
|   oproxykey   | 必选  | 身份验证 | 用来验证调用者的身份是否是管理者 | ocean-proxy-server |
| Authorization | 可选  | 身份验证 |   传输jwt token    |        ...         |

## 💫请求示例
### 签发
> 请求示例

```shell
curl --location --request GET '/oproxy/admin/jwt/sign' \
--header 'oproxykey: ocean-proxy-server'
```

> 响应示例

格式: text
内容
```
ewogICJhbGciOiAiSFMyNTYiLAogICJ0eXAiOiAiSldUIgp9.ewogICJleHAiOiAxNzQ0MDkyOTE3LAogICJpYXQiOiAxNzQ0MDA2NTE3Cn0.iGuUBVTgWk2JeOgDH8zHg_Ni97BeQA7mEVF5mpFV2Vs
```

### 校验
> 请求示例

```shell
curl --location --request GET '/oproxy/admin/jwt/verify' \
--header 'Authorization: Bearer ewogICJhbGciOiAiSFMyNTYiLAogICJ0eXAiOiAiSldUIgp9.ewogICJleHAiOiAxNzQxNTc2NTYwLAogICJpYXQiOiAxNzQxNDkwMTYwCn0.P6P26n52W4KeJMk80qG2aUWNwVKFFPJfXYZpet72hvg' \
--header 'oproxykey: ocean-proxy-server'
```

> 响应示例

格式: text
内容
```
jwt verify success
```


# ♾️log
## 💫请求地址

请求地址：/oproxy/admin/log

| 请求方法 |          请求URI           | 请求body |      描述       |
| :--: | :----------------------: | :----: | :-----------: |
| POST | /oproxy/admin/log/submit | {...}  |     提交日志      |
| GET  | /oproxy/admin/log/flush  |   无    | 将缓存中的日志保存到文件中 |

## 💫body请求参数

|      名称       | 必选项 |  类型  |        描述        |        示例值         |
| :-----------: | :-: | :--: | :--------------: | :----------------: |
|   oproxykey   | 必选  | 身份验证 | 用来验证调用者的身份是否是管理者 | ocean-proxy-server |

## 💫请求示例
### 提交log
> 请求示例

```shell
curl --location --request POST '/oproxy/admin/log/submit' \
--header 'oproxykey: ocean-proxy-server' \
--header 'Content-Type: application/json' \
--data-raw '{
    "prefix": "debug",
    "log": "admin api submit test"
}'
```

> 响应示例

格式: text
内容
```
[admin-api] submit log info
```

### flush
> 请求示例

```shell
curl --location --request GET '/oproxy/admin/log/flush' \
--header 'oproxykey: ocean-proxy-server'
```

> 响应示例

格式: text
内容
```
[admin api] flush log cache success
```





