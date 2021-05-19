# Docker

## 架构

![docker-framework](D:\Typora\images\docker\docker-framework.png)

**Docker Client**: Docker客户端，命令窗口，执行docker命令，

**Docker Host**: Docker服务端

**Docker daemon**: Docker守护进程，主要作用有：运行客户端发送的命令，获取远程查过库的镜像，实例化本地镜像

**Registry**:  远程镜像仓库

**Image**: 本地镜像

**Containers**: 镜像实例化的容器



## 安装

> 官网安装地址：https://docs.docker.com/get-docker/
>
> 本文安装环境：CentOS 7    3.10.0-862.el7.x86_64

```shell
 
 # 卸载旧环境
 $ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
# 需要的安装包
$ sudo yum install -y yum-utils

# 设置镜像地址，官方给出的是国外仓库地址
# https://download.docker.com/linux/centos/docker-ce.repo，
# 国内访问巨慢，建议换成国内地址
$ sudo yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
# 安装Docker，docker-ce：社区版； docker-ee：企业版
$ sudo yum install docker-ce docker-ce-cli containerd.io
# 安装指定版本
# sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io

# 使用docker version 判断安装结果
$ docker version
$ docker info # 查看更为详细的docker系统信息
```

> Docker默认工作路径：/var/lib/docker
>
> 卸载Docker:
>
> `yum remove docker-ce docker-ce-cli containerd.io`
>
> `rm -rf /var/lib/docker`
>
> `rm -rf /var/lib/containerd`

## Hello World

```shell
# 启动Docker
$ systemctl start docker

# 拉取并启动hello world镜像
$ docker run hello-world
Unable to find image 'hello-world:latest' locally # 首先查看本地镜像，若本地没有当前镜像
latest: Pulling from library/hello-world # 去远程镜像仓库查找当前镜像
b8dfde127a29: Pull complete # 成功从远程仓库拉取到了镜像
Digest: sha256:5122f6204b6a3596e048758cabba3c46b1c937a46b5be6225b835d091b90e46c # 镜像信息
Status: Downloaded newer image for hello-world:latest
# 启动镜像

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
 
 
# 查看本地镜像列表
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              d1165f221234        2 months ago        13.3kB
```



## 镜像命令

> 官方镜像地址：https://hub.docker.com/

### 查找镜像

从Docker Hub查找镜像 

**docker search [OPTIONS] TERM**

​        **-f --filter:显示满足条件的镜像**

```shell
$ docker search mysql --filter=stars=3000
NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql               MySQL is a widely used, open-source relation…   10881               [OK]                
mariadb             MariaDB Server is a high performing open sou…   4104                [OK] 
```

### 获取镜像

从镜像仓库中拉取或者更新指定镜像 

**docker pull [OPTIONS] NAME[:TAG|@DIGEST]**

```shell
$ docker pull mysql
Using default tag: latest # 使用最新版本
latest: Pulling from library/mysql
69692152171a: Pull complete # 分层下载
1651b0be3df3: Pull complete 
951da7386bc8: Pull complete 
0f86c95aa242: Pull complete 
37ba2d8bd4fe: Pull complete 
6d278bb05e94: Pull complete 
497efbd93a3e: Pull complete 
f7fddf10c2c2: Pull complete 
16415d159dfb: Pull complete 
0e530ffc6b73: Pull complete 
b0a4a1a77178: Pull complete 
cd90f92aa9ef: Pull complete 
Digest: sha256:d50098d7fcb25b1fcb24e2d3247cae3fc55815d64fec640dc395840f8fa80969 # 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest # 实际地址  

$ docker pull mysql:5.7
5.7: Pulling from library/mysql
69692152171a: Already exists # 已存在的分层可直接复用
1651b0be3df3: Already exists 
951da7386bc8: Already exists 
0f86c95aa242: Already exists 
37ba2d8bd4fe: Already exists 
6d278bb05e94: Already exists 
497efbd93a3e: Already exists 
a023ae82eef5: Pull complete  # 只获取变更的内容分层
e76c35f20ee7: Pull complete 
e887524d2ef9: Pull complete 
ccb65627e1c3: Pull complete 
Digest: sha256:a682e3c78fc5bd941e9db080b4796c75f69a28a8cad65677c23f7a9f18ba21fa
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

> `docker pull mysql` 等同于 `docker pull docker.io/library/mysql:latest`

> **联合文件系统UFS**：是一种分层的、轻量级的文件系统，将对文件的修改作为一次提交来一层一层的叠加。
>
> UFS是Docker镜像的基础，镜像可以通过分层来继承基础镜像。

### 查看镜像

列出本地镜像

**docker images [OPTIONS] [REPOSITORY[:TAG]]**

​        **-a --all:列出本地所有的镜像（含中间映像层，默认情况下，过滤掉中间映像层）**
​        **-f --filter:显示满足条件的镜像**
​        **-q --quiet:只显示镜像ID**

```shell
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mysql               5.7                 2c9028880e58        4 days ago          447MB
mysql               latest              c0cdc95609f1        4 days ago          556MB
hello-world         latest              d1165f221234        2 months ago        13.3kB

