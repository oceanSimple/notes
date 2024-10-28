# ♾️导包
[学习网址](https://darjun.github.io/2020/02/16/godailylib/email/)

```shell
go get github.com/jordan-wright/email
```

> pop3Code需要去邮箱获取
# ♾️基础用法
```go
package main  
  
import (  
    "github.com/jordan-wright/email"  
    "net/smtp")  
  
const (  
    sender   = "1252074183@qq.com"  
    pop3Code = "puzrtecamezmiifb"  
)  
  
func main() {  
    // 基本用法  
    e := email.NewEmail()  
    e.From = "ocean<ocean0703@qq.com>"  
    e.To = []string{"hhy1252074183@gmail.com"}  
  
    e.Subject = "测试email发送"  
    e.Text = []byte("这是一封测试邮件")  
  
    err := e.Send("smtp.qq.com:587",  
       smtp.PlainAuth("", sender, pop3Code, "smtp.qq.com"))  
    if err != nil {  
       panic(err)  
    }  
}
```

# ♾️连接池用法
```go
package main  
  
import (  
    "fmt"  
    "github.com/jordan-wright/email"    "net/smtp"    "time")  
  
const (  
    sender   = "1252074183@qq.com"  
    pop3Code = "puzrtecamezmiifb"  
)  
  
func main() {  
    pool, err := email.NewPool("smtp.qq.com:587", 1,  
       smtp.PlainAuth("", sender, pop3Code, "smtp.qq.com"))  
    if err != nil {  
       fmt.Printf("new pool err: %s", err)  
       return  
    }  
  
    e := email.NewEmail()  
    e.From = sender  
    e.To = []string{"hhy1252074183@gmail.com"}  
    e.Subject = "测试email发送"  
    e.Text = []byte("这是一封测试邮件")  
  
    if err := pool.Send(e, 10*time.Second); err != nil {  
       fmt.Printf("send err: %s", err)  
       return  
    }  
}
```


