

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

##### 查看版本

- grep -i  不区分大小写

```
[root@hadoop100 ~]# docker image inspect 7614ae9453d1 | grep -i version
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

-p: 指定端口映射，小写p                 -p [宿主机端口]:[容器端口]



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
  - docker run -it Ubuntu /bin/bash

```
root@9aa6a47e4d24:/# apt-get update
root@9aa6a47e4d24:/# apt-get install net-tools
```



- 上传到私有库

```
[root@hadoop100 ~]# docker commit -m="add ifconfig" -a="wmt" 9aa6a47e4d24 ubuntu_ifconfig :1.2

[root@hadoop100 ~]# docker images
REPOSITORY                                              TAG       IMAGE ID       CREATED         SIZE
ubuntu_ifconfig                                         1.2       ba5ecfba537c   5 seconds ago   116MB
```



- 使用curl验证私服库有什么镜像

```
[root@hadoop100 ~]# curl -XGET http://192.168.206.100:5000/v2/_catalog
{"repositories":[]}
```



- 上传私有库
  - 基本语法                 tag版本描述
    -  docker tag 需要上传镜像名称:tag host：port/REPOSITORY:TAG

```
[root@hadoop100 ~]# docker images
REPOSITORY                                              TAG       IMAGE ID       CREATED         SIZE
ubuntu_ifconfig                                         1.2       849476941c81   10 seconds ago   117MB


[root@hadoop100 ~]# docker tag ubuntu_ifconfig:1.2 192.168.206.100:5000/myubuntu_ifconfig:1.2


[root@hadoop100 ~]# docker images
REPOSITORY                                              TAG       IMAGE ID       CREATED         SIZE
192.168.206.100:5000/myubuntu_ifconfig                  1.2       849476941c81   2 minutes ago   117MB



```

- docker 默认不允许HTTP方式推送镜像	
  -  vim /etc/docker/daemon.json 
  - json格式要加,

```

{
  "registry-mirrors": ["https://nadw34aj.mirror.aliyuncs.com"],

  "insecure-registries":["192.168.206.100:5000"]
}

```

修改如果不生效====》重启docker



- push到私有库

```
[root@hadoop100 ~]# docker tag ubuntu_ifconfig:1.2 192.168.206.100:5000/myubuntu_ifconfig:1.2

