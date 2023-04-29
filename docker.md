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
protected-mode yes  

```



###### 创建容器

```
[root@hadoop100 redis]# docker run -p 6379:6379 --name myredis6.2.6 --privileged=true -v /docker/redis/redis.conf:/etc/redis/redis.conf -v /docker/redis/data:/data -d redis redis-server /etc/redis/redis.conf

```

```
[root@hadoop100 redis]# docker run -p 6379:6379 --name myredis6.2.6 --privileged=true 
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





面试题： 如果有2亿条数据需要进行缓存，请问如何设计这个存储案例

单机肯定不可能实现，

2亿条记录就是2亿个k,v， 我们单机不行必须要分布式多机，假设有3台机器构成-一个集群，用户每次读写操作都是根据公式:
hash(key) % N个机器台数，计算出哈希值，用来决定数据映射到哪-一个节点上。

优点：

简单方便，快捷只需要估计好数据规划好节点，列如3，6，9，就能保证一段时间的数据支持，使用hash算法固定要一部分请求落到同一台服务器上面，这样每一台服务器固定处理这些请求，起到负载均衡+分而处理的作用。



缺点：

如果其中一台服务挂了，或者进行扩容和缩容就比较麻烦了每次数据变动导致节点变动，映射关系需要重新计算，在服务器个数固定不变的时候没有问题，当服务器个数改变时候取模公式会发生变化hash(key)3会变成hash(Key)?。如果地址经过redis机器宕机，由于台数变化，会导致hash取余数据重新洗牌。



43级