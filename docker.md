# docker

## 1.0 docker 安装



1. 安装yum

```
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

2. 配置阿里镜像

```
yum-config-manager --add-repo   http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

3. 更新索引  可以让下载更快

```
[root@hadoop100 yum.repos.d]# yum makecache fast
```

4. 下载docker镜像

```
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

5. 启动docker

```
[root@hadoop100 yum.repos.d]# systemctl start docekr
```

6. 查看docker 的用户端和服务端

```
[root@hadoop100 /]# docker version
```

7. 配置docker加速器

   1. 打开阿里云服务器
   2. ![image-20230423122427145](C:\Users\34912\Desktop\docker_study\imgs\image-20230423122427145.png)

   ```
   
   ```

   

8. 配置镜像加速

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://nadw34aj.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```



	##  帮助启动命令

## 2.0docker 基本命令

##### 启动 、重启、停止，查看，docker

-  基本语法
  - systemctl [选项] docker
  - 选项说明
    - start                                                               启动
    - restart                                                            重启
    - stop                                                                停止
    - status                                                             查看状态
- 实际操作

```
[root@hadoop100 ~]# systemctl status docker
```

- 开机启动

 ```
[root@hadoop100 ~]# systemctl enable docker
 ```

- 查看指定名的使用

```
[root@hadoop100 ~]# docker images --help
```





##### 查看images容器

- 基本语法
  -  docker images [-qa]
  - -q                    -q, --quiet           Only show image IDs
  - -a                     -a, --all             Show all images (default hides intermediate images)

```
[root@hadoop100 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    feb5d9fea6a5   19 months ago   13.3kB
```

```
REPOSITORY             表示镜像仓库源
TAG                    镜像的标签版本号
IMAGE ID               镜像ID
CREATED                镜像创建的时间
SIZE                   镜像大小
```

同一个仓库可以有多个TAG版本



##### search 查看镜像

- 基本语法
  - docker search [容器名称]

- 实际操作

```
[root@hadoop100 ~]# docker search redis
NAME                     DESCRIPTION                                      STARS     OFFICIAL   AUTOMATED
redis                    Redis is an open source key-value store that…   12032     [OK] 
```

```
NAME                容器名称
DESCRIPTION         容器描述
STARS               点赞说明
OFFICIAL            是否官方
AUTOMATED           是否自动构建
```



##### pull 下载镜像

- 基本语法
  - docker pull   镜像名称[:TAG]         没有TAG就是最新版

实际操作

```
[root@hadoop100 ~]# docker pull redis
```



##### df 查看docker的使用情况

- 基本语法
  - docker system df                 查看docker的使用情况

```
[root@hadoop100 ~]# docker system df
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          2         1         112.7MB   112.7MB (99%)
Containers      2         0         0B        0B
Local Volumes   0         0         0B        0B
Build Cache     0         0         0B        0B
```

##### rmi 删除 镜像名

- 基本语法
  - docker rmi [-f] 镜像名称或者id
  - rmi                      i指的是image
  - -f                       -forced强制删除

```
[root@hadoop100 ~]# docker rmi -f hello-world
Untagged: hello-world:latest

```

- 全部删除
  - docker rmi ${docker images -qa}
- 删除多个
  - docker rmi [-f]  镜像名称1：镜像名称2



##### 面试题：谈谈什么是docker虚悬镜像

仓库名称：none   版本：none  没什么用建议直接删除









## 3.0docker容器操作

###### docker 运行容器

- 基本语法

  - docker run [OPTIONS] IMAGS [COMMANG] [AGR..]



使用镜像centos:latest以后台模式启动一个容器
docker run -d centos
问题:然后docker ps-a进行查看,会发现容器已经退出很重要的要说明的一点: Docker容器后台运行,就必须有一个前台进程容器运行的命令如果不是那些一直挂起的命 令(比如运行top，tail) ，就是会自动退出的。这个是docker的机制问题,比如你的web容器,我们以nginx为例，正常情况下，我们配置启动服务只需要启动响应的service即可。例如service nginx start但是,这样做,nginx为后台进程模式运行,就导致docker前台没有运行的应用，这样的容器后台启动后,会立即自杀因为他觉得他没事可做了。所以，最佳的解决方案是，将你要运行的程序以前台进程的形式运行，常见就是命令行模式，表示我还有交互操作，别中断，O(∩_∩)O哈哈~



-it 前台交互式容器                                  ：交互式窗口退出程序就直接退出



-d 后台守护式启动                                   ：后台自动挂起启动