[root@hadoop100 ~]# docker push 192.168.206.100:5000/myubuntu_ifconfig:1.2
```



- pull 到本地库

```
[root@hadoop100 ~]# docker pull 192.168.206.100:5000/myubuntu_ifconfig:1.2
```



## 5.0 docker 容器数据卷

Docker挂载主机目录访问如果出现cannot open directory .: Permission denied

解决办法:在挂载目录后多加一个--privileged=true参数即可

如果是CentOS7安全模块会比之前系统版本加强，不安全的会先禁止，所以目录挂载的情况被默认为不安全的行为, 在SELinux里面挂载目录被禁止掉了额，如果要开启，我们一-般使用--privileged=true命令，扩大容器的权限解决挂载目录没有权限的问题，也即使用该参数, container内 的root拥有真正的root权限，否则，container内 的root只是外部的一一个 普通用户权限。



> 什么是容器数据卷

将docker容器内的数据保存进宿主机的磁盘中  类似于我们redis中的rdb和aof文件



>  容器卷的特点

1:数据卷可在容器之间共享或重用数据

2:卷中的更改可以直接实时生效，爽

3:数据卷中的更改不会包含在镜像的更新中

4:数据卷的生命周期直持续到没有容器使用它为止



- 基础语法
  - docker run -it -v /宿主机的绝对路径目录:/容器目录   镜像名
  - 宿主机的绝对路径目录 和 容器目录里面的数据像vue的双向绑定
- 实际操作
  - 宿主机的绝对路径目录  如果不存在会自动创建

```
[root@hadoop100 ~]# docker run -it --name u1 -v /tmp/host_data:/tmp/docker_data ubuntu
```



##### 查看是否挂载成功

- 基本语法

  - docker inspect  容器id

  ```
  [root@hadoop100 host_data]# docker inspect 3480f68fc277
  
   "Mounts": [
              {
                  "Type": "bind",
                  "Source": "/tmp/host_data",
                  "Destination": "/tmp/docker_data",
                  "Mode": "",
                  "RW": true,
                  "Propagation": "rprivate"
              }
  
  ```

  

##### 限制容器权限

- 基本语法
  - /容器目录:ro  镜像名                                   只能读不能写                       ro = read only
- 实际操作

```
[root@hadoop100 host_data]# docker run -it -v /tmp/host_data:/tmp/docker_data:ro --name ui ba6acccedd29

[root@hadoop100 host_data]# docker inspect ca53d270f9eb

 "Mounts": [
            {
                "Type": "bind",
                "Source": "/tmp/host_data",
                "Destination": "/tmp/docker_data",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            }

```



##### 卷的继承和共享

- 基本语法
  - docker run -it --name u2 --volumes-from 继承的容器名称  ubuntu
- 实际操作

```
[root@hadoop100 /]# docker run -it --name u2 --volumes-from u1  ubuntu

[root@hadoop100 host_data]# docker inspect d4738917c445

 "VolumeDriver": "",
            "VolumesFrom": [
                "u1"
            ],

```

> docker 中的继承不会因为父类的死亡而改变子类的使用，并不会影响 其余跟java中继承差不多 **这个继承更像是继承的是物理地址**



## 6.0 安装镜像



[ docker.hub 官网] https://hub.docker.com/



##### 安装tomcat

1. 先去官网上去search   或者直接在Linux里search
2. 直接pull

    ```
docker pull tomcat
    ```

3. run

   ```
   [root@hadoop100 host_data]# docker run -d -p 8080:8080 --name tomcat tomcat
   ```

4. 测试



![image-20230427161033075](..\docker_study\imgs\image-20230427161033075.png)

>  原因：

​	因为tomcat的最新版本webapps里面是空的



- 进入tomcat

```
[root@hadoop100 host_data]# docker exec -it 21b1b27910ac /bin/bash
```



- 查看tomcat目录 中webapps

```
root@21b1b27910ac:/usr/local/tomcat# ls -l
total 132
drwxr-xr-x. 2 root root     6 Dec 22  2021 webapps
drwxr-xr-x. 7 root root    81 Dec  2  2021 webapps.dist
#查看webapps
root@21b1b27910ac:/usr/local/tomcat# cd webapps
root@21b1b27910ac:/usr/local/tomcat/webapps# ls -a
.  ..

#删除webapps
root@21b1b27910ac:/usr/local/tomcat# rm -rf webapps
#重署名
root@21b1b27910ac:/usr/local/tomcat# mv webapps.dist webapps
#重启tomcat
docker restart tomcat
```

> 运行结果：

![image-20230428093156992](..\docker_study\imgs\image-20230428093156992.png)



- 不需要配置的tomcat

```
[root@hadoop100 ~]# docker run -d -p 8080:8080 --name mytomcat8 billygoo/tomcat8-jdk8
```



##### 安装MySQL

###### 简单安装MySQL

- 查看当前Linux中有没有启动的MySQL

   -e     Select all processes.  Identical to -A.

  -f     Do full-format listing. This option can be combined with many other UNIX-style options to add additional columns.  It also causes the command arguments to be printed.

```
[root@hadoop100 ~]# ps -ef | grep mysql
root      11031   9057  0 10:05 pts/0    00:00:00 grep --color=auto mysql
```



- 创建容器
  - 语法来自dockr官网[mysql - Official Image | Docker Hub](https://hub.docker.com/_/mysql)

```
[root@hadoop100 ~]# docker run --name mysql5.7 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
ef408c6c63c67aee2efe9fd85f452eebee2aa97fb62b0374c9f52f19b25b0df4

```



- 进入mysq

  ```
  [root@hadoop100 ~]# docker exec -it 42e72bfdd503 /bin/bash
  root@42e72bfdd503:/# mysql -uroot -p123456
  ```

- 创建数据库

  ```
  #展示数据库
  mysql> show databases;
  +--------------------+
  | Database           |
  +--------------------+
  | information_schema |
  | mysql              |
  | performance_schema |
  | sys                |
  +--------------------+
  4 rows in set (0.00 sec)
  #创建数据库
  mysql> create database db01;
  Query OK, 1 row affected (0.01 sec)
  
  mysql> show databases;
  +--------------------+
  | Database           |
  +--------------------+
  | information_schema |
  | db01               |
  | mysql              |
  | performance_schema |
  | sys                |
  +--------------------+
  5 rows in set (0.00 sec)
  #进入数据库
  mysql> use db01
  Database changed
  #创建表
  mysql> create table t1(id int ,name varchar(20));
  Query OK, 0 rows affected (0.00 sec)
  #插入
  mysql> insert into t1 values(1,"wmt");
  Query OK, 1 row affected (0.00 sec)
  #查找
  mysql> select * from t1 where id=1;
  +------+------+
  | id   | name |
  +------+------+
  |    1 | wmt  |
  +------+------+
  1 row in set (0.00 sec)
  
  
  ```

- 中文乱码问题

  - 查看字符编码  latin1

  ```
  mysql> SHOW VARIABLES LIKE 'character%' ;
  +--------------------------+----------------------------+
  | Variable_name            | Value                      |
  +--------------------------+----------------------------+
  | character_set_client     | latin1                     |
  | character_set_connection | latin1                     |
  | character_set_database   | latin1                     |
  | character_set_filesystem | binary                     |
  | character_set_results    | latin1                     |
  | character_set_server     | latin1                     |
  | character_set_system     | utf8                       |
  | character_sets_dir       | /usr/share/mysql/charsets/ |
  
  ```

###### 实战安装MySQL

> 解决中文乱码问题
>
> 解决数据丢失问题



- 创建容器

```
[root@hadoop100 ~]# docker run -d -p 3306:3306 --name mysql5.7 --privileged=true\
-v /docker/mysql/log:/var/log/mysql \
-v /docker/mysql/data:/var/lib/mysql \
-v /docker/mysql/conf:/etc/mysql/conf.d \
-e MYSQL_ROOT_PASSWORD=123456 mysql:5.7\

docker run -d -p 3306:3306 --name mysql5.7 --privileged=true -v /docker/mysql/log:/var/log/mysql -v /docker/mysql/data:/var/lib/mysql -v /docker/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7

```



- 配置文件防止乱码 my.cnf

```
[root@hadoop100 conf]# pwd
/docker/mysql/conf
[root@hadoop100 conf]# vim my.cnf
[root@hadoop100 conf]# cat my.cnf 

[mysqld]
collation-server = utf8_general_ci
#服务端使用的字符集默认为utf-8
character-set-server=utf8
[client]
#客户端使用的字符集默认为utf8
default-character-set=utf8

# 记住要重启MySQL容器
#一定要在客户端运行字符编码检测
#删除容器只要容器卷还在数据会自动恢复
mysql> SHOW VARIABLES LIKE 'character%' ;
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.01 sec)

```





#####  安装redis

- 安装最新版 

  - docker pull redis

    ```
    [root@hadoop100 ~]# docker pull redis
    
    #查看最新版的具体版本 6.2.6
    
    [root@hadoop100 ~]# docker image inspect 7614ae9453d1 | grep -i version
                    "GOSU_VERSION=1.12",
                    "REDIS_VERSION=6.2.6",
            "DockerVersion": "20.10.7",
                    "GOSU_VERSION=1.12",
                    "REDIS_VERSION=6.2.6",
    
    ```



###### 下载redis配置文件



​    -   根据版本去官网下配置文件  https://raw.githubusercontent.com/redis/redis/6.2/redis.conf

​        把配置文件放在redis.conf文件中通过Xftp工具上传到Linux系统文件中通过容器卷和redis绑定

​		

``` 
[root@hadoop100 redis]# mkdir /docker/redis
#Xftp上传到redis中
[root@hadoop100 redis]# ls
redis.conf
[root@hadoop100 redis]# pwd
/docker/redis
```



###### 修改配置文件

```
注掉 允许外部访问
#bind 127.0.0.1 -::1

注释  或者no   因为配置会和docker run -d参数起冲突
daemonize no

数据持久化开启
appendonly yes

连接安全问题
protected-mode no  

```



###### 创建容器

```
[root@hadoop100 redis]# docker run -p 36379:6379 --name myredis --privileged=true -v /docker/redis/redis.conf:/etc/redis/redis.conf -v /docker/redis/data:/data -d redis redis-server /etc/redis/redis.conf

```

```
[root@hadoop100 redis]# docker run -p 36379:6379 --name myredis6.2.6 --privileged=true 
-v /docker/redis/redis.conf:/etc/redis/redis.conf 
-v /docker/redis/data:/data 
-d 
#镜像名称
redis  
#启动方式
redis-server /etc/redis/redis.conf

```



##### 测试配置文件是否生效

- 配置文件

```
# Set the number of databases. The default database is DB 0, you can select
# a different one on a per-connection basis using SELECT <dbid> where
# dbid is a number between 0 and 'databases'-1
databases 15

```

- 测试

```
[root@hadoop100 redis]# docker exec -it 8a652e3ad7d4 /bin/bash
root@8a652e3ad7d4:/data# redis-cli  
127.0.0.1:6379> select 15
OK
```



- 修改配置文件

```
  # Set the number of databases. The default database is DB 0, you can select
  # a different one on a per-connection basis using SELECT <dbid> where
  # dbid is a number between 0 and 'databases'-1
  databases 10
```

  

- 测试

```
[root@hadoop100 redis]# docker exec -it 8a652e3ad7d4 /bin/bash
root@8a652e3ad7d4:/data# ll
bash: ll: command not found
root@8a652e3ad7d4:/data# redis-cli
127.0.0.1:6379> select 15
(error) ERR DB index is out of range

```



##### 配置密码

```
[root@hadoop100 redis]# docker exec -it myredis6.2.6 /bin/bash
root@8a652e3ad7d4:/data# redis-cli
127.0.0.1:6379> config set requirepass 123456
OK
#登录
127.0.0.1:6379> ping
(error) NOAUTH Authentication required.
127.0.0.1:6379> auth 123456
OK

```





## docker高级篇

> 主从复制

![image-20230428210819372](..\docker_study\imgs\image-20230428210819372.png)



1. 同一MySQL版本
2. 同一网段 可以互相ping通
3. 配置主机MySQL中的my.ini 主 配置    
   1. server-id=1                   主服务器唯一ID
   
   2. log-bin=C:/ProgramData/MySQL/MySQL Server 5.7/Data/mysqlbin         启动二进制日志             本地MySQL路径/mysqlbin
   
   3. log-err=C:/ProgramData/MySQL/MySQL Server 5.7/Data/mysqlerr          启动二进制日志             本地MySQL路径/mysqlerr
   
   4. basedir=C:/ProgramData/MySQL\MySQL Server 5.7                                    basedir=自己本地路径                          [可选] 根目录
   
   5.  binlog-ignore=                                                                                                      设置不需要复制的数据库
   
   6. binlog-do-db=需要复制数据库的名字
   
      
   
      

4. 配置从机 my.cnf
   1. server-id=2                                                           不能为一
   2. log-bin=mysql-bin







docker中主MySQL配置 my.cnf

```


[mysqld]
collation-server = utf8_general_ci
#服务端使用的字符集默认为utf-8
character-set-server=utf8
#局域网唯一id
#设置server_id,同一局域网中需要唯一
server_id=101
#指定不需要同步的数据库名称
binlog-ignore-db=mysql
#开启二进制日志功能
log-bin=mall-mysql-bin
#设置二进制日志使用内存大小(事务)
binlog_cache_size=1M
#设置使用的二进制日志格式( mixed, statement, row)
binlog_format=mixed
#二进制日志过期清理时间。默认值为0,表示不自动清理。
expire_logs_days=7
#跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
#如: 1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062
[client]
#客户端使用的字符集默认为utf8
default-character-set=utf8                                
```



重新启动服务器



查看能否登录成功

```
root@5875c3c977f6:/# mysql -uroot -p123456
```

创建数据同步用户

```
create user 'wmt_slave'@'%' identified by '123456';
grant replication slave,replication client on *.* to 'wmt_slave'@'%';

CREATE USER 'wmt'@'%' IDENTIFIED BY '123456';
GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'wmt'@'%';
# 查看用户
select user,host from mysql.user; 
+---------------+-----------+
| user          | host      |
+---------------+-----------+
| root          | %         |
| wmt           | %         |
| mysql.session | localhost |
| mysql.sys     | localhost |
| root          | localhost |
+---------------+-----------+

```

创建从数据库

```
[root@hadoop100 conf]# docker run -d -p 3307:3306 --name mysql5.7_copy --privileged=true -v     /docker/mysql/log_copy:/var/log/mysql -v /docker/mysql/data_copy:/var/lib/mysql -v /docker/mysql/conf_copy:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7



docker run -d -p 3307:3306 --name mysql5.7_copy --privileged=true 
-v /docker/mysql/log_copy:/var/log/mysql 
-v /docker/mysql/data_copy:/var/lib/mysql 
-v /docker/mysql/conf_copy:/etc/mysql/conf.d 
-e MYSQL_ROOT_PASSWORD=123456 mysql:5.7

```

配置从机数据库

```


[mysqld]
collation-server = utf8_general_ci
#服务端使用的字符集默认为utf-8
character-set-server=utf8
#局域网唯一id
#设置server_id,同一局域网中需要唯一
server_id=102
#指定不需要同步的数据库名称
binlog-ignore-db=mysql
#开启二进制日志功能，以备Slave作为其它数据库实例的Master时使用.
log-bin=mall-mysql-slave1-bin
#设置二进制日志使用内存大小(事务)
binlog_cache_size=1M
#设置使用的二进制日志格式( mixed, statement, row)
binlog_format=mixed
##二进制日志过期清理时间。默认值为0，表示不自动清理。
expire_logs_days=7
#跳过主从复制中遇到的所有错误或指定类型的错误,避免slave端复制中断。
#如: 1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062
# relay_log配置中继日志
relay_log=mall-mysql-relay-bin
# log_slave_updates表示slave将复制事件写进自己的二进制日志
log_slave_updates=1
# slave设置为只读(具有super权限的用户除外)
read_only=1
[client]
#客户端使用的字符集默认为utf8
default-character-set=utf8   
```

重新启动服务

```
[root@hadoop100 conf_copy]# docker restart 9e7669e3338b

```



主机查看主从复制的状态

```
mysql> show master status;

+-----------------------+----------+--------------+------------------+-------------------+
| File                  | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+-----------------------+----------+--------------+------------------+-------------------+
| mall-mysql-bin.000001 |      621 |              | mysql            |                   |
+-----------------------+----------+--------------+------------------+-------------------+
1 row in set (0.01 sec)

```



进入从机

```
[root@hadoop100 conf_copy]# docker exec -it 9e7669e3338b /bin/bash
root@9e7669e3338b:/# mysql -uroot -p123456

```



从机申请连接

```
change master to master_host='192.168.206.100', master_user='wmt_slave', master_password='1234567', master_port=3306, master_log_file='mall-mysql-bin.000002', master_log_pos=334, master_connect_retry=30;

mysql> change master to master_host='192.168.206.100', master_user='wmt', master_password='123456', master_port=3306, master_log_file='mall-mysql-bin.000001', master_log_pos=621, master_connect_retry=30;
Query OK, 0 rows affected, 2 warnings (0.02 sec)

```





查看从机信息    /G   会以k v  键值对展示

```
mysql> show slave status /G;
            Slave_IO_Running: No             IO还没开始
            Slave_SQL_Running: No            MySQL还没开始

```



启动从数据库接收同步

```
# 停止
mysql> stop slave ;
mysql> start slave ;
Query OK, 0 rows affected (0.03 sec)

mysql> show slave status /G;
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes

```





####  搭建MySQL主从复制

>  主MySQL

- 创建文件

```
mkdir /mysql/mysql_master/conf

touch my.cnf

```

- 编写配置文件 

```


[mysqld]
collation-server = utf8_general_ci
#服务端使用的字符集默认为utf-8
character-set-server=utf8
#局域网唯一id
#设置server_id,同一局域网中需要唯一
server_id=101
#指定不需要同步的数据库名称
binlog-ignore-db=mysql
#开启二进制日志功能
log-bin=mall-mysql-bin
#设置二进制日志使用内存大小(事务)
binlog_cache_size=1M
#设置使用的二进制日志格式( mixed, statement, row)
binlog_format=mixed
#二进制日志过期清理时间。默认值为0,表示不自动清理。
expire_logs_days=7
#跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
#如: 1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062
[client]
#客户端使用的字符集默认为utf8
default-character-set=utf8    
```

- 创建主机容器

```
[root@hadoop100 conf]# docker run -d -p 3307:3306 --name mysql_master --privileged=true -v /mysql/mysql_master/conf:/etc/mysql/conf.d -v /mysql/mysql_master/log:/var/log/mysql -v /mysql/mysql_master/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
c73a5dc82ae2d96a9243fbb3a3813de1f1d71866e220cc05fa4f6be4d3b53f6f
[root@hadoop100 conf]# docker ps
CONTAINER ID   IMAGE                   COMMAND                   CREATED         STATUS         PORTS                                                  NAMES
c73a5dc82ae2   mysql:5.7               "docker-entrypoint.s…"   6 seconds ago   Up 5 seconds   33060/tcp, 0.0.0.0:3307->3306/tcp, :::3307->3306/tcp   mysql_master

```

- 进入主MySQL

```
[root@hadoop100 mysql]# docker exec -it c73a5dc82ae2 /bin/bash
root@c73a5dc82ae2:/# mysql -uroot -p
Enter password: 

```



- 创建数据同步用户

```
#创建用户
mysql> create user 'wmt_slave'@'%' identified by '123456';
Query OK, 0 rows affected (0.01 sec)
#给于条件
mysql> grant replication slave,replication client on *.* to 'wmt_slave'@'%';
Query OK, 0 rows affected (0.00 sec)
#查看用户
mysql> select user,host from mysql.user; 
+---------------+-----------+
| user          | host      |
+---------------+-----------+
| root          | %         |
| wmt_slave     | %         |
| mysql.session | localhost |
| mysql.sys     | localhost |
| root          | localhost |
+---------------+-----------+
5 rows in set (0.00 sec)

```



- 查看主机状态             

```
mysql> show master status ;
+-----------------------+----------+--------------+------------------+-------------------+
| File                  | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+-----------------------+----------+--------------+------------------+-------------------+
| mall-mysql-bin.000003 |      625 |              | mysql            |                   |
+-----------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)

