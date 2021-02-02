# HDDW项目部署手册

## 环境

Windows server2008 R2

Oracle JDK 1.8.0_201

MySQL Community Server 8.0.22

## 安装软环境

### JDK1.8.0_201

#### 安装

1. 双击运行 `jdk-8u201-windows-x64.exe` 文件，点击**下一步**

![image-20201202112501891](C:\Users\qiaojiyuan\AppData\Roaming\Typora\typora-user-images\image-20201202112501891.png)

2. 安装JDK，可更改安装路径，点击**下一步**

![image-20201202112536123](C:\Users\qiaojiyuan\AppData\Roaming\Typora\typora-user-images\image-20201202112536123.png)

3. 点击**确定**

![image-20201202112638161](C:\Users\qiaojiyuan\AppData\Roaming\Typora\typora-user-images\image-20201202112638161.png)

4. 安装JRE，JRE可与JDK安装在同一根目录。点击**下一步**

![image-20201202112713714](C:\Users\qiaojiyuan\AppData\Roaming\Typora\typora-user-images\image-20201202112713714.png)

5. 点击**关闭**，安装完成

![image-20201202112820470](C:\Users\qiaojiyuan\AppData\Roaming\Typora\typora-user-images\image-20201202112820470.png)

#### 配置系统变量

1. 点击左下角 开始 - 计算机（**右键点击**）- 属性

   高级系统设置 - 环境变量 - 新建系统变量 （**配置JJDK安装路径，若安装时修改了安装路径，请配置正确的安装路径**）

   >  JAVA_HOME
   >
   > C:\Program Files\Java\jdk1.8.0_201

![image-20201202113418904](C:\Users\qiaojiyuan\AppData\Roaming\Typora\typora-user-images\image-20201202113418904.png)

2. 修改Path系统变量值

   在尾部添加`%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;`（**若原变量值尾部没有分号，需添加英文分号`;`后再添加新内容**）

![image-20201202113550317](C:\Users\qiaojiyuan\AppData\Roaming\Typora\typora-user-images\image-20201202113550317.png)

3. 新建CLASSPATH系统变量（**注意前边有个`.`**）

   > CLASSPATH
   >
   > .;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar

![image-20201202113713960](C:\Users\qiaojiyuan\AppData\Roaming\Typora\typora-user-images\image-20201202113713960.png)

#### 验证

环境变量配置完成，验证安装配置结果，按`Win + R` 键打开运行，输入`cmd`，确定

![image-20201202113934952](C:\Users\qiaojiyuan\AppData\Roaming\Typora\typora-user-images\image-20201202113934952.png)

输入`java -version` 回车

看到如下输出信息说明JDK安装成功

![image-20201202113857030](C:\Users\qiaojiyuan\AppData\Roaming\Typora\typora-user-images\image-20201202113857030.png)

### MySQL Community Server 8.0.22

#### 安装

1. 解压`mysql-8.0.22-winx64.zip`文件

2. 将解压后的文件移动到想要安装的目录

3. 打开`my.ini`文件修改`basedir`到安装目录

![image-20201202130536691](C:\Users\qiaojiyuan\AppData\Roaming\Typora\typora-user-images\image-20201202130536691.png)

4. 点击左下角 开始 - 命令提示符（**右键点击**）- 以管理员身份运行

5. 通过`cd`命令进入安装路径

6. 执行`mysqld --initialize --console`初始化配置

7. 记录初始密码（可在任意区域 右键 - 标记 选中密码 `Ctrl + C`复制出来） 

#### 启动

8. 执行`mysqld --install`安装Mysql

9. 执行`net start mysql`启动Mysql

#### 修改初始密码

10. 执行`mysql -u root -p` 连接，输入刚刚记录的密码（可右键 粘贴）

11. 执行`ALTER USER root@localhost IDENTIFIED BY '123456';` 修改初始密码（注意`;`）

12. 执行`exit;`退出（注意`;`）

13. 再执行`mysql -u root -p`验证密码是否修改成功

![image-20201202130314880](C:\Users\qiaojiyuan\AppData\Roaming\Typora\typora-user-images\image-20201202130314880.png)

## 部署项目

