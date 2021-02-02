Java SE    标准版 应用于桌面程序、控制台开发

Java ME    嵌入式开发

Java EE / J2EE    企业级开发 应用于服务器开发



JDK    Java Development kit    java开发与运行环境

JRE    Java Runtime Enviroment    java运行环境

JVM    Java Virtual Machine    java虚拟机

JDK中包含了JRE；JRE中包含了JVM



Java - 编译型 加 解释型语言

java先通过编译器（javac.exe）将java代码编译成.class文件，即JVM的机器语言。

再通过解释器（java.exe）将编译后的程序逐步解释给操作系统执行



注释

```java
// 单行注释

/*
 * 多行注释
 * 
 */

/**
 * 文档注释
 * @Description 
 * @Author
 */
```



标识符

关键字：系统预先定义好的一批词语，程序自定义的标识符不允许以此命名（String、Integer是类，不是关键字）

自定义标识符规则：

必须以字母、美元符($)、下划线(_)开始

首字母之后可以包含字母、美元符($)、下划线(_)、数字

标识符是大小写敏感的



数据类型

基本数据类型

byte   1个字节    -128 ~ 127

short   2个字节    -32768 ~ 32767

int   4个字节     -2147483648 ~ 2147483647

long   8个字节 

float   4个字节     8位有效位

double   8个字节     17位有效位

char   2个字节

boolean     1个Bit

long、float在定义时要加上L、F后缀

java中0开头代表八进制、0x开头代表十六进制、\开头代表转义、\u开头代表unicode字符



引用数据类型

Byte

Short

Integer

Long

Float

Double

String

Boolean