```

> 从机会使用到上面数据



> 从MySQL

- 创建目录

```
mkdir /mysql/mysql_slave/conf
[root@hadoop100 conf]# pwd
/mysql/mysql_slave/conf
#创建从机配置文件
[root@hadoop100 conf]# touch my.cnf
[root@hadoop100 conf]# vim my.cnf

```

 

- 编写从机配置文件

```


[mysqld]
collation-server = utf8_general_ci
#服务端使用的字符集默认为utf-8
character-set-server=utf8
#局域网唯一id
#设置server_id,同一局域网中需要唯一
server_id=102
#指定不需要同步的数据库名称
binlog-ignore-db=mysql
#开启二进制日志功能，以备Slave作为其它数据库实例的Master时使用.
log-bin=mall-mysql-slave1-bin
#设置二进制日志使用内存大小(事务)
binlog_cache_size=1M
#设置使用的二进制日志格式( mixed, statement, row)
binlog_format=mixed
##二进制日志过期清理时间。默认值为0，表示不自动清理。
expire_logs_days=7
#跳过主从复制中遇到的所有错误或指定类型的错误,避免slave端复制中断。
#如: 1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062
# relay_log配置中继日志
relay_log=mall-mysql-relay-bin
# log_slave_updates表示slave将复制事件写进自己的二进制日志
log_slave_updates=1
# slave设置为只读(具有super权限的用户除外)
read_only=1
[client]
#客户端使用的字符集默认为utf8
default-character-set=utf8   
```



- 创建从机容器

```
[root@hadoop100 mysql]# docker run -d -p 3308:3306 --name mysql_slave --privileged=true -v /mysql/mysql_slave/conf:/etc/mysql/conf.d -v /mysql/mysql_slave/log:/var/log/mysql -v /mysql/mysql_slave/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
4c49fb59a09bd6dc592813d3e66372e3f06b8a10e541865f4c403631cd5fd3ac
[root@hadoop100 mysql]# docker ps
CONTAINER ID   IMAGE                   COMMAND                   CREATED          STATUS          PORTS                                                  NAMES
4c49fb59a09b   mysql:5.7               "docker-entrypoint.s…"   3 seconds ago    Up 2 seconds    33060/tcp, 0.0.0.0:3308->3306/tcp, :::3308->3306/tcp   mysql_slave

```

- 进入从机MySQL

```
[root@hadoop100 mysql]# docker exec -it 4c49fb59a09b /bin/bash
root@4c49fb59a09b:/# mysql -uroot -p

```



- 从机申请连接

```
mysql> change master to master_host='192.168.206.100', master_user='wmt_slave', master_password='123456', master_port=3307, master_log_file='mall-mysql-bin.000003', master_log_pos=625, master_connect_retry=30;
Query OK, 0 rows affected, 2 warnings (0.03 sec)