$ docker images -aq
c0cdc95609f1
d1165f221234
5726af297dd4
564b3b43485e
b28df07deb6c
7e6257c9f8d8
0d120b6ccaa8
2ae23eb477aa
f60d84d4d72c
```

### 删除镜像

删除本地一个或多少镜像

**docker rmi [OPTIONS] IMAGE [IMAGE...]**

​        **-f :强制删除**

​        **--no-prune :不移除该镜像的过程镜像，默认移除**

```shell
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mysql               5.7                 2c9028880e58        4 days ago          447MB
mysql               latest              c0cdc95609f1        4 days ago          556MB
hello-world         latest              d1165f221234        2 months ago        13.3kB

# 等价与 docker rmi -f mysql:5.7
$ docker rmi -f 2c9028880e58
Untagged: mysql:5.7
Untagged: mysql@sha256:a682e3c78fc5bd941e9db080b4796c75f69a28a8cad65677c23f7a9f18ba21fa
Deleted: sha256:2c9028880e5814e8923c278d7e2059f9066d56608a21cd3f83a01e3337bacd68
Deleted: sha256:c49c5c776f1bc87cdfff451ef39ce16a1ef45829e10203f4d9a153a6889ec15e
Deleted: sha256:8345316eca77700e62470611446529113579712a787d356e5c8656a41c244aee
Deleted: sha256:8ae51b87111404bd3e3bde4115ea2fe3fd2bb2cf67158460423c361a24df156b
Deleted: sha256:9d5afda6f6dcf8dd59aef5c02099f1d3b3b0c9ae4f2bb7a61627613e8cdfe562
```

> Linux中可通过$()的方式将命令的执行结果作为另一条命令的执行参数
>
> **删除所有镜像：`docker rmi -f $(docker images -aq)`**



## 容器命令

> 下述案例通过centos镜像为例。可以先通过`docker pull centos`获取镜像

### 运行容器

创建并启动一个新的容器

**docker run [OPTIONS] IMAGE [COMMAND] [ARG...]**

​        **-d 后台运行**
​	    **-t 为容器重新生成一个伪输入终端**
​	    **-i 以交互模式运行容器**
​	    **-P 随机端口映射**
​	    **-p 指定端口映射 主机端口:容器端口**
​	    **--name="" 指定容器名称**
​	    **-v 挂载 本地目录:容器内目录**

```shell
# /bin/bash打开命令窗口
[root@qjy]# docker run -it centos /bin/bash
[root@87265183b4ad /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
# 停止容器，退出
[root@87265183b4ad /]# exit
exit
[root@qjy]# 
```

> -d 后台运行时，必须要有一个前台进程，docker发现没有应用，就会自动停止。
>
> **退出容器，后台运行：`Ctrl + P + Q`**

### 查看运行的容器

列出正在运行的容器 

**docker ps [OPTIONS]**

​    **-a 显示所有历史运行过的的容器，包括未运行的**
​	**-f 根据条件过滤显示的内容 -f label=color**
​	**-l 显示最近创建的容器**
​	**-n 列出最近创建的n个容器**
​	**--no-trunc 不截断输出**
​	**-q 只显示容器编号**
​	**-s 显示总的文件大小**

```shell
$ docker ps -a
CONTAINER ID    IMAGE       COMMAND           CREATED             STATUS                      PORTS               NAMES
87265183b4ad    centos      "/bin/bash"       5 minutes ago       Exited (0) 2 minutes ago                        friendly_ride
```

### 删除容器

删除一个或多个容器

**docker rm [OPTIONS] CONTAINER [CONTAINER...]**

​    **-f 强制删除**
​	**-l 移除网络连接**
​	**-v 删除挂在的数据卷**

```shell
$ docker rm 87265183b4ad
87265183b4ad
```

> **删除所有容器：`docker rm -f $(docker ps -aq)`**

### 启动、停止、删除容器

启动一个或多个已停止的容器 / 停止一个正在运行的容器 / 重启容器

**docker start/stop/restart [OPTIONS] CONTAINER [CONTAINER...]**

**docker kill [OPTIONS] CONTAINER [CONTAINER...]**

### 查看容器日志

获取容器的日志

**docker logs [OPTIONS] CONTAINER**
	**-f 跟踪日志输出**
	**--since 显示某个开始时间的所有日志**
	**-t 显示时间戳**
	**--tail 仅列出最新N条容器日志**

```shell
$ docker run -d centos /bin/bash -c "while true;do echo docker;sleep 1;done;"
92f4e7234ead7d9feac48cf394c479333855292a3e35be5f5a4a70a4572a1994
$ docker ps 
CONTAINER ID   IMAGE    COMMAND                  CREATED             STATUS              PORTS               NAMES
92f4e7234ead   centos   "/bin/bash -c 'while…"   7 seconds ago       Up 6 seconds                            infallible_feynman
$ docker logs -ft --tail 10 92f4e7234ead
2021-05-17T06:27:26.774346245Z docker
2021-05-17T06:27:27.781174458Z docker
2021-05-17T06:27:28.790260292Z docker
2021-05-17T06:27:29.796359672Z docker
2021-05-17T06:27:30.802464903Z docker
2021-05-17T06:27:31.807643914Z docker
2021-05-17T06:27:32.811927412Z docker
2021-05-17T06:27:33.818802350Z docker
2021-05-17T06:27:34.823597810Z docker
2021-05-17T06:27:35.829822605Z docker
2021-05-17T06:27:36.836026570Z docker
2021-05-17T06:27:37.849264894Z docker
```

### 查看容器内的进程信息

**docker top [CONTAINER]**

```shell
$ docker top 325561fbacd1
UID       PID       PPID           C                   STIME               TTY                 TIME                CMD
root      8183      8166           0                   23:32               pts/0               00:00:00            /bin/bash
```

### 获取容器/镜像的元数据

**docker inspect [OPTIONS] NAME|ID [NAME|ID...]**

### 连接到正在运行中的容器 

**docker attach [OPTIONS] CONTAINER**

**docker exec [OPTIONS] CONTAINER COMMAND [ARG...]**
	    **-t 为容器重新生成一个伪输入终端**
	    **-i 以交互模式运行容器**

> docker exec：重新开启一个终端
>
> docker attach：直接进入正在执行的终端

```shell
$ docker run -d centos /bin/bash -c "while true;do echo docker;sleep 1;done;"
770f6ae8aaaf0eee518ad766b05aa74c31e0f9fa824e42e533308f05d22ddba8
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
770f6ae8aaaf        centos              "/bin/bash -c 'while…"   17 seconds ago      Up 15 seconds                           silly_kalam

# exec
[root@qjy]# docker exec -it 770f6ae8aaaf /bin/bash
[root@770f6ae8aaaf /]# 

# attach
[root@qjy]# docker attach 770f6ae8aaaf
docker
docker
docker
docker
docker
```

### 文件传输

用于容器与主机之间的数据拷贝 

**docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH**
**docker cp [OPTIONS] SRC_PATH CONTAINER:DEST_PATH**

```shell
$ docker ps
CONTAINER ID        IMAGE     COMMAND             CREATED             STATUS              PORTS           NAMES
2fa5bb6c4a1c        centos    "/bin/bash"         8 seconds ago       Up 7 seconds                        reverent_pasteur

# 在外边创建qjy.txt文件
[root@qjy]# ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
[root@qjy]# touch qjy.txt
[root@qjy]# ls
Desktop  Documents  Downloads  Music  Pictures  Public  qjy.txt  Templates  Videos

# 在docker内创建docker.txt文件
[root@qjy]# docker attach 2fa5bb6c4a1c
[root@2fa5bb6c4a1c /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@2fa5bb6c4a1c /]# touch docker.txt
[root@2fa5bb6c4a1c /]# ls
bin  dev  docker.txt  etc  home  lib  lib64  lost+found  media	mnt  opt  proc	root  run  sbin  srv  sys  tmp	usr  var
[root@2fa5bb6c4a1c /]# 

# 相互传输文件
[root@qjy]# docker cp qjy.txt 2fa5bb6c4a1c:/
[root@qjy]# docker cp 2fa5bb6c4a1c:/docker.txt ./
[root@qjy]# ls
Desktop  docker.txt  Documents  Downloads  Music  Pictures  Public  qjy.txt  Templates  Videos
[root@qjy]# docker attach 2fa5bb6c4a1c
[root@2fa5bb6c4a1c /]# ls
bin  dev  docker.txt  etc  home  lib  lib64  lost+found  media	mnt  opt  proc	qjy.txt  root  run  sbin  srv  sys  tmp  usr  var
```

## 提交镜像

> 官方镜像大多数都是最小安装版，缩减了很多内容，以tomcat为例，tomcat镜像中webapp目录为空，webapp中的内容被放到了webapp.dist中
>
> 我们可以将webapp.dist中的内容复制到webapp中，制作一个新的tomcat镜像。

将容器保存为新镜像

**docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]**
	    **-a :提交的镜像作者**
	    **-c :使用Dockerfile指令来创建镜像**
	    **-m :提交时的说明文字**
	    **-p :在commit时，将容器暂**

```shell
$ docker run -it -p8080:8080 --name="tomcat" 2ae23eb477aa
...
18-May-2021 02:13:24.478 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in [326] milliseconds

# 启动官方tomcat
[root@qjy]# docker exec -it f26a739acda3 /bin/bash
root@f26a739acda3:/usr/local/tomcat# ls
BUILDING.txt  CONTRIBUTING.md  LICENSE	NOTICE	README.md  RELEASE-NOTES  RUNNING.txt  bin  conf  lib  logs  native-jni-lib  temp  webapps  webapps.dist  work
root@f26a739acda3:/usr/local/tomcat# cd webapps
root@f26a739acda3:/usr/local/tomcat/webapps# ls

# 复制webapp内容
root@f26a739acda3:cp -r ../webapps.dist/* ./
root@f26a739acda3:/usr/local/tomcat/webapps# ls
ROOT  docs  examples  host-manager  manager

# 提交新的镜像
$ docker commit -a="qjy" -m="add webapp for tomcat" f26a739acda3 tomcat-app:1.0
sha256:f6be24f100c8862c50d41b7a9c2da4ac18e32735ce467556d74ce43893efca1e
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tomcat-app          1.0                 f6be24f100c8        5 seconds ago       652MB
tomcat              latest              2ae23eb477aa        9 months ago        647MB
```

## 数据卷挂载

将容器内的目录挂在到Linux上，数据双向绑定，**实现容器数据的持久化和同步操作**

**-v 挂载 本地目录:容器内目录:权限(ro/rw)**（指定路径挂载）

**权限默认rw读写，若指定了ro只读则文件只能通过宿主机操作。容器内不能改变**

> **匿名挂载**：若挂载时不指定本地目录，则会默认分配一个地址挂载 -v 容器内目录
>
> **具名挂载**：挂载时指定挂载名 -v 挂载名:容器内目录
>
> **冒号前的部分以  /  开始则认为是指定路径挂载，若不是以  /  开始，则认为是具名挂载**

**查看当前挂载情况 `docker volume ls `**

**挂载详细信息 `docker volume inspect 挂载名`**

```shell
[root@home]# ls
maradb  mariadb  myEnvDockerFile  qjy
[root@home]# mkdir v-test
[root@home]# ls
maradb  mariadb  myEnvDockerFile  qjy  v-test

# 将Linux中/home/v-test与容器内/home目录绑定
[root@home]# docker run -it -v /home/v-test:/home centos
[root@7470228130ab /]# cd home 
[root@7470228130ab home]# ls
# 容器内/home创建文件test.java
[root@7470228130ab home]# touch test.java

# Linux中/home/v-test同步到test.java文件
[root@home]# cd v-test/
[root@v-test]# ls
test.java
# Linux中/home/v-test创建文件test2.txt
[root@v-test]# touch test2.txt

# 容器内/home同步到test2.txt文件
[root@v-test]# docker attach 7470228130ab
[root@7470228130ab home]# ls
test.java  test2.txt
```

**容器间的数据共享**

**--volumes-from 容器名**

> **docker run -it -p 3306:3306 -v /my/custom:/etc/mysql/conf.d -v /my/own/datadir:/var/lib/mysql -e MARIADB_ROOT_PASSWORD=qiaojiyuan --name m1 mariadb**
>
> **docker run -it -p 3307:3306 -e MARIADB_ROOT_PASSWORD=qiaojiyuan --name m2 --volumes-from m1 mariadb**

## DockerFile

### 指令

* 每个关键字都必须大写
* 按照从上到下顺序执行
* #表示注释
* 每一条指令都会创建一个镜像层

```shell
FROM            # 基础镜像
MAINTAINER      # 维护人信息 姓名+邮箱
RUN             # 构建镜像需要运行的命令
ADD             # 添加需要的依赖，会自动解压
COPY            # 本地文件复制到镜像中
WORKDIR         # 镜像工作目录
VOULME          # 挂载目录
EXPOSE          # 保留端口配置
CMD             # 指定容器启动时，执行的命令，替换，只有最后一个会生效
ENTRYPOINT      # 指定容器启动时，执行的命令，追加
ONBUILD         # 当构建一个被继承的DockerFile时会运行
ENV             # 构建的时候设置环境变量
```
### 构建my-centos镜像
```dockerfile
FROM centos
MAINTAINER qjy<12345678@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim

EXPOSE 80

CMD echo $MYPATH
CMD echo "--------end-------"
CMD /bin/bash
```

**docker build [OPTIONS] PATH | URL | -**  

​        **-f 指定DockerFile文件**

​        **-t 指定构建镜像名:版本**

> 指令最后一个 **.** 是上下文路径，指 docker 在构建镜像，有时候想要使用到本机的文件（比如复制），docker build 命令得知这个路径后，会将路径下的所有内容打包。

```shell
# 构建镜像
$ docker build -f my-centos -t mycentos:0.0.1 .
Sending build context to Docker daemon  34.05MB
Step 1/9 : FROM centos
 ---> 0d120b6ccaa8
Step 2/9 : MAINTAINER qjy<12345678@qq.com>
 ---> Running in 95d4af89d3a8
Removing intermediate container 95d4af89d3a8
 ---> 0b4363bad428
Step 3/9 : ENV MYPATH /usr/local
 ---> Running in 4766aa646947
Removing intermediate container 4766aa646947
 ---> bf09600ec2c9
Step 4/9 : WORKDIR $MYPATH
 ---> Running in 478cae30c024
Removing intermediate container 478cae30c024
 ---> baabfda4d7ec
Step 5/9 : RUN yum -y install vim
 ---> Running in c71e90cf10f8
...
...
Complete!
Removing intermediate container c71e90cf10f8
 ---> 147a68ab83f6
Step 6/9 : EXPOSE 80
 ---> Running in f9ee9b8b506b
Removing intermediate container f9ee9b8b506b
 ---> dd3af8e75107
Step 7/9 : CMD echo $MYPATH
 ---> Running in 0a943157db62
Removing intermediate container 0a943157db62
 ---> 63c73bef7318
Step 8/9 : CMD echo "--------end-------"
 ---> Running in b03f6b514453
Removing intermediate container b03f6b514453
 ---> 67eb5c0cdf03
Step 9/9 : CMD /bin/bash
 ---> Running in 53858c9a4656
Removing intermediate container 53858c9a4656
 ---> efb257ea25e6
Successfully built efb257ea25e6
Successfully tagged mycentos:0.0.1

$ docker run -it --name m1 mycentos:0.0.1 
# 进入后的默认工作目录
[root@0c17b54954b3 local]# pwd
/usr/local
[root@0c17b54954b3 local]# 
```

**查看镜像构建历史**

```shell
$ docker history efb257ea25e6
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
efb257ea25e6        8 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "/bin…   0B                  
67eb5c0cdf03        8 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B                  
63c73bef7318        8 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B                  
dd3af8e75107        8 minutes ago       /bin/sh -c #(nop)  EXPOSE 80                    0B                  
147a68ab83f6        8 minutes ago       /bin/sh -c yum -y install vim                   58MB                
baabfda4d7ec        10 minutes ago      /bin/sh -c #(nop) WORKDIR /usr/local            0B                  
bf09600ec2c9        10 minutes ago      /bin/sh -c #(nop)  ENV MYPATH=/usr/local        0B                  
0b4363bad428        10 minutes ago      /bin/sh -c #(nop)  MAINTAINER qjy<12345678@q…   0B                  
0d120b6ccaa8        9 months ago        /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B                  
<missing>           9 months ago        /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B                  
<missing>           9 months ago        /bin/sh -c #(nop) ADD file:538afc0c5c964ce0d…   215MB      
```

### 构建包含项目的tomcat镜像

```dockerfile
FROM centos:7

ADD jdk-8u261-linux-x64.tar.gz /usr/local
ADD apache-tomcat-9.0.37.tar.gz /usr/local
COPY demo-0.0.1-SNAPSHOT.jar /usr/local

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_261
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.37
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.37
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_BASH/bin

EXPOSE 80 8080

ENTRYPOINT ["nohup","java","-jar","demo-0.0.1-SNAPSHOT.jar",">","demo.log","&"]
```