> OPTIONS说明(常用)

OPTIONS说明(常用) :有些是一个减号，有些是两个减号

--name 才容器新名字" 为容器指定一个名称;

-d: 后台运行容器并返回容器ID，也即启动守护式容器(后台运行);

-i:  以交互模式运行容器，通常与-t同时使用;

-t: 为容器重新分配一个伪输入终端，通常与-i同时使用;  也即启动交互式容器(前台有伪终端，等待交互);

-P: 随机端口映射，大写P

-p: 指定端口映射，小写p



> IMAGS  说明

IMAGS 容器名称



> COMMAND 说明

COOMMAND   命令



> AGR                                          agreement协议

AGR按照什么协议来执行



- 实际案例

  - 启动Ubuntu   
    - -i:  以交互模式运行容器
    - -t: 为容器重新分配一个伪输入终端                                           TTY终端
    - /bin/bash    放在镜像后面的是命令，这里我们希望有一个交互式的shell，因此就是/bin/bash , 如果要退出终端就直接exit

```
[root@hadoop100 ~]# docker run -it ubuntu /bin/bash 
root@46ee76302e4b:/# ps -f
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 07:14 pts/0    00:00:00 /bin/bash

#去另一个终端查看
[root@hadoop100 /]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
1a45b6b70f83   ubuntu    "/bin/bash"   34 seconds ago   Up 33 seconds             vigorous_edison
```

- 给容器自定义名称

  ```
  [root@hadoop100 ~]# docker run -it --name myubuntu  ubuntu /bin/bash
  
  #自定义的名称
  CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
  42da8213188a   ubuntu    "/bin/bash"   13 seconds ago   Up 11 seconds             myubuntu
  
  ```



###### docker 退出容器

- 基本语法
  - exit                                                   run进去容器，exit退出，容器停止
  - CTRL +p+q                                       run进去容器，CTRL+p+q ，容器不停止



###### 启动已经停止的容器

docker start 容器id或者容器名称



###### 重启容器

docker restart 容器id或者容器名称



###### 停止容器

docker stop 容器id或者容器名称



###### 强制停止容器

docker kill 容器id或者容器名称



###### 删除容器	

docker rm 容器id或者容器名称



###### 查看容器日志

- 基本语法
  - docker logs 容器id或者容器名称

- 实际操作

```
[root@hadoop100 ~]# docker logs 122c64aefc62
1:C 25 Apr 2023 08:53:36.763 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
```

###### 查看容器内部细节

- 基本操作
  -  docker inspect 容器id或者容器名称

```
[root@hadoop100 ~]# docker inspect 122c64aefc62
```



###### ps 查看运行的容器

- 基本语法

  - docker ps [选项]

  ```
  [root@hadoop100 ~]# docker ps
  CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
  42da8213188a   ubuntu    "/bin/bash"   13 seconds ago   Up 11 seconds             myubuntu
  ```

  - 选项
    - -a :列出当前所有正在运行的容器+历史上运行过的
    - -l:显示最近创建的容器。
    - -n:显示最近n个创建的容器。 后面必须加数字
    - -q :静默模式，只显示容器编号。



###### 退出在进入容器  有redis的操作

- 方法一
  - 基本语法
    -  docker exec -it 容器id  /bin/bash
    - 使用exec是新开一个窗口exit不会关闭容器

```
#进入容器
[root@hadoop100 ~]# docker run -it ubuntu /bin/bash
root@a34388817f34:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
#退出容器   ctrl+p+q 退出
root@a34388817f34:/# [root@hadoop100 ~]# 
[root@hadoop100 ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                   CREATED          STATUS          PORTS      NAMES
a34388817f34   ubuntu    "/bin/bash"               30 seconds ago   Up 29 seconds         confident_driscoll
122c64aefc62   redis     "docker-entrypoint.s…"   42 minutes ago   Up 42 minutes   6379/tcp clever_engelbart
#在进入容器
[root@hadoop100 ~]# docker exec -it a34388817f34 /bin/bash
root@a34388817f34:/# 

# 进入redis
[root@hadoop100 ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                   CREATED          STATUS          PORTS      NAMES
122c64aefc62   redis     "docker-entrypoint.s…"   53 minutes ago   Up 53 minutes   6379/tcp clever_engelbart
[root@hadoop100 ~]# docker exec -it 122c64aefc62 /bin/bash
root@122c64aefc62:/data# redis-cli -p 6379
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> set key1 val1
OK
127.0.0.1:6379> get key1
"val1"
127.0.0.1:6379> 

```