change master to 
master_host='192.168.206.100', 
master_user='wmt_slave', master_password='123456', 
master_port=3306, 
master_log_file='mall-mysql-bin.000003', 
master_log_pos=625, 
master_connect_retry=30;
```

> master_host：主数据库的IP地址；
> master_port：主数据库的运行端口；
> master_user：在主数据库创建的用于同步数据的用户账号；
> master_password：在主数据库创建的用于同步数据的用户密码；
> master_log_file：指定从数据库要复制数据的日志文件，通过查看主数据的状态，获取File参数；
> master_log_pos：指定从数据库从哪个位置开始复制数据，通过查看主数据的状态，获取Position参数；
> master_connect_retry：连接失败重试的时间间隔，单位为秒。





- 查看从机配置                show slave status \G;       \G以 k v键值对展示

  ```
  mysql> show slave status \G;
  *************************** 1. row ***************************
                 Slave_IO_State: 
                    Master_Host: 192.168.206.100
                    Master_User: wmt_slave
                    Master_Port: 3306
                  Connect_Retry: 30
                Master_Log_File: mall-mysql-bin.000003
            Read_Master_Log_Pos: 625
                 Relay_Log_File: mall-mysql-relay-bin.000001
                  Relay_Log_Pos: 4
          Relay_Master_Log_File: mall-mysql-bin.000003
               Slave_IO_Running: No        
              Slave_SQL_Running: No
  
  ```

  >             Slave_IO_Running: No     同步状态    
  >             Slave_SQL_Running: No    同步状态



- 开启从MySQL                       stop slave 停止同步

```
mysql> start slave;
Query OK, 0 rows affected (0.00 sec)

mysql> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.206.100
                  Master_User: wmt_slave
                  Master_Port: 3307
                Connect_Retry: 30
              Master_Log_File: mall-mysql-bin.000003
          Read_Master_Log_Pos: 625
               Relay_Log_File: mall-mysql-relay-bin.000002
                Relay_Log_Pos: 325
        Relay_Master_Log_File: mall-mysql-bin.000003
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes

```

>             Slave_IO_Running: Yes
>             Slave_SQL_Running: Yes



自己去测试



#### 分布式存储之 哈希取余算法



面试题： 如果有2亿条数据需要进行缓存，请问如何设计这个存储案例

单机肯定不可能实现，

2亿条记录就是2亿个k,v， 我们单机不行必须要分布式多机，假设有3台机器构成一个集群，用户每次读写操作都是根据公式:
hash(key) % N个机器台数，计算出哈希值，用来决定数据映射到哪-一个节点上。

优点：

简单方便，快捷只需要估计好数据规划好节点，列如3，6，9，就能保证一段时间的数据支持，使用hash算法固定要一部分请求落到同一台服务器上面，这样每一台服务器固定处理这些请求，起到负载均衡+分而处理的作用。



缺点：

如果其中一台服务挂了，或者进行扩容和缩容就比较麻烦了每次数据变动导致节点变动，映射关系需要重新计算，在服务器个数固定不变的时候没有问题，当服务器个数改变时候取模公式会发生变化hash(key)3会变成hash(Key)?。如果地址经过redis机器宕机，由于台数变化，会导致hash取余数据重新洗牌。



#### 分布式存储之 一致性哈希算法

>  一致性Hash算法背景

一致性哈希算法在1997年由麻省理工学院中提出的，设计目标是为了解决
分布式缓存数据变动和映射问题，某个机器单机了，分母数量改变了，自然取余数不0K了。



> 能干嘛

提出一致性Hash解决方案。目的是当服务器个数发生变动时，尽量减少影响客户端到服务器的映射关系。





> 算法构建一致性哈希环

一致性哈希环 
一致性哈希算法必然有个hash函数并按照算法产生hash值，这个算法的所有可能哈希值会构成一个全量集， **这个集合可以成为一个hash空间.[0,2^32-1]**， 这个是一个线性空间，但是在算法中，我们通过适当的逻辑控制将它首尾相连(0 = 2^32)，这样让它逻辑上形成了一个环形空间。

它也是按照使用取模的方法，前面笔记介绍的节点取模法是对节点(服务器)的数量进行取模。而一致性Hash算法是对2^32取模，简单来说，**一致性Hash算法将整个哈希值空间组织成一个虚拟的圆环，**如假设某哈希函数H的值空间为0-2^32-1 ( 即哈希值是一 个32位无符号整形)，整个哈希环如下图:整个空间按顺时针方向组织，圆环的正上方的点代表0，0点右侧的第一一个点代表1，以此类推，2、3、4、....直到2^32-1， 也就是说0点左
侧的第一 个点代表2^32-1，0和2^32-1在零 点中方向重合，我们把这个由2^32个点组成的圆环称为Hash环。



<img src="..\docker-study\imgs\image-20230520112636768.png" alt="image-20230520112636768" style="zoom:67%;" />

- 节点映射

  将集群中各个IP节点映射到环上的某一个位置。
  将各个服务器使用Hash进行一个哈希，具体可以选择服务器的IP或主机名作为关键字进行哈希，这样每台机器就能确定其在哈希环上的位置。假如
  4个节点NodeA、 B、C、D,经过IP地址的哈希函数计算(hash(ip))， 使用IP地址哈希后在环空间的位置如下:



<img src="..\docker_study\imgs\image-20230520114627451.png" alt="image-20230520114627451" style="zoom:67%;" />





- key值落到服务器上的规则

当我们需要存储- -个kv键值对时，首先计算key的hash值，hash(key), **将这个key使用相同的函数Hash计算出哈希值并确定此数据在环上的位置，从此位置沿环顺时针“行走”，第一台遇到的服务器就是其应该定位到的服务器，并将该键值对存储在该节点上**。

如我们有Object A、Object B、Object C、Object D四个数据对象，经过哈希计算后，在环空间上的位置如下:根据- -致性Hash算法， 数据A会被定为到Node A上，B被定为到Node B上，C被定为到Node C上，D被定为到Node D上。

<img src="..\docker_study\imgs\image-20230520114927188.png" alt="image-20230520114927188" style="zoom:67%;" />



> 优点



- 一致性哈希算法的**容错性**

  ​       假设Node C宕机，可以看到此时对象A、B、D不会受到影响，只有C对象被重定位到Node D。一般的，在一致性Hash算法中，如果一台服务器不可用，**则受影响的数据仅仅是此服务器到其环空间中前一台服务 器(即沿着逆时针方向行走遇到的第一 台服务器)之间数据，其它不会受到影响**。 简单说，就是C挂了，受到影响的只是B、C之间的数据，并且这些数据会转移到D进行存储。



<img src="..\docker_study\imgs\image-20230520115735536.png" alt="image-20230520115735536" style="zoom:67%;" />





- 一致性哈希算法的**扩展性**

​       数据量增加了，需要增加一台节点NodeX, X的位置在A和B之间，那收到影响的也就是A到X之间的数据，重新把A到X的数据录入到X上即可，**不会导致hash取余全部数据重新洗牌。**

<img src="..\docker_study\imgs\image-20230520120036613.png" alt="image-20230520120036613" style="zoom:67%;" />





> 缺点

- 一致性哈希算法的数据倾斜问题

hash环的数据倾斜问题

一致性Hash算法在服务 **节点太少时，**容易因为节点分布不均匀而造成**数据倾斜**( 被缓存的对象大部分集中缓存在某一台服务器 上)问题，
例如系统中只有两台服务器:



<img src="..\docker_study\imgs\image-20230520122101899.png" alt="image-20230520122101899" style="zoom:67%;" />





##### 小总结

为了在节点数目发生改变时尽可能少的迁移数据

将所有的存储节点排列在收尾相接的Hash环.上，每个key在计算Hash后会顺时针找到临近的存储节点存放。而当有节点加入或退出时仅影响该节点在Hash环上顺时针相邻的后续节点。

> 优点

加入和删除节点只影响哈希环中顺时针方向的相邻的节点，对其他节点无影响。

> 缺点

数据的分布和节点的位置有关，因为这些节点不是均匀的分布在哈希环.上的，所以数据在进行存储时达不到均匀分布的效果。.





#### 哈希槽分区

##### 是什么



> 为什么出现

一致性哈希算法的数据偏移量问题

哈希槽实质就是一个数组，数组[0,2^14 -1]形成hash slot空间。



> 能干什么

解决均匀分配的问题，在数据和节点之间又加入了一层，把这层称为哈希槽(slot) ，用于管理数据和节点之间的关系，现在就相当于节点上放的是槽，槽里放的是数据。



<img src="..\docker_study\imgs\image-20230520140529317.png" alt="image-20230520140529317" style="zoom:80%;" />

槽解决的是粒度问题，相当于把粒度变大了，这样便于数据移动。哈希解决的是映射问题，使用key的哈希值来计算所在的槽，便于数据分配。





> 多少个hash槽

​        一个集群只能有16384个槽， 编号0-16383 (0-2^14-1) 。这些槽会分配给集群中的所有主节点**，分配策略没有要求。可以指定哪些编号的槽分配给哪个主节点。集群会记录节点和槽的对应关系**。解决了节点和槽的关系后，接下来就需要对key求哈希值，然后对16384取余， 余数是几key就落入对应的槽里。slot = CRC16(key) % 16384。以槽为单位移动数据，因为槽的数目是固定的，处理起来比较容易，这样数据移动问题就解决了。





> 为什么是16384个槽

(1)**如果槽位为65536，发送心跳信息的消息头:达8k,发送的心跳包过于庞大**。在消息头中最占空间的是myslots[CLUSTER_ SLOTS/8]。 当槽位 为65536时，这块的大小是: 65536+ 8+ 1024=8kb因为每秒钟，redis 节点需要发送一定数量的ping消息作为心跳包，如果槽位为65536，这个ping消息的消息头太大了，浪费带宽。

(2)**redis的集群主节点数量基本不可能超过1000个。集群节点越多，心跳包的消息体内携带的数据越多**。如果节点过1000个，也会导致网络拥堵。因此redis作 者不建议redis cluster节点数量超过1000个。 那么，对于节点数在1000以内的redis cluster集群， 16384个槽位够用了。没有必要拓展到65536个。

(3)**槽位越小， 节点少的情况下，压缩比高，**容易传输Redis主节点的配置信息中它所负责的哈希槽是通过一. 张bitmap的形式来保存的，在传输过程中会对bitmap进行压缩，但是如果bitmap的填充率slots1 /N很高的话(N表示节点数)， bitmap的压缩率就很低。 如果节点数很少，而哈希槽数量很多的话，bitmap的压缩率就很低。



##### 哈希槽计算

Redis集群中内置了16384个哈希槽，redis 会根据节点数量大致均等的将哈希槽映射到不同的节点。当需要在Redis集群中放置一个 key-value时，redis先对key使用crc16算法算出-一个结果，然后把结果对16384求余数，这样每个key都会对应一个编号在0-16383之间的哈希槽，也就是映射到某个节点上。如下代码，key之A、B在Node2，key之 C落在Node3上



![image-20230521152504933](..\docker_study\imgs\image-20230521152504933.png)



springboot整合了redis才会有lettuce

<img src="..\docker_study\imgs\image-20230520142320565.png" alt="image-20230520142320565" style="zoom: 67%;" />





#### redis集群

> 本次集群的思维图

![image-20230520153221872](..\docker_study\imgs\image-20230520153221872.png)

> 关闭防火墙 + 启动docker后台服务器

```
#查看docker状态   目前是启动
[root@hadoop100 ~]# systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
   Active: active (running) since 五 2023-05-19 17:24:04 CST; 21h ago


