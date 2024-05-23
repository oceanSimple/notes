# ♾️查看
- 查看路由
```shell
curl http://127.0.0.1:9180/apisix/admin/routes -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1'
```
- 查看upstream
```shell
curl "http://127.0.0.1:9180/apisix/admin/upstreams" \
-H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X GET
```


# ♾️创建
- 创建上游
```shell
curl http://127.0.0.1:9180/apisix/admin/upstreams/2  \
-H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -i -X PUT -d '
{
    "type":"roundrobin",
    "nodes":{
        "111.229.78.126:8002": 1
    }
}'
```

- 创建路由
```shell
curl http://127.0.0.1:9180/apisix/admin/routes/1 \
-H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT -i -d '
{
    "uri": "/account/*",
    "upstream_id": 1
}'
```