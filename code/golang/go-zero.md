# 0. 准备
[go-zero 官网](https://go-zero.dev/docs/tasks/installation/go-zero)

> 基础安装(见官网)

1. Golang
2. goctl
3. protoc

> 创建项目后,下载依赖包

```bash
go get -u github.com/zeromicro/go-zero@latest
```

> 其他通用包

```bash
go get github.com/pkg/errors
```

# 1. Api项目
[api文件 官方文档](https://go-zero.dev/docs/tasks/dsl/api)

## 1.1 修改`handler.tpl`
修改电脑中的`handler.tpl`文件,将包名改成项目名

## 1.2 基础

根据官网编写一个api文件,例如:
```api
syntax = "v1"  
  
type (  
    // 定义登录接口的请求体  
    LoginReq {  
       Username string `json:"username"`  
       Password string `json:"password"`  
    }  
    // 定义登录接口的响应体  
    LoginResp {  
       Id       int64  `json:"id"`  
       Name     string `json:"name"`  
       Token    string `json:"token"`  
       ExpireAt string `json:"expireAt"`  
    }  
)  
  
// 定义 HTTP 服务  
// 微服务名称为 user，生成的代码目录和配置文件将和 user 值相关  
service user {  
    // 定义 http.HandleFunc 转换的 go 文件名称及方法  
    @handler Login  
    // 定义接口  
    // 请求方法为 post    // 路由为 /user/login    // 请求体为 LoginReq    // 响应体为 LoginResp，响应体必须有 returns 关键字修饰  
    post /user/login (LoginReq) returns (LoginResp)  
}
```

然后在终端输入命令生成项目
```bash
goctl api go -api user_api/user.api -dir user_api/api/
```

整理依赖mod
```bash
go mod tidy
```

进入项目下/api/internal目录,运行user.go


## 1.3 common文件夹
该文件夹保存所有微服务公用包

### 1.3.1 response
1. `httpResult.go`
```go
package response  
  
import (  
    "github.com/pkg/errors"  
    "github.com/zeromicro/go-zero/core/logx"    "github.com/zeromicro/go-zero/rest/httpx"    "go-zero-learn/common/myError"    "net/http")  
  
func HttpResult(r *http.Request, w http.ResponseWriter, resp interface{}, err error) {  
    // 成功返回  
    if err == nil {  
       r := Success(resp)  
       httpx.WriteJson(w, http.StatusOK, r)  
       return  
    }  
  
    // 错误返回  
    errCode := uint32(500)  
    errMsg := "服务器开小差啦，稍后再来试一试"  
  
    var e *myError.CodeError  
    if errors.As(err, &e) {  
       errCode = e.GetErrCode()  
       errMsg = e.GetErrMsg()  
    } else {  
       errMsg = err.Error()  
    }  
    logx.WithContext(r.Context()).Errorf("【API-ERR】 : %+v ", err)  
    httpx.WriteJson(w, http.StatusBadRequest, Error(errCode, errMsg))  
}
```
2. `response.go`
```go
package response  
  
type SuccessBean struct {  
    Code uint32      `json:"code"`  
    Msg  string      `json:"msg"`  
    Data interface{} `json:"data"`  
}  
type NullJson struct{}  
  
func Success(data interface{}) *SuccessBean {  
    return &SuccessBean{200, "OK", data}  
}  
  
type ErrorBean struct {  
    Code uint32 `json:"code"`  
    Msg  string `json:"msg"`  
}  
  
func Error(errCode uint32, errMsg string) *ErrorBean {  
    return &ErrorBean{errCode, errMsg}  
}
```


### 1.3.2 myError
1. `error.go`
```go
package myError  
  
import "fmt"  
  
type CodeError struct {  
    errCode uint32  
    errMsg  string  
}  
  
// GetErrCode 获取错误码  
func (e *CodeError) GetErrCode() uint32 {  
    return e.errCode  
}  
  
// GetErrMsg 获取错误信息  
func (e *CodeError) GetErrMsg() string {  
    return e.errMsg  
}  
  
// Error 返回错误信息string  
func (e *CodeError) Error() string {  
    return fmt.Sprintf("ErrorCode:%d，ErrorMsg:%s", e.errCode, e.errMsg)  
}  
  
// NewErrCodeMsg 创建错误码和错误信息  
func NewErrCodeMsg(errCode uint32, errMsg string) *CodeError {  
    return &CodeError{errCode: errCode, errMsg: errMsg}  
}
```

2. `errorCode.go`
```go
package myError  
  
const CommonError uint32 = 100001 // 全局错误码
```

3. `errorMsg.go`
```go
package myError  
  
var message map[uint32]string  
  
func init() {  
    message = make(map[uint32]string)  
    message[CommonError] = "服务器出现了一些小问题，请稍后再试"  
}  
  
// GetErrMsg 获取错误信息  
func GetErrMsg(errCode uint32) string {  
    if msg, ok := message[errCode]; ok {  
       return msg  
    } else {  
       return "服务器出现了一些小问题，请稍后再试"  
    }  
}  
  
// IsCodeErr 判断错误码是否存在  
func IsCodeErr(errCode uint32) bool {  
    _, ok := message[errCode]  
    return ok  
}
```

### 1.3.3 返回错误的示范
```go
err = errors.Wrap(  
    myError.NewErrCodeMsg(  
       myError.CommonError, myError.GetErrMsg(myError.CommonError)),  
    "err")
```


## 1.4 jwt验证

### 1.4.0 说明
1. jwts中的`JwtPayLoad`结构体,可以根据业务要求更改
2. 配置文件中`AccessSecret`不要全是数字,不然可能会报错
3. 解析token由框架解决,直接去context取得解析结果

### 1.4.1 common/jwts

```go
package jwts  
  
import (  
    "github.com/golang-jwt/jwt/v4"  
    "time")  
  
// JwtPayLoad jwt中payload数据  
type JwtPayLoad struct {  
    UserID   uint   `json:"user_id"`  
    Username string `json:"username"` // 用户名  
    Role     int    `json:"role"`     // 权限  1 普通用户  2 管理员  
}  
  
type CustomClaims struct {  
    JwtPayLoad  
    jwt.RegisteredClaims  
}  
  
// GetToken 创建 Tokenfunc GetToken(user JwtPayLoad, accessSecret string, expires int64) (string, error) {  
    claim := CustomClaims{  
       JwtPayLoad: user,  
       RegisteredClaims: jwt.RegisteredClaims{  
          ExpiresAt: jwt.NewNumericDate(time.Now().Add(time.Hour * time.Duration(expires))),  
       },  
    }  
  
    token := jwt.NewWithClaims(jwt.SigningMethodHS256, claim)  
    return token.SignedString([]byte(accessSecret))  
}  
  
// ParseToken 解析 token//func ParseToken(tokenStr string, accessSecret string, expires int64) (*CustomClaims, error) {  
//  
//  token, err := jwt.ParseWithClaims(tokenStr, &CustomClaims{}, func(token *jwt.Token) (interface{}, error) {  
//     return []byte(accessSecret), nil  
//  })  
//  if err != nil {  
//     return nil, err  
//  }  
//  if claims, ok := token.Claims.(*CustomClaims); ok && token.Valid {  
//     return claims, nil  
//  }  
//  return nil, errors.New("invalid token")  
//}
```

### 1.4.2 api文件
> 在需要鉴权地路由前加上`jwt: Auth`
```api
@server (  
    prefix: /api/users  
    jwt:    Auth  
)  
service users {  
    @handler tokenLogin  
    get /tokenLogin returns (string)  
}
```

### 1.4.3 配置文件
`api/etc/.yaml`
```
Auth:  
  AccessSecret: 1234567890fa  
  AccessExpire: 3600
```

### 1.4.4 返回jwt-tokon
```go
func (l *LoginLogic) Login(req *types.LoginRequest) (resp *types.LoginResponse, err error) {  
    // todo: add your logic here and delete this line  
    auth := l.svcCtx.Config.Auth  
  
    token, err := jwts.GetToken(jwts.JwtPayLoad{  
       UserID:   1,  
       Username: "admin",  
       Role:     2,  
    }, auth.AccessSecret, auth.AccessExpire)  
    return &types.LoginResponse{  
       Token: token,  
    }, nil  
}
```

### 1.4.5 解析token
```go
func (l *TokenLoginLogic) TokenLogin() (resp string, err error) {  
    // todo: add your logic here and delete this line  
    username := l.ctx.Value("username").(string)  
    return "hello " + username, nil  
}
```

### 1.4.6 鉴权失败
修改`main.go`文件
```go
server := rest.MustNewServer(c.RestConf, rest.WithUnauthorizedCallback(JwtUnauthorizedResult))

// JwtUnauthorizedResult jwt验证失败的回调  
func JwtUnauthorizedResult(w http.ResponseWriter, r *http.Request, err error) {  
    fmt.Println(err) // 具体的错误，没带token，token过期？伪造token？  
    httpx.WriteJson(w, http.StatusOK, response.Success("jwt验证失败"))  
}
```


## 1.5 整合gorm

### 1.5.0 说明

- 没什么特殊的,主要是介绍添加插件的方法
```shell
go get -u gorm.io/gorm
go get gorm.io/driver/mysql
```

### 1.5.1 common/gormInit
`enter.go`
```go
package gorm  
  
import (  
    "fmt"  
    "gorm.io/driver/mysql"
	"gorm.io/gorm")  
  
type SqlConfig struct {  
    Host     string  
    Port     int  
    Username string  
    Password string  
    DbName   string  
}  
  
func GetSqlConnect(config SqlConfig) *gorm.DB {  
    var _db *gorm.DB  
    timeout := "10s" // Database connection timeout  
    //Stitch the dsn parameters below   
     dsn := fmt.Sprintf("%s:%s@tcp(%s:%d)/%s?charset=utf8&parseTime=True&loc=Local&timeout=%s",  
       config.Username, config.Password, config.Host, config.Port, config.DbName, timeout)  
  
    // Connect to sql to obtain a database instance for subsequent database read and write operations  
    var err error  
    _db, err = gorm.Open(mysql.Open(dsn), &gorm.Config{})  
    if err != nil {  
       panic("fail to connect to sql database, error=" + err.Error())  
    }  
  
    sqlDB, _ := _db.DB()  
  
    sqlDB.SetMaxIdleConns(20)  // Sets the maximum number of connections in the free connection pool  
    sqlDB.SetMaxOpenConns(100) // Sets the maximum number of open database connections  
  
    return _db  
}
```

### 1.5.2 配置文件
`etc/.yaml`
```
Mysql:  
  Host: 127.0.0.1  
  Port: 3306  
  Username: root  
  Password: root  
  Dbname: golearn
```

`api/internal/config/config.go`
```go
type Config struct {  
    rest.RestConf  
    Mysql struct {  
       Username string  
       Password string  
       Host     string  
       Port     int  
       Dbname   string  
    }  
}
```

`api/internal/svc/servicecontext.go`
```go
type ServiceContext struct {  
    Config config.Config  
    Db     *gorm.DB  
}  
  
func NewServiceContext(c config.Config) *ServiceContext {  
    db := gormInit.GetSqlConnect(gormInit.SqlConfig{  
       Host:     c.Mysql.Host,  
       Port:     c.Mysql.Port,  
       Username: c.Mysql.Username,  
       Password: c.Mysql.Password,  
       DbName:   c.Mysql.Dbname,  
    })  
    return &ServiceContext{  
       Config: c,  
       Db:     db,  
    }  
}
```

### 1.5.3 使用
`l.svcCtx.Db.Find(&users)`



## 1.6 api文档

1. 导包
```bash
go install github.com/zeromicro/goctl-swagger@latest
```
2. 生成`.json`文件
```bash
goctl api plugin -plugin goctl-swagger="swagger -filename app.json -host localhost:8888 -basepath /" -api ./user_api/user.api -dir   ./user_api/doc
```



# 2 rpc服务
## 2.1 rpc文件分析

- package:暂不知道用处
- `option go_package`: grpc文件生成的go文件的包名(最后在pb文件夹下),重要!
```proto
syntax = "proto3";  
  
package user_db;  
  
option go_package = "./user_db_service";  
  
message GetAccountByIdRequest {  
  string id = 1;  
}  
  
message Account {  
  uint64 id = 1;  
  string account = 2;  
  string password = 3;  
  string phone = 4;  
  string email = 5;  
}  
  
service AccountService {  
  rpc GetAccountById(GetAccountByIdRequest) returns (Account);  
}  
  
// goctl rpc protoc user_rpc.proto --go_out=./pb --go-grpc_out=./pb --zrpc_out=.
```

## 2.2 客户端调用
1. etc配置文件
```
UserRpc:  
  Etcd:  
    Hosts:  
      - 127.0.0.1:2379  
    Key: userrpc.rpc
```

注意,etcd下的key是rpc注册到etcd中的key

2. internal/config
```
type Config struct {  
    rest.RestConf  
    UserRpc zrpc.RpcClientConf  
}
```

3. internal/svc
```
type ServiceContext struct {  
    Config  config.Config  
    UserRpc accountservice.AccountService  
}  
  
func NewServiceContext(c config.Config) *ServiceContext {  
    return &ServiceContext{  
       Config:  c,  
       UserRpc: accountservice.NewAccountService(zrpc.MustNewClient(c.UserRpc)),  
    }  
}
```

4. 使用
```
response, err := l.svcCtx.UserRpc.GetAccountById(l.ctx, &accountservice.GetAccountByIdRequest{  
    Id: 1,  
})
```