#查看防火墙状态   目前是关闭
[root@hadoop100 ~]# systemctl status firewalld.service 
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:firewalld(1)

```



#####  创建集群容器

> 创建集群容器

- 解释
  - --net host                                               使用宿主机的ip和端口默认
  - 7614ae9453d1                                       redis镜像的版本号
  - --cluster-enabled yes                             开启redis集群
  - --appendonly yes                                   开启持久化
  -  --privileged=true -v                               开启宿主机root权限
  - -v                                                              容器卷

```
 docker run  -d --name redis-node-1 --net host --privileged=true -v /redis_cluster/redis-node-1/data:/data 7614ae9453d1 --cluster-enabled yes --appendonly yes --port 6381
 
 
  docker run  -d --name redis-node-2 --net host --privileged=true -v /redis_cluster/redis-node-2/data:/data 7614ae9453d1 --cluster-enabled yes --appendonly yes --port 6382

  docker run  -d --name redis-node-3 --net host --privileged=true -v /redis_cluster/redis-node-3/data:/data 7614ae9453d1 --cluster-enabled yes --appendonly yes --port 6383
  
  docker run  -d --name redis-node-4 --net host --privileged=true -v /redis_cluster/redis-node-4/data:/data 7614ae9453d1 --cluster-enabled yes --appendonly yes --port 6391
    
   docker run  -d --name redis-node-5 --net host --privileged=true -v /redis_cluster/redis-node-5/data:/data 7614ae9453d1 --cluster-enabled yes --appendonly yes --port 6392
      
   docker run  -d --name redis-node-6 --net host --privileged=true -v /redis_cluster/redis-node-6/data:/data 7614ae9453d1 --cluster-enabled yes --appendonly yes --port 6393
   
   
[root@hadoop100 ~]# docker ps
CONTAINER ID   IMAGE          COMMAND                   CREATED              STATUS              PORTS                                         NAMES
eb8c3de1a261   7614ae9453d1   "docker-entrypoint.s…"   2 seconds ago        Up 1 second                                                       redis-node-6
bf206c586328   7614ae9453d1   "docker-entrypoint.s…"   7 seconds ago        Up 6 seconds                                                      redis-node-5
098af405c246   7614ae9453d1   "docker-entrypoint.s…"   12 seconds ago       Up 12 seconds                                                     redis-node-4
e5e7c2a80695   7614ae9453d1   "docker-entrypoint.s…"   19 seconds ago       Up 19 seconds                                                     redis-node-3
0a6298a72e34   7614ae9453d1   "docker-entrypoint.s…"   About a minute ago   Up About a minute                                                 redis-node-2
5041b6821468   7614ae9453d1   "docker-entrypoint.s…"   5 minutes ago        Up 5 minutes                                                      redis-node-1


```



###### 构建redis主从关系

> 构建redis主从关系

- 解释
  - --cluster create ip:port  [ip:port...]                          创建主从关系
  - --cluster-replicas 1                                                     表示一个master搭建一个slave节点

```
#先随机进入一个redis
[root@hadoop100 ~]# docker exec -it  redis-node-1 /bin/bash
#构建主从关系
root@hadoop100:/data# redis-cli --cluster create 192.168.206.100:6381 192.168.206.100:6382 192.168.206.100:6383 192.168.206.100:6391 192.168.206.100:6392 192.168.206.100:6393 --cluster-replicas 1

# 采取的是哈希槽分区 16384个槽  分成了 0 - 5460   5461 - 10922 10923 - 16383
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460   
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 192.168.206.100:6392 to 192.168.206.100:6381
Adding replica 192.168.206.100:6393 to 192.168.206.100:6382
Adding replica 192.168.206.100:6391 to 192.168.206.100:6383
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[0-5460] (5461 slots) master
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[5461-10922] (5462 slots) master
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[10923-16383] (5461 slots) master
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
Can I set the above configuration? (type 'yes' to accept): yes
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.  #这里算成功了

```



###### 查看集群情况

> 查看集群情况

- 解释
  - cluster  info                                                 查看集群信息
  - cluster nodes                                              查看节点信息

```
root@hadoop100:/data# redis-cli -p 6381   #进入redis-node-1
127.0.0.1:6381> CLUSTER INFO              #查看集群信息
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384                   #已分配的哈希槽
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6                     #已知道的节点
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:585
cluster_stats_messages_pong_sent:580
cluster_stats_messages_sent:1165
cluster_stats_messages_ping_received:575
cluster_stats_messages_pong_received:585
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:1165

```



###### 查看主从关系

> 本次案例生成的主从关系为

 主6383 从6393 
 主6382 从6392
 主6381 从6391



- `myself`：表示当前节点是自己。
- `master`：表示当前节点是主节点。
- `slave`：表示当前节点是从节点。
- `fail?`：表示当前节点可能已经失效。
- `handshake`：表示当前节点正在进行握手操作。
- `noaddr`：表示当前节点无法获取到 IP 地址。
- `disconnected`：表示当前节点已经断开连接。
- `fail`：表示当前节点已经失效。
- `failover`：表示当前节点正在进行故障转移操作。
- `noflags`：表示当前节点没有任何标识符。

```
#本次案例生成的主从关系为
 # 主6383 从6393 
 # 主6382 从6392
 # 主6381 从6391
127.0.0.1:6381> CLUSTER NODES
1e80 192.168.206.100:6381@16381 myself,master - 0 1684568367000 1 connected 0-5460  #主机6381的id=1e80

a3ad 192.168.206.100:6393@16393 slave df47 0 1684568368759 3 connected    #从6393属于df47

9336 192.168.206.100:6391@16391 slave 1e80 0 1684568365000 1 connected    #从6391属于1e80

5689 192.168.206.100:6382@16382 master - 0 1684568365000 2 connected 5461-10922   #主机6382的id=5689

df47 192.168.206.100:6383@16383 master - 0 1684568367750 3 connected 10923-16383   #主机6383的id=df47

36ab 192.168.206.100:6392@16392 slave 5689 0 1684568366745 2 connected      #从6392属于id=5689
```



##### 主从容错切换迁移

> 注意集群如果挂了一个不能单独启动得全部重启



###### 连接错误案例

> 目前是三主三从的情况通过哈希槽分成3个区

```
Master[0] -> Slots 0 - 5460   
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
```



> 进入第node_redis_1

```
[root@hadoop100 ~]# docker exec  -it redis-node-1 /bin/bash
root@hadoop100:/data# redis-cli -p 6381
127.0.0.1:6381> 

