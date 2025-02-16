# ♾️入口-OGin
![[oginTree.png]]

> 参数成员
> - server: http.Server
> - addr: ip地址
> - port: 端口号
> - route: //


> 函数成员
> - run: 启动服务器
> - get/post/put/delete: 注册监听器
> - group: 获取分组器

# ♾️前缀树组件
# 💫路由树示例
![[routerTreeExample.png]]

## 💫路由树组件-Trie

![[trieTree.png]]

> 参数成员
> - pattern: 保存完整的路由. 结尾的part才保存完整路由
> - part: 部分路由
> - children: 子路由
> - isWild: 是否是模糊匹配, 例如 * 或 :

> 函数成员
> - matchChild: 根据路由寻找child. insert函数的工具
> - insert: 将新路由插入到路由树
> - matchChildren: 匹配路由children. search函数的工具
> - search: 搜索匹配路由函数

## 💫路由组件-Router
![[routerTree.png]]

> 参数成员
> - roots: 路由树. get/post/put/delete各种method都有一颗单独的树
> - handlers: 保存各个请求路径的处理器


> 函数成员
> - handle: 处理函数
> - parsePattern: 处理请求路径, 按照"/"分隔
> - addRoute: 添加路由到路由树
> - getRoute: 从路由树中获取路由, 同时将路由参数解析出来


# ♾️上下文组件-Context
![[contextTree.png]]

> 参数成员
> - request: 获取http.Request
> - response: 获取http.Response
> - path: 请求路径
> - method: 请求方法
> - params: 存储路径参数的map
> - statusCode: 响应参数
> - body: 请求中的body数据(String). 注意由于框架已经将body读取了一遍, 因此若手动读取resquest.body会无法获取
> - handlers: // TODO 用于中间件, 待定
> - index: // TODO 中间件模块, 待定

> 函数成员
> - param: 获取路径参数
> - query: 获取query参数
> - getParamMap: 获取paramMap, 暂时没啥用
> - json: 响应体使用json
> - html: 响应体使用html
> - next: // TODO 中间件模块, 待定

# ♾️路由处理器-OGinDistributor
![[oginDistributor.png]]

> 自定义的处理器, 通过前缀路由树来处理


# ♾️分组组件-routerGroup
## 💫routerGroup
![[routerGroup.png]]

> 参数成员
> - prefix: 当前group的前缀
> - handlers: // TODO 用于中间件
> - parent: 父前缀


> 函数成员
> - group: 创建子group
> - addRoute: 根据当前前缀加入路由
> - get/post/put/delete: 创建路由




