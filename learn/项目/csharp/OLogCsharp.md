# ♾️项目介绍

使用 c# 语言实现的日志库. 包含`日志字符串生成器`以及`日志文件写入器`

# ♾️构造器介绍
## 💫LogBuilder

> 默认构造器

```csharp
using OLog.Builders;  
  
var logBuilder = new LogBuilder();  
Console.WriteLine(logBuilder.Info("Info test"));  
Console.WriteLine(logBuilder.Debug("Debug test"));  
Console.WriteLine(logBuilder.Error("Error test"));  
Console.WriteLine(logBuilder.Warn("Warn test"));
```


```
[2025-04-14 20:42:09] [INFO] Info test
[2025-04-14 20:42:09] [DEBUG] Debug test
[2025-04-14 20:42:09] [ERROR] Error test
[2025-04-14 20:42:09] [WARN] Warn test
```

> 修改 time 格式

```csharp
using OLog.Builders;  
  
var logBuilder = new LogBuilder();  
logBuilder.SetTimeFormat("yyyy-MM-dd");  

Console.WriteLine(logBuilder.Info("Info test"));  
Console.WriteLine(logBuilder.Debug("Debug test"));  
Console.WriteLine(logBuilder.Error("Error test"));  
Console.WriteLine(logBuilder.Warn("Warn test"));
```

```
[2025-04-14] [INFO] Info test
[2025-04-14] [DEBUG] Debug test
[2025-04-14] [ERROR] Error test
[2025-04-14] [WARN] Warn test
```

## 💫ConsoleLogBuilder

> 控制台日志生成器: Info, Default 等日志类别字符, 在控制台显示时自带颜色
> 使用方法同 LogBuilder


## 💫LogFileBuilder

> 日志文件写入构造器, 将日志写入磁盘文件

- 文件目录: 当前运行的文件夹下, 创建一个 `olog` 文件夹
- 日志文件: 默认文件为 `log.txt`

```csharp
using OLog.Builders;  
using OLog.Enums;  
  
var logBuilder = new LogFileBuilder()  
    .SetStrategy(EStrategy.Always)  
    .SetCacheLength(20)  
    .SetFilePath("log1.txt")  
    .SetLogBuilder(new LogBuilder());  
  
logBuilder.Info("Info test");  
logBuilder.Debug("Debug test");  
logBuilder.Error("Error test");  
logBuilder.Warn("Warn test");
```

### 写入策略
- Always: 自动写入磁盘
- EveryN: 先将日志写入缓存中, 当缓存数超过 N 时, 全部flush到磁盘
- Never: 日志全部写入缓存中, 当且仅当用户主动调用flush函数时, 才将日志写入磁盘