#往容器中插入值
127.0.0.1:6381> set k1 v1
(error) MOVED 12706 192.168.206.100:6383  #错误叫你移动到 哈希槽为 12706的redis
127.0.0.1:6381> set k2 v2
OK
127.0.0.1:6381> set k3 v3
OK
127.0.0.1:6381> set k4 v4
(error) MOVED 8455 192.168.206.100:6382  #错误叫你移动到 哈希槽为 8455的redis

```



###### 连接正确案例

- 解释

  - -c表示以集群方式连接

  

```
[root@hadoop100 ~]# docker exec  -it redis-node-1 /bin/bash
root@hadoop100:/data# redis-cli -p 6381 -c
127.0.0.1:6381> set k1 v1           #注意观察端口号
-> Redirected to slot [12706] located at 192.168.206.100:6383
OK
192.168.206.100:6383> set k2 v2  #注意观察端口号
-> Redirected to slot [449] located at 192.168.206.100:6391
OK
192.168.206.100:6391> set k3 v3   #注意观察端口号
OK
192.168.206.100:6391> set k4 v4
-> Redirected to slot [8455] located at 192.168.206.100:6382
OK
192.168.206.100:6382>             #注意观察端口号
```



###### 查看集群状态

> 查看集群详细信息

因为之前6381宕机了 所以现在从机变主机6391

- 解释
  - redis-cli --cluster check ip:port

```

root@hadoop100:/data# redis-cli --cluster check  192.168.206.100:6381
192.168.206.100:6382 (05077ffa...) -> 1 keys | 5462 slots | 1 slaves.
192.168.206.100:6383 (56cbd2b7...) -> 1 keys | 5461 slots | 1 slaves.
192.168.206.100:6391 (541bb98e...) -> 2 keys | 5461 slots | 1 slaves.
[OK] 4 keys in 3 masters.         #表示4个keys放在 3个主机上面
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 192.168.206.100:6381)
S: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381   #从机6381
   slots: (0 slots) slave
   replicates 541bb98e6982632c573d12b543a91b39bd149336            #主机id为 6391
   
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382    #主 6382
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
   
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392      #从机 6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689                #主机 6382
   
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393      #从 6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47                #主 6383
   
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
   
M: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)

```



###### 主从容错切换迁移案例



![image-20230521113817397](..\docker_study\imgs\image-20230521113817397.png)



先停掉6391的redis

```

127.0.0.1:6381> CLUSTER NODES
1e80 192.168.206.100:6381@16381 myself,slave 9336 0 1684637786000 7 connected #6381是从机 6381的主机是6391

5689 192.168.206.100:6382@16382 master - 0 1684637789000 2 connected 5461-10922

36ab 192.168.206.100:6392@16392 slave 5689 0 1684637789383 2 connected

a3ad 192.168.206.100:6393@16393 slave f47 0 1684637790398 3 connected

df47 192.168.206.100:6383@16383 master - 0 1684637788000 3 connected 10923-16383

9336 192.168.206.100:6391@16391 master - 0 1684637789000 7 connected 0-5460           #主机6391


[root@hadoop100 ~]# docker ps
CONTAINER ID   IMAGE          COMMAND                   CREATED             STATUS             PORTS                                                     NAMES
eb8c3de1a261   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago        Up About an hour                                                             redis-node-6
bf206c586328   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago        Up About an hour                                                             redis-node-5
098af405c246   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago        Up About an hour                                                             redis-node-4
e5e7c2a80695   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago        Up About an hour                                                             redis-node-3
0a6298a72e34   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago        Up About an hour                                                             redis-node-2
5041b6821468   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago        Up About an hour                                                             redis-node-1

#停止了6391
[root@hadoop100 ~]# docker stop redis-node-4
redis-node-4
[root@hadoop100 ~]# docker ps
CONTAINER ID   IMAGE          COMMAND                   CREATED             STATUS             PORTS                                                     NAMES
eb8c3de1a261   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago        Up About an hour                                                             redis-node-6
bf206c586328   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago        Up About an hour                                                             redis-node-5
e5e7c2a80695   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago        Up About an hour                                                             redis-node-3
0a6298a72e34   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago        Up About an hour                                                             redis-node-2
5041b6821468   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago        Up About an hour  

```



> 进入6381

`cluster nodes` : 查看节点信息

`fail`：表示当前节点已经失效。

```
root@hadoop100:/data# redis-cli -p 6381 -c
127.0.0.1:6381> CLUSTER NODES
1e80 192.168.206.100:6381@16381 myself,master - 0 1684640847000 8 connected 0-5460 #从机变主机

5689 192.168.206.100:6382@16382 master - 0 1684640847480 2 connected 5461-10922

36ab 192.168.206.100:6392@16392 slave 5689 0 1684640846000 2 connected

a3ad 192.168.206.100:6393@16393 slave df47 0 1684640848000 3 connected

df47 192.168.206.100:6383@16383 master - 0 1684640848502 3 connected 10923-16383

9336 192.168.206.100:6391@16391 master,fail - 1684640733240 1684640725643 7 disconnected  #宕机了fail

#虽然宕机了一个但是数据一样可以查询出来
127.0.0.1:6381> get k1
-> Redirected to slot [12706] located at 192.168.206.100:6383
"v1"
192.168.206.100:6383> get k2
-> Redirected to slot [449] located at 192.168.206.100:6381
"v2"
192.168.206.100:6381> get k3
"v3"
192.168.206.100:6381> get k4
-> Redirected to slot [8455] located at 192.168.206.100:6382
"v4"

```





> 如果redis-node-1 6391 回来了

```
[root@hadoop100 redis]# docker ps
CONTAINER ID   IMAGE          COMMAND                   CREATED        STATUS             PORTS                                                     NAMES
eb8c3de1a261   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago   Up 2 hours                                                                   redis-node-6
bf206c586328   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago   Up 2 hours                                                                   redis-node-5
e5e7c2a80695   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago   Up 2 hours                                                                   redis-node-3
0a6298a72e34   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago   Up 2 hours                                                                   redis-node-2
5041b6821468   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago   Up 2 hours                                                                   redis-node-1

#启动redis-node-4 
[root@hadoop100 redis]# docker start redis-node-4 
redis-node-4
[root@hadoop100 redis]# docker ps
CONTAINER ID   IMAGE          COMMAND                   CREATED        STATUS             PORTS                                                     NAMES
eb8c3de1a261   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago   Up 2 hours                                                                   redis-node-6
bf206c586328   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago   Up 2 hours                                                                   redis-node-5
098af405c246   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago   Up 2 seconds                                                                 redis-node-4
e5e7c2a80695   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago   Up 2 hours                                                                   redis-node-3
0a6298a72e34   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago   Up 2 hours                                                                   redis-node-2
5041b6821468   7614ae9453d1   "docker-entrypoint.s…"   21 hours ago   Up 2 hours                                                                   redis-node-1

```



> 查看主从关系

 ```
192.168.206.100:6382> cluster nodes
36ab 192.168.206.100:6392@16392 slave 5689 0 1684642358490 2 connected

df47 192.168.206.100:6383@16383 master - 0 1684642357000 3 connected 10923-16383

a3ad 192.168.206.100:6393@16393 slave df47 0 1684642358000 3 connected

5689 192.168.206.100:6382@16382 myself,master - 0 1684642357000 2 connected 5461-10922

1e80 192.168.206.100:6381@16381 master - 0 1684642359509 8 connected 0-5460   #仍然是主机 先来后到

9336 192.168.206.100:6391@16391 slave 1e80 0 1684642360531 8 connected        #之前的主机变从机了

 ```



总结：

如果之前的主机宕机了，那它的从机就会变成主机，如果之前的主机又起来了，只能是从机了，先来后到的原理





##### 主从扩容

> 先恢复之前的三主三从



![image-20230521143903766](..\docker_study\imgs\image-20230521143903766.png)



###### 查看当前容器数量

> 查看当前容器数量

```
#6台三主三从
[root@hadoop100 ~]# docker ps
CONTAINER ID   IMAGE          COMMAND                   CREATED        STATUS       PORT     NAMES

eb8c3de1a261   7614ae9453d1   "docker-entrypoint.s…"   23 hours ago   Up 4 hours         redis-node-6

bf206c586328   7614ae9453d1   "docker-entrypoint.s…"   23 hours ago   Up 4 hours         redis-node-5

098af405c246   7614ae9453d1   "docker-entrypoint.s…"   23 hours ago   Up 3 hours         redis-node-4

