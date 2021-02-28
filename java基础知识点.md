# JAVA 基础知识总结

## JAVA 三大版本

1. **Java SE**：标准版 应用于桌面程序、控制台开发

2. **Java ME**：嵌入式开发

3. **Java EE / J2EE**：企业级开发 应用于服务器开发

## JDK、JRE、JVM

java作为跨平台的高级编程语言，**一次编译，多处运行**。首先要了解其开发运行所依赖的环境

1. **JDK：Java Development kit** 

   java开发环境，主要面向开发者，其中包含了java开发所需要的工具包，具有编译功能，将java文件编译为class文件，便于执行

2. **JRE：Java Runtime Enviroment**   

   java运行环境，只要面向用户，可运行编译后的class文件

3. **JVM： Java Virtual Machin**   

   java虚拟机，是java实现跨平台的核心，提供了一套与平台无关的代码执行方法。jre就是在次虚拟机上解释执行class文件

**三者关系为：JDK中包含了JRE；JRE中包含了JVM**

## 执行过程

Java是一种**编译型加解释型**语言，先通过编译器（javac.exe）将java代码编译成.class文件，即JVM的机器语言。再通过解释器（java.exe）将编译后的程序逐步解释给操作系统执行。

> **编译型语言**：将计算机不能识别的高级编程语言，转化为成计算机可执行的语言，编译成一个可执行文件。进行执行
>
> 优点是执行效率快，消耗内存小，但其编译后的可执行文件比解释型的可执行文件大，且编译后的文件依赖平台
>
> **解释型语言**：将计算机不能识别的高级编程语言，一句一句解释成机器语言并执行
>
> 其中间语言代码量小。且具有平台独立性
>
> 所以java初学者经常会通过javac命令将文本文档编写的java文件编译成class文件，再通过java命令执行编译后的class文件。

## 第一个java程序

```java
class test01 {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```

**编译 - 解释执行**

```shell
# 查看jdk版本
>java -version
java version "1.8.0_171"
Java(TM) SE Runtime Environment (build 1.8.0_171-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.171-b11, mixed mode)
# 编译
>javac test01.java
# 解析执行
>java test01
Hello World!
```

## 标识符

**关键字**：系统预先定义好的一批词语，程序自定义的标识符不允许以此命名

> String、Integer等是内置类，不是关键字

