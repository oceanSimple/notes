# ♾️导言
1. 关于int类型
	- 最低为16位，通常为32位（4bytes）
	- 由电脑和编译器决定，在我的环境中，int为32位
	- 如果想使用64位int，如下
```c
#include <stdio.h>
#include <stdint.h>

int main(int argc , char const* argv [ ]) {
  printf("Size of int: %zu bytes\n" , sizeof(int64_t));
}
```

2. 占位符
![[Pasted image 20240805103251.png]]

![[Pasted image 20240805103602.png]]

3. C语言强制要求for循环语句必须有一个循环体，因此需要用单独的分号代替
![[Pasted image 20240805105805.png]]


# ♾️类型、运算符、表达式
1. 变量命名规范
	- 只能包含大小写字母、数字和下划线
	- 不能以数字开头
2. 基本类型
![[Pasted image 20240805181109.png]]

3. 常量