- 方法二
  - 基本语法
    - docker attach 容器id                                                    attach   进入
    - 使用 attach exit退出会关闭容器

```
[root@hadoop100 ~]# docker attach a34388817f34
root@a34388817f34:/# 

```



###### 从容器里拷贝出文件到本机

- 基本语法
  - docker cp 容器id:容器路径文件  /目标路径文件夹

```
[root@hadoop100 ~]# docker cp a34388817f34:/home/text.txt /wmt
Successfully copied 2.05kB to /wmt
```



###### 导入导出容器

> export导出容器的内容留作为一个tar归档文件[对应import命令]

- 基本语法
  - docker export  容器id  >  导出的文件名.tar

```
[root@hadoop100 wmt]# docker export a34388817f34 > ubuntu.tar
[root@hadoop100 wmt]# ll
-rw-r--r--. 1 root root 75160576 4月  25 19:02 ubuntu.tar
```



> import从tar包中的内容创建--个新的文件系统再导入为镜像[对应export]

- 基本语法
  - cat 压缩后的镜像.tar | docker import -   /镜像名称:版本



```
[root@hadoop100 wmt]# cat ubuntu.tar | docker import - wmt/ubuntu:latest
sha256:1a9cf3669688ce31fbf4dd8f678b168c70ed0a05ca025740cbe16cf5735107cb

[root@hadoop100 wmt]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
wmt/ubuntu   latest    1a9cf3669688   27 seconds ago   72.8MB

#运行把镜像变成容器
[root@hadoop100 wmt]# docker run -it 1a9cf3669688 /bin/bash

```



## 4.0 docker镜像

> 什么是镜像

是一种轻量级、可执行的独立软件包，它包含运行某个软件所需的所有内容，我们把应用程序和配置依赖打包好形成一个可交付的
运行环境(包括代码、运行时需要的库、环境变量和配置文件等)，这个打包好的运行环境就是image镜像文件。只有通过这个镜像文件才能生成Docker容器实例(类似Java中new出来--个对象)。









> 分层的镜像

UnionFS (联合文件系统) : Union文件系统(UnionFS) 是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作
为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual
filesystem)。Union 文件系统是Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像(没有父镜像)，可以制作各种
具体的应用镜像。

特性:一次同时加载多个文件系统，但从外面看起来，只能看到一一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的
文件系统会包含所有底层的文件和目录







> Docker镜像加载原理:
>
> boot file system                          引导文件系统

docker的镜像实际上由**一层一层的文件系统组成**，这种层级的文件系统UnionFS。
bootfs(boot file system)主要包含bootloader和kernel, bootloader主要是引导加载kernel, Linux刚启动时会加载bootfs文件系统，在
Docker镜像的最底层是引导文件系统bootfs。 这-层与我们典型的Linux/Unix系统是-样的，包含boot加载器和内核。当boot加载完
成之后整个内核就都在内存中了，此时内存的使用权己由bootfs转交给内核，**此时系统也会卸载bootfs。**
**rootfs (root file system)，在bootfs之 上**。**包含的就是典型Linux系统**中的/dev, /proc, /bin, /etc等标准目录和文件。rootfs就是 各种
不同的操作系统发行版，比如buntu, Cehtos等 等。



对于一个精简的OS，rootfs可以很小，只需要包括最基本的命令、工具和程序库就可以了，因为底层直接用Host的kernel, 自己只
需要提供rootfs就行了。由此可见对于不同的linux发行版,bootfs基本是一致的,rootfs会有差别,因此不同的发行版可以公用
bootfs。



> 为什么要镜像分层

镜像分层最大的一个好处就是共享资源，方便复制迁移，**就是为了复用**。
比如说有多个镜像都从相同的base镜像构建而来，那么Docker Host只需在磁盘上保存一份base镜像;
同时内存中也只需加载一份base镜像，就可以为所有容器服务了。而且镜像的每一层都可以被共享。





> 什么是镜像和容器

docker 镜像层都是只读的，容器层是可写的，当容器启动时，一个新的可写容器被加载到镜像的顶部，这一层通常被称为**容器层**，**容器层之下被称为镜像层**







###### commit 提交

- 准备有vim的Ubuntu

    - 运行Ubuntu

  ```
  docker run -it ubuntu的id
  ```

  - 更新数据列表

  ```
  apt-get update
  ```

  - 下载vim

  ```
  apt-get -y install vim
  ```



