# ♾️enter.go
```go
package output  
  
func DyeText(text string, color string) string {  
    return color + text + Reset  
}  
  
func Error() string {  
    return DyeText("[Error] ", Red)  
}  
  
func Info() string {  
    return DyeText("[Info] ", Green)  
}  
  
func Default() string {  
    return DyeText("[Default] ", Grey)  
}
```

# ♾️colorEnum.go
```go
package output  
  
const (  
    Green = "\033[32m"  
    Red   = "\033[31m"  
    Grey  = "\033[90m"  
  
    Reset = "\033[0m"  
)
```