e5e7c2a80695   7614ae9453d1   "docker-entrypoint.s…"   23 hours ago   Up 4 hours         redis-node-3

0a6298a72e34   7614ae9453d1   "docker-entrypoint.s…"   24 hours ago   Up 4 hours         redis-node-2

5041b6821468   7614ae9453d1   "docker-entrypoint.s…"   24 hours ago   Up 4 hours         redis-node-1

```



###### 创建6384和6394

> 创建6384和6394

```
 docker run  -d --name redis-node-7 --net host --privileged=true -v /redis_cluster/redis-node-7/data:/data 7614ae9453d1 --cluster-enabled yes --appendonly yes --port 6384
 
 docker run  -d --name redis-node-8 --net host --privileged=true -v /redis_cluster/redis-node-8/data:/data 7614ae9453d1 --cluster-enabled yes --appendonly yes --port 6394
 
 [root@hadoop100 ~]# docker ps
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS         PORTS  NAMES
60432fb1b5cd   7614ae9453d1   "docker-entrypoint.s…"   2 seconds ago    Up 2 seconds            redis-node-8
b499622e8035   7614ae9453d1   "docker-entrypoint.s…"   26 seconds ago   Up 25 seconds           redis-node-7

```



###### 开始扩容

> 开始扩容

- 解释
  -  redis-cli --cluster add-node ip:port  ip:port              加入集群
  - 6384    就是要作为master新增节点
  - 6381 就是原来集群节点的引路人，相当于6384拜了6381的码头组织加入集群

```
#进入redis-node-7  
[root@hadoop100 ~]# docker exec -it redis-node-7 /bin/bash
#加入集合
root@hadoop100:/data# redis-cli --cluster add-node 192.168.206.100:6384 192.168.206.100:6381
>>> Adding node 192.168.206.100:6384 to cluster 192.168.206.100:6381
>>> Performing Cluster Check (using node 192.168.206.100:6381)
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots: (0 slots) slave
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
>>> Send CLUSTER MEET to node 192.168.206.100:6384 to make it join the cluster.
[OK] New node added correctly.             #一个新的节点加入成功

```



###### 查看当前节点

> 查看当前节点

-  redis-cli --cluster check  ip: port               查看集群节点信息

```
root@hadoop100:/data# redis-cli --cluster check 192.168.206.100:6381
#四个节点
192.168.206.100:6381 (78ba1e57...) -> 2 keys | 5461 slots | 1 slaves. 
192.168.206.100:6382 (05077ffa...) -> 1 keys | 5462 slots | 1 slaves.
192.168.206.100:6384 (e72754ec...) -> 0 keys | 0 slots | 0 slaves.    #刚加入的6384master  0 keys 0 slots
192.168.206.100:6383 (56cbd2b7...) -> 1 keys | 5461 slots | 1 slaves.
[OK] 4 keys in 4 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 192.168.206.100:6381)
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
M: e72754ec63a7efdf59de3ee02d735c37fd56278f 192.168.206.100:6384
   slots: (0 slots) master
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots: (0 slots) slave
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.

```



###### 重新分配槽号

> 重新分配槽号

当前分区是这样

<img src="..\docker_study\imgs\image-20230521152504933.png" alt="image-20230521152504933" style="zoom:80%;" />

怎么扩容

<img src="../docker_study\imgs\image-20230519095118966.png" alt="image-20230521153500134" style="zoom:80%;" />



> 分配槽号

```
root@hadoop100:/data# redis-cli --cluster reshard 192.168.206.100:6381
>>> Performing Cluster Check (using node 192.168.206.100:6381)
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
M: e72754ec63a7efdf59de3ee02d735c37fd56278f 192.168.206.100:6384  #没有分配hash槽
   slots: (0 slots) master
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots: (0 slots) slave
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
How many slots do you want to move (from 1 to 16384)? 4096
What is the receiving node ID? e72754ec63a7efdf59de3ee02d735c37fd56278f   #分配给0slots的master主机

Type 'all' to use all the nodes as source nodes for the hash slots.
Type 'done' once you entered all the source nodes IDs.
#选择all
```



* 怎么计算分配的槽数
  * 16384/master
    * 除以master的数量 不是所以机器的数量

![image-20230521154807267](..\docker_study\imgs\image-20230521154807267.png)





> 分槽成功

```
root@hadoop100:/data# redis-cli --cluster check 192.168.206.100:6381
192.168.206.100:6381 (78ba1e57...) -> 1 keys | 4096 slots | 1 slaves.
192.168.206.100:6382 (05077ffa...) -> 1 keys | 4096 slots | 1 slaves.
192.168.206.100:6384 (e72754ec...) -> 1 keys | 4096 slots | 0 slaves.
192.168.206.100:6383 (56cbd2b7...) -> 1 keys | 4096 slots | 1 slaves.
[OK] 4 keys in 4 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 192.168.206.100:6381)
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[1365-5460] (4096 slots) master
   1 additional replica(s)
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[6827-10922] (4096 slots) master
   1 additional replica(s)
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
M: e72754ec63a7efdf59de3ee02d735c37fd56278f 192.168.206.100:6384
   slots:[0-1364],[5461-6826],[10923-12287] (4096 slots) master
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[12288-16383] (4096 slots) master
   1 additional replica(s)
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots: (0 slots) slave
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```



> 当前分槽为

![image-20230521161835888](..\docker_study\imgs\image-20230521161835888.png)





###### 为主节点分配从节点

- 命令
  - redis-cli --cluster add-node ip:salve_port  ip:master_port --cluster-salve --cluster-master-id  新主机节点id

```
#先查看新主机节点id
root@hadoop100:/data# redis-cli --cluster check 192.168.206.100:6381

M: e72754ec63a7efdf59de3ee02d735c37fd56278f 192.168.206.100:6384
   slots:[0-1364],[5461-6826],[10923-12287] (4096 slots) master

#为主节点分配从节点
root@hadoop100:/data# redis-cli --cluster add-node 192.168.206.100:6394 192.168.206.100:6384 --cluster-slave --cluster-master-id e72754ec63a7efdf59de3ee02d735c37fd56278f  #第四台master


#查看节点
root@hadoop100:/data# redis-cli -cluster check 192.168.206.100:6381
Unrecognized option or bad number of args for: '-cluster'
root@hadoop100:/data# redis-cli --cluster check 192.168.206.100:6381
192.168.206.100:6381 (78ba1e57...) -> 1 keys | 4096 slots | 1 slaves.
192.168.206.100:6382 (05077ffa...) -> 1 keys | 4096 slots | 1 slaves.
192.168.206.100:6384 (e72754ec...) -> 1 keys | 4096 slots | 1 slaves.
192.168.206.100:6383 (56cbd2b7...) -> 1 keys | 4096 slots | 1 slaves.
[OK] 4 keys in 4 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 192.168.206.100:6381)
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[1365-5460] (4096 slots) master
   1 additional replica(s)
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[6827-10922] (4096 slots) master
   1 additional replica(s)
S: 5a8684ae49b2af7a922787dd72f322b200d4de44 192.168.206.100:6394     # 从节点6394
   slots: (0 slots) slave
   replicates e72754ec63a7efdf59de3ee02d735c37fd56278f               #从节点的主节点id
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
M: e72754ec63a7efdf59de3ee02d735c37fd56278f 192.168.206.100:6384      #主节点
   slots:[0-1364],[5461-6826],[10923-12287] (4096 slots) master
   1 additional replica(s)
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[12288-16383] (4096 slots) master
   1 additional replica(s)
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots: (0 slots) slave
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80

```





##### 主从缩容

> 思想

![image-20230522091024080](C:\Users\34912\Desktop\docker_study\imgs\image-20230522091024080.png)

![image-20230522091024080](..\imgs\image-20230522091024080.png)



> 目的: 
>
> ​	停止从机6394
>
> ​	停止主机6384



###### 查看集群情况

> 去获取从机6394的id

```
#进入集群
[root@hadoop100 ~]# docker exec -it redis-node-2 /bin/bash
#检查Redis集群的状态
root@hadoop100:/data# redis-cli --cluster check 192.168.206.100:6382
192.168.206.100:6382 (05077ffa...) -> 1 keys | 4096 slots | 1 slaves.
192.168.206.100:6384 (e72754ec...) -> 1 keys | 4096 slots | 1 slaves.
192.168.206.100:6383 (56cbd2b7...) -> 1 keys | 4096 slots | 1 slaves.
192.168.206.100:6381 (78ba1e57...) -> 1 keys | 4096 slots | 1 slaves.
[OK] 4 keys in 4 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 192.168.206.100:6382)
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[6827-10922] (4096 slots) master
   1 additional replica(s)