- commit操作
  - 基本语法
    - docker commit -m="提交描述信息" -a="作者"  版本包/版本名称:版本号

```
[root@hadoop100 ~]# docker commit  -m="add vim" -a="wmt" fbcd00b264f3 wmt/myubuntu:laste
sha256:ac417ee61b6f023418be30f2de6eaf75d049cb5b34ac3b457c502d83d55b8cb9


[root@hadoop100 ~]# docker images
REPOSITORY     TAG       IMAGE ID       CREATED          SIZE
wmt/myubuntu   laste     ac417ee61b6f   17 minutes ago   183MB

```

 

#### 上传下载阿里云



###### 创建镜像仓库

1. 登录阿里云

2. 容器镜像服务

3. 实列列表

4. 镜像仓库

5. 基本信息

   

   如图

   ![image-20230426111252942](..\docker_study\imgs\image-20230426111252942.png)



点击myubuntu

![image-20230426111457893](..\docker_study\imgs\image-20230426111457893.png)



###### 将镜像推送到阿里云



###### 1. 登录阿里云Docker Registry

```
$ docker login --username=wmt20010820 registry.cn-hangzhou.aliyuncs.com
```

用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

您可以在访问凭证页面修改凭证密码。

###### 2. 从Registry中拉取镜像

```
$ docker pull registry.cn-hangzhou.aliyuncs.com/2849569455/myubuntu:[镜像版本号]
```

###### 3. 将镜像推送到Registry

```
$ docker login --username=wmt20010820 registry.cn-hangzhou.aliyuncs.com
$ docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/2849569455/myubuntu:[镜像版本号]
$ docker push registry.cn-hangzhou.aliyuncs.com/2849569455/myubuntu:[镜像版本号]
```

请根据实际镜像信息替换示例中的[ImageId]和[镜像版本号]参数。

###### 4. 选择合适的镜像仓库地址

从ECS推送镜像时，可以选择使用镜像仓库内网地址。推送速度将得到提升并且将不会损耗您的公网流量。

如果您使用的机器位于VPC网络，请使用 registry-vpc.cn-hangzhou.aliyuncs.com 作为Registry的域名登录。

###### 5. 示例

使用"docker tag"命令重命名镜像，并将它通过专有网络地址推送至Registry。

```
$ docker images
REPOSITORY                                    TAG                IMAGE ID     CREATED           VIRTUAL SIZE
registry.aliyuncs.com/acs/agent         0.7-dfb6816         37bb9c63c8b2        7 days ago          37.89 MB
$ docker tag 37bb9c63c8b2 registry-vpc.cn-hangzhou.aliyuncs.com/acs/agent:0.7-dfb6816
```

使用 "docker push" 命令将该镜像推送至远程。

```
$ docker push registry-vpc.cn-hangzhou.aliyuncs.com/acs/agent:0.7-dfb6816
```



实际操作

```
#上传镜像
[root@hadoop100 ~]# docker login --username=wmt20010820 registry.cn-hangzhou.aliyuncs.com
[root@hadoop100 ~]# docker tag ac417ee61b6f registry.cn-hangzhou.aliyuncs.com/2849569455/myubuntu:latest
[root@hadoop100 ~]# docker push registry.cn-hangzhou.aliyuncs.com/2849569455/myubuntu:latest

#下载镜像
[root@hadoop100 ~]# docker pull registry.cn-hangzhou.aliyuncs.com/2849569455/myubuntu:latest
```





### 上传下载私有库

- 下载registry镜像

```
[root@hadoop100 ~]# docker pull registry
```



- 运行registry镜像

```
docker run -d -p 5000:5000 -v /wmt/mryegis/:/tmp/registry --privileged=true registry

CONTAINER ID   IMAGE      COMMAND                   CREATED         STATUS         PORTS              NAMES
fc3907409a4a   registry   "/entrypoint.sh /etc…"   3 minutes ago   Up 3 minutes   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   goofy_margulis

```



- 配置Ubuntu的ifconfig

```
root@9aa6a47e4d24:/# apt-get update
root@9aa6a47e4d24:/# apt-get install net-tools
```



- 上传到私有库

```
[root@hadoop100 ~]# docker commit -m="add ifconfig" -a="wmt" 9aa6a47e4d24 wmt:1.2

[root@hadoop100 ~]# docker images
REPOSITORY                                              TAG       IMAGE ID       CREATED         SIZE
wmt                                                     1.2       ba5ecfba537c   5 seconds ago   116MB
```

