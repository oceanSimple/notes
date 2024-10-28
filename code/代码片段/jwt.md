1. 获取包
```shell
go get -u github.com/golang-jwt/jwt/v5
```


```go
package tool  
  
import (  
    "errors"  
    "github.com/golang-jwt/jwt/v5"    "time")  
  
// empty struct  
type JwtPayLoad struct {  
}  
  
type CustomClaims struct {  
    JwtPayLoad  
    jwt.RegisteredClaims  
}  
  
func GetJwtToken(user JwtPayLoad, accessSecret string, expires int64) (string, error) {  
    claim := CustomClaims{  
       JwtPayLoad: user,  
       RegisteredClaims: jwt.RegisteredClaims{  
          ExpiresAt: jwt.NewNumericDate(time.Now().Add(time.Hour * time.Duration(expires))),  
       },  
    }  
  
    token := jwt.NewWithClaims(jwt.SigningMethodHS256, claim)  
    return token.SignedString([]byte(accessSecret))  
}  
  
func ParseJwtToken(tokenStr string, accessSecret string) (*CustomClaims, error) {  
    token, err := jwt.ParseWithClaims(tokenStr, &CustomClaims{}, func(token *jwt.Token) (interface{}, error) {  
       return []byte(accessSecret), nil  
    })  
    if err != nil {  
       return nil, err  
    }  
    if claims, ok := token.Claims.(*CustomClaims); ok && token.Valid {  
       return claims, nil  
    }  
    return nil, errors.New("invalid jwt token")  
}
```