M: e72754ec63a7efdf59de3ee02d735c37fd56278f 192.168.206.100:6384
   slots:[0-1364],[5461-6826],[10923-12287] (4096 slots) master
   1 additional replica(s)
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[12288-16383] (4096 slots) master
   1 additional replica(s)
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
S: 5a8684ae49b2af7a922787dd72f322b200d4de44 192.168.206.100:6394
   slots: (0 slots) slave
   replicates e72754ec63a7efdf59de3ee02d735c37fd56278f
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[1365-5460] (4096 slots) master
   1 additional replica(s)
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots: (0 slots) slave
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.

```



###### 删除从节点

> 删除6394

- 基础命令
  - redis-cli --cluster del-node ip:端口  节点id

```
#要删除的从机信息
S: 5a8684ae49b2af7a922787dd72f322b200d4de44 192.168.206.100:6394
   slots: (0 slots) slave
   replicates e72754ec63a7efdf59de3ee02d735c37fd56278f
   
#执行删除   
 root@hadoop100:/data# redis-cli --cluster del-node 192.168.206.100:6394 5a8684ae49b2af7a922787dd72f322b200d4de44
>>> Removing node 5a8684ae49b2af7a922787dd72f322b200d4de44 from cluster 192.168.206.100:6394
>>> Sending CLUSTER FORGET messages to the cluster...
>>> Sending CLUSTER RESET SOFT to the deleted node.

#查看是否删除成功 少了一台从机
root@hadoop100:/data# redis-cli --cluster check 192.168.206.100:6381
192.168.206.100:6381 (78ba1e57...) -> 1 keys | 4096 slots | 1 slaves.
192.168.206.100:6382 (05077ffa...) -> 1 keys | 4096 slots | 1 slaves.
192.168.206.100:6384 (e72754ec...) -> 1 keys | 4096 slots | 0 slaves.
192.168.206.100:6383 (56cbd2b7...) -> 1 keys | 4096 slots | 1 slaves.
[OK] 4 keys in 4 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 192.168.206.100:6381)
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[1365-5460] (4096 slots) master
   1 additional replica(s)
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[6827-10922] (4096 slots) master
   1 additional replica(s)
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
M: e72754ec63a7efdf59de3ee02d735c37fd56278f 192.168.206.100:6384
   slots:[0-1364],[5461-6826],[10923-12287] (4096 slots) master
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[12288-16383] (4096 slots) master
   1 additional replica(s)
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots: (0 slots) slave
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.

```



###### 重新分配槽号

* 分槽方式
  * 把mater分支上的槽位全部分给一个主节点
  * 把master分支是的槽位平均分给所有主节点

> 本次是把master分支是的槽位平均分给所有主节点

```
#第一次分配   6384分配1365个槽点给6381
root@hadoop100:/data# redis-cli --cluster reshard 192.168.206.100:6381
>>> Performing Cluster Check (using node 192.168.206.100:6381)
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[6827-10922] (4096 slots) master
   1 additional replica(s)
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
M: e72754ec63a7efdf59de3ee02d735c37fd56278f 192.168.206.100:6384
   slots:[0-1364],[5461-6826],[10923-12287] (4096 slots) master
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[12288-16383] (4096 slots) master
   1 additional replica(s)
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots: (0 slots) slave
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
How many slots do you want to move (from 1 to 16384)? 1365                #输入分配多少个槽点
What is the receiving node ID? 78ba1e5759efe34a26631a25b0a4400329651e80   #6381接收 1365个槽点
Please enter all the source node IDs.
  Type 'all' to use all the nodes as source nodes for the hash slots.
  Type 'done' once you entered all the source nodes IDs.
Source node #1: e72754ec63a7efdf59de3ee02d735c37fd56278f                 #谁出这个资源 6384
Source node #2: done                                                     #输入所有源节点id后，键入'done'



#第二次分配   6384分配1366个槽点给6382
root@hadoop100:/data# redis-cli --cluster reshard 192.168.206.100:6381
>>> Performing Cluster Check (using node 192.168.206.100:6381)
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[6827-10922] (4096 slots) master
   1 additional replica(s)
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
M: e72754ec63a7efdf59de3ee02d735c37fd56278f 192.168.206.100:6384    #此时槽号少了一组
   slots:[5461-6826],[10923-12287] (2731 slots) master    
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[12288-16383] (4096 slots) master
   1 additional replica(s)
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots: (0 slots) slave
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
How many slots do you want to move (from 1 to 16384)? 1366               #输入分配多少个槽点
What is the receiving node ID? 05077ffab7bbb7a8a03c024c648ac092bbf75689   #6382接收 1366个槽点
Please enter all the source node IDs.
  Type 'all' to use all the nodes as source nodes for the hash slots.
  Type 'done' once you entered all the source nodes IDs.
Source node #1: e72754ec63a7efdf59de3ee02d735c37fd56278f                   #谁出这个资源 6384
Source node #2: done                                                       #输入所有源节点id后，键入'done'


#第三次分配   6384分配1365个槽点给6383
root@hadoop100:/data# redis-cli --cluster reshard 192.168.206.100:6381
>>> Performing Cluster Check (using node 192.168.206.100:6381)
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
M: e72754ec63a7efdf59de3ee02d735c37fd56278f 192.168.206.100:6384   #6384只剩下一组槽号
   slots:[10923-12287] (1365 slots) master
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[12288-16383] (4096 slots) master
   1 additional replica(s)
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots: (0 slots) slave
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
How many slots do you want to move (from 1 to 16384)? 1365
What is the receiving node ID? 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
Please enter all the source node IDs.
  Type 'all' to use all the nodes as source nodes for the hash slots.
  Type 'done' once you entered all the source nodes IDs.
Source node #1: e72754ec63a7efdf59de3ee02d735c37fd56278f                   #谁出这个资源 6384
Source node #2: done                                                       #输入所有源节点id后，键入'done'


#查看集群情况
root@hadoop100:/data# redis-cli --cluster cehck 192.168.206.100:6381
Unknown --cluster subcommand
root@hadoop100:/data# redis-cli --cluster check 192.168.206.100:6381
192.168.206.100:6381 (78ba1e57...) -> 2 keys | 5461 slots | 1 slaves.
192.168.206.100:6382 (05077ffa...) -> 1 keys | 5462 slots | 1 slaves.
192.168.206.100:6384 (e72754ec...) -> 0 keys | 0 slots | 0 slaves.            #6384此时槽号为0
192.168.206.100:6383 (56cbd2b7...) -> 1 keys | 5461 slots | 1 slaves.
[OK] 4 keys in 4 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 192.168.206.100:6381)
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
M: e72754ec63a7efdf59de3ee02d735c37fd56278f 192.168.206.100:6384
   slots: (0 slots) master
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots: (0 slots) slave
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.

```





###### 删除master主节点

基础命令

- redis-cli --cluster del-node ip:端口  节点id

```
#删除主节点
root@hadoop100:/data# redis-cli --cluster del-node 192.168.206.100:6384 e72754ec63a7efdf59de3ee02d735c37fd56278f
>>> Removing node e72754ec63a7efdf59de3ee02d735c37fd56278f from cluster 192.168.206.100:6384
>>> Sending CLUSTER FORGET messages to the cluster...
>>> Sending CLUSTER RESET SOFT to the deleted node.


#查看集群情况
root@hadoop100:/data# redis-cli --cluster check 192.168.206.100:6381
192.168.206.100:6381 (78ba1e57...) -> 2 keys | 5461 slots | 1 slaves.
192.168.206.100:6382 (05077ffa...) -> 1 keys | 5462 slots | 1 slaves.
192.168.206.100:6383 (56cbd2b7...) -> 1 keys | 5461 slots | 1 slaves.
[OK] 4 keys in 3 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 192.168.206.100:6381)
M: 78ba1e5759efe34a26631a25b0a4400329651e80 192.168.206.100:6381
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 05077ffab7bbb7a8a03c024c648ac092bbf75689 192.168.206.100:6382
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 105ceca39e8e16b2795f679d304ce5c02fb836ab 192.168.206.100:6392
   slots: (0 slots) slave
   replicates 05077ffab7bbb7a8a03c024c648ac092bbf75689
S: 60ceebe6601db538a4ac64859b61d1c4abe6a3ad 192.168.206.100:6393
   slots: (0 slots) slave
   replicates 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47
M: 56cbd2b7aa0ea2072113d6066f9671cdb23fdf47 192.168.206.100:6383
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 541bb98e6982632c573d12b543a91b39bd149336 192.168.206.100:6391
   slots: (0 slots) slave
   replicates 78ba1e5759efe34a26631a25b0a4400329651e80
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.

```



57p