| **关键字**   | **含义**                                                     |
| ------------ | ------------------------------------------------------------ |
| abstract     | 表明类或者成员方法具有抽象属性                               |
| assert       | 断言，用来进行程序调试                                       |
| boolean      | 基本数据类型之一，声明布尔类型的关键字                       |
| break        | 提前跳出一个块                                               |
| byte         | 基本数据类型之一，字节类型                                   |
| case         | 用在switch语句之中，表示其中的一个分支                       |
| catch        | 用在异常处理中，用来捕捉异常                                 |
| char         | 基本数据类型之一，字符类型                                   |
| class        | 声明一个类                                                   |
| const        | 保留关键字，没有具体含义                                     |
| continue     | 回到一个块的开始处                                           |
| default      | 默认，例如，用在switch语句中，表明一个默认的分支。Java8 中也作用于声明接口函数的默认实现 |
| do           | 用在do-while循环结构中                                       |
| double       | 基本数据类型之一，双精度浮点数类型                           |
| else         | 用在条件语句中，表明当条件不成立时的分支                     |
| enum         | 枚举                                                         |
| extends      | 表明一个类型是另一个类型的子类型。对于类，可以是另一个类或者抽象类；对于接口，可以是另一个接口 |
| final        | 用来说明最终属性，表明一个类不能派生出子类，或者成员方法不能被覆盖，或者成员域的值不能被改变，用来定义常量 |
| finally      | 用于处理异常情况，用来声明一个基本肯定会被执行到的语句块     |
| float        | 基本数据类型之一，单精度浮点数类型                           |
| for          | 一种循环结构的引导词                                         |
| goto         | 保留关键字，没有具体含义                                     |
| if           | 条件语句的引导词                                             |
| implements   | 表明一个类实现了给定的接口                                   |
| import       | 表明要访问指定的类或包                                       |
| instanceof   | 用来测试一个对象是否是指定类型的实例对象                     |
| int          | 基本数据类型之一，整数类型                                   |
| interface    | 接口                                                         |
| long         | 基本数据类型之一，长整数类型                                 |
| native       | 用来声明一个方法是由与计算机相关的语言（如C/C++/FORTRAN语言）实现的 |
| new          | 用来创建新实例对象                                           |
| package      | 包                                                           |
| private      | 一种访问控制方式：私用模式                                   |
| protected    | 一种访问控制方式：保护模式                                   |
| public       | 一种访问控制方式：共用模式                                   |
| return       | 从成员方法中返回数据                                         |
| short        | 基本数据类型之一,短整数类型                                  |
| static       | 表明具有静态属性                                             |
| strictfp     | 用来声明FP_strict（单精度或双精度浮点数）表达式遵循[IEEE 754](https://baike.baidu.com/item/IEEE 754)算术规范 |
| super        | 表明当前对象的父类型的引用或者父类型的构造方法               |
| switch       | 分支语句结构的引导词                                         |
| synchronized | 表明一段代码需要同步执行                                     |
| this         | 指向当前实例对象的引用                                       |
| throw        | 抛出一个异常                                                 |
| throws       | 声明在当前定义的成员方法中所有需要抛出的异常                 |
| transient    | 声明不用序列化的成员域                                       |
| try          | 尝试一个可能抛出异常的程序块                                 |
| void         | 声明当前成员方法没有返回值                                   |
| volatile     | 表明两个或者多个变量必须同步地发生变化                       |
| while        | 用在循环结构中                                               |

**自定义标识符规则：**

1. 必须以字母、美元符($)、下划线(_)开始，如

2. 首字母之后可以包含字母、美元符($)、下划线(_)、数字

3. 标识符是大小写敏感的

## 注释

> 代码是写给机器执行的，注释是写给开发人员阅读的
>
> 注释不会出现在编译后的class文件中，并不会影响代码的执行

```java
// 单行注释

/*
 * 多行注释
 * 
 */

/**
 * 头注释（用于方法、类上解释其功能、参数、返回值）
 * @Description 
 * @Author
 */
```

## 数据类型

java数据类型分为**基本数据类型**和**引用数据类型**

> java中0开头代表八进制、0x开头代表十六进制、\开头代表转义、\u开头代表unicode字符

### 基本数据类型

1. **byte**：占1个字节【-128 ~ 127】

2. **short**：占2个字节【-32768 ~ 32767】

3. **int**：占4个字节【-2147483648 ~ 2147483647】

4. **long**：占8个字节 

5. **float**：占4个字节【8位有效位】

6. **double**：占8个字节【17位有效位】

7. **char**：占2个字节【一个字符】

8. **boolean**：占1个Bit

**long、float在定义时要加上L、F后缀，以用来区分int和double类型**

> **数据类型转换：**
>
> 优先级(低 -> 高) **byte, short, char -> int -> long -> float -> double**
>
> **低 -> 高 自动类型转换** `int a = 100;long b = a;`
>
> **高 -> 低 强制类型转换**  (数据类型) 数据`long a = 100L; int b = (int)a`
>
> **布尔值不能转换**

### 引用数据类型

> 引用数据类型即java为每一个基本数据类型提供了一个封装类，并为每一种数据类型提供了一些基本的操作方法
>
> 基本数据类型直接存在栈内存中，效率更高，但是没有常用的操作方法
>
> 引用数据类型实际值存在堆内存中，在栈内存中存放对象的引用，只能通过引用访问。

**Byte，Short，Integer，Long，Float，Double，String，Boolean**

## 运算符

四则运算：+ - * / %(求余)

自增运算：++

自减运算：--

等于判断：== 

不等于判断：!=

与或非逻辑运算：&& || !

三元表达式：? :

赋值运算：+= -= 

位运算符：&、 |、^、 ~、 <<、 >>

```java
public static void main(String[] args) {
    int a = 10;
    int b = 3;
    int c = 1;
    int d = 1;
    int e = 1;
    System.out.println("a + b = " + (a + b)); // a + b = 13
    System.out.println("a - b = " + (a - b)); // a - b = 7
    System.out.println("a * b = " + (a * b)); // a * b = 30
    System.out.println("a / b = " + (a / b)); // a / b = 3
    System.out.println("a % b = " + (a % b)); // a / b = 3
    System.out.println("c++ = " + (c++)); // c++ = 1
    System.out.println("c++之后，c = " + c); // c++之后，c = 2
    System.out.println("++c = " + (++c)); // ++c = 3
    System.out.println("++c之后，c = " + c); // ++c之后，c = 3
    System.out.println("d-- = " + (d--)); // d-- = 1
    System.out.println("d--之后，d = " + d); // d--之后，d = 0
    System.out.println("--d = " + (--d)); // --d = -1
    System.out.println("--d之后，d = " + d); // --d之后，d = -1
    System.out.println("a == b: " + (a == b)); // a == b: false
    System.out.println("a != b: "+ (a != b)); // a != b: true
    System.out.println("a == 10 && b == 10: " + (a == 10 && b == 10)); // a == 10 && b == 10: false
    System.out.println("a == 10 || b == 10: " + (a == 10 || b == 10)); // a == 10 || b == 10: true
    System.out.println("!(a == 10): " + !(a == 10)); // !(a == 10): false
    System.out.println("a == 10 ? a : b = " + (a == 10 ? a : b)); // a == 10 ? a : b = 10
    e += 10;
    System.out.println("e += 10之后，e = " + e); // e += 10之后，e = 11
    e -= 10;
    System.out.println("e -= 10之后，e = " + e); // e -= 10之后，e = 1
}
```

## 数组

一类相同数据类型的数据的有序集合

数组大小初始化的时候定义，不能改变

通过数组下表访问元素，从0开始

定义：数据类型[] 变量名 = new 数据类型[元素个数]

初始化：

​    数据类型[] 变量名 = new 数据类型[]{逗号分隔数据集合}；  

​    数据类型[] 变量名 = {逗号分隔数据集合}；

多维数组

定义：数据类型[] [] 变量名 = new 数据类型[元素个数] [元素个数]

初始化：

​    数据类型[] [] 变量名 = new 数据类型[]{{}, {}}；  

​    数据类型[] [] 变量名 = {{}, {}}；



## 结构

### 顺序结构

### 选择结构

if else

switch (byte short int char string) break、

​	swich 会生成跳转表，占用内存多，但是效率高

​	ifelse 会遍历分支条件，直至命中条件

### 循环结构

while 可能执行0次

do..while 至少执行一次

for

增强for

break contionue

 

## 方法

方法、函数 可重用的代码段 解决一类问题的代码集合  一个方法只解决一个问题

方法的定义与调用

修饰符 返回类型 方法名(参数类型 参数名 ...) { }

public Integer add(Integer a, Integer b) { return a + b; }

方法重载

名称相同 

参数列表不同（类型、个数、顺序）保证通过参数能匹配到唯一的一个方法 

返回类型可相同也可不相同



可变参数

只能有一个且只能放在最后一个。

public Integer add(Integer... a) { return a[0] + a[1]; }



## 面向对象

面向过程与面向对象

面向过程： 步骤清晰，按步执行，适合处理简单问题

### 类与对象

通过分类，将同一类事务的属性和行为总结为一个类，社和处理复杂的多人协作的问题

以类的方式组织代码，以对象的方式封装数据

类是抽象的，对象是类的实例，通过new 实例化 

构造函数

名称与类名相同，且无返回类型

默认无参，定义有参时，无参会失效，须显示定义无参

### 封装 

隐藏实现细节，保护数据，提高安全性

​    getter/setter

### 继承

​    所有类默认继承Object

​    this	super

​    子类无参构造会默认调用父类无参构造

​    方法重写

​    静态方法可以被继承，但不能被重写

### 多态

 多态是同一个行为具有多个不同表现形式或形态的能力。、

​    继承

​    重写

​    父类引用指向子类对象：Parent p = new Child();



## static final



## 抽象类

​		定义通用东西（普通方法）

​        约束定制化东西（抽象方法）

## 接口

​        只有规范



## Error Exception



## 包机制

package 包全路径

import



