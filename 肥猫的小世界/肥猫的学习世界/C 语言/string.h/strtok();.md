# C 库函数 - strtok()

 [![C 标准库 - <string.h>](https://www.runoob.com/images/up.gif) C 标准库 - <string.h>](https://www.runoob.com/cprogramming/c-standard-library-string-h.html)

## 描述

C 库函数 **char *strtok(char *str, const char *delim)** 分解字符串 **str** 为一组字符串，**delim** 为分隔符。

## 声明

下面是 strtok() 函数的声明。

char *strtok(char *str, const char *delim)

## 参数

- **str** -- 要被分解成一组小字符串的字符串。
- **delim** -- 包含分隔符的 C 字符串。

## 返回值

该函数返回被分解的第一个子字符串，如果没有可检索的字符串，则返回一个空指针。

## 实例

下面的实例演示了 strtok() 函数的用法。

## 实例

```c
#include <string.h>
#include <stdio.h>
 
int main () {
   char str[80] = "This is - www.runoob.com - website";
   const char s[2] = "-";
   char *token;
   
   /* 获取第一个子字符串 */
   token = strtok(str, s);
   
   /* 继续获取其他的子字符串 */
   while( token != NULL ) {
      printf( "%s\n", token );
    
      token = strtok(NULL, s);
   }
   
   return(0);
}
```

让我们编译并运行上面的程序，这将产生以下结果：

```
This is 
 www.runoob.com 
 website
```

## 笔记
    
```c
#include <string.h>
#include <stdio.h>

int main()
{
char str[80] = "This is - www.runoob.com - website";
const char s[2] = "-";
char *token;

/* 获取第一个子字符串 */
token = strtok(str, s);

/* 继续获取其他的子字符串 */
while (token != NULL)
{
printf("%s\n", token);

token = strtok(NULL, s);
}
printf("\n");
for (int i = 0; i < 34;i++)
printf("%c", str[i]);

return (0);
}
```

输出：

```c
This is 
www.runoob.com 
website

This is  www.runoob.com  website
```
