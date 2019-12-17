
# 安装
```
Mac 安装
brew cask install docker
安装成功可以查看docker版本
$ docker --version
查看docker 的信息
$ docker info
返回相应信息，证明安装完成
```
# 镜像（Image）
```
Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数
（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。

```
## 获取镜像
```
从仓库拉取一个镜像
Usage:	docker pull [OPTIONS] NAME[:TAG|@DIGEST]
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
$ docker pull ubuntu:16.04 # 获取名为 ubuntu 标签为16.04的官方镜像
# 存储是一层一层存储，所以下载也是一层一层下载
```
## 查看镜像
```
docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
ubuntu                  18.04               775349758637        6 weeks ago         64.2MB
test/ubuntu             v2                  3547930cdaf3        2 months ago        146MB
nginx                   latest              5a3221f0137b        4 months ago        126MB
python                  3.5                 61bbcc36b492        4 months ago        909MB
127.0.0.1:5000/ubuntu   16.04               5e13f8dd4c1a        4 months ago        120MB
ubuntu                  16.04               5e13f8dd4c1a        4 months ago        120MB
registry                latest              f32a97de94e1        9 months ago        25.8MB
django                  latest              eb40dcf64078        2 years ago         436MB
```
## 删除镜像
```
$ docker image rm [选项] <镜像1> [<镜像2> ...] # <镜像> 可以是 镜像短 ID、镜像长 ID、镜像名 或者 镜像摘要
$ docker image rm ubuntu:18.04
Untagged: ubuntu:18.04
Untagged: ubuntu@sha256:6e9f67fa63b0323e9a1e587fd71c561ba48a034504fb804fd26fd8800039835d
Deleted: sha256:775349758637aff77bf85e2ff0597e86e3e859183ef0baba8b3e8fc8d3cba51c
Deleted: sha256:4fc26b0b0c6903db3b4fe96856034a1bd9411ed963a96c1bc8f03f18ee92ac2a
Deleted: sha256:b53837dafdd21f67e607ae642ce49d326b0c30b39734b6710c682a50a9f932bf
Deleted: sha256:565879c6effe6a013e0b2e492f182b40049f1c083fc582ef61e49a98dca23f7e
Deleted: sha256:cc967c529ced563b7746b663d98248bc571afdb3c012019d7f54d6c092793b8b

# 用 docker image ls 命令来配合
可以使用 docker image ls -q 来配合使用 docker image rm，这样可以成批的删除希望删除的镜像。

比如，我们需要删除所有仓库名为 redis 的镜像
$ docker image rm $(docker image ls -q redis)

或者删除所有在 mongo:3.2 之前的镜像：
$ docker image rm $(docker image ls -q -f before=mongo:3.2)

```

# 容器（Container）
```
镜像和容器的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。
容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的 命名空间。
查看容器
$ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
54b42e0e9891        registry            "/entrypoint.sh /etc…"   3 months ago        Up 4 days           0.0.0.0:5000->5000/tcp   registry
查看容器所有命令
$ docker container --help

Usage:	docker container COMMAND

Manage containers

Commands:
  attach      Attach local standard input, output, and error streams to a running container
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  inspect     Display detailed information on one or more containers
  kill        Kill one or more running containers
  logs        Fetch the logs of a container
  ls          List containers
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  prune       Remove all stopped containers
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  run         Run a command in a new container
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  wait        Block until one or more containers stop, then print their exit codes

Run 'docker container COMMAND --help' for more information on a command.
```
## 启动容器
```
$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
python                  latest              0a3a95c81a2b        3 weeks ago         932MB
test/ubuntu             v2                  3547930cdaf3        2 months ago        146MB
nginx                   latest              5a3221f0137b        4 months ago        126MB
python                  3.5                 61bbcc36b492        4 months ago        909MB
127.0.0.1:5000/ubuntu   16.04               5e13f8dd4c1a        4 months ago        120MB
ubuntu                  16.04               5e13f8dd4c1a        4 months ago        120MB
registry                latest              f32a97de94e1        9 months ago        25.8MB
django                  latest              eb40dcf64078        2 years ago         436MB
$ docker run -it python:latest
Python 3.8.0 (default, Nov 23 2019, 05:36:56)
[GCC 8.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> print ('hello-world')
hello-world
>>> exit
Use exit() or Ctrl-D (i.e. EOF) to exit
>>> exit()
# -t 选项让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上， -i 则让容器的标准输入保持打开
当运行docker run 时 ：
1.检查本地是否存在指定的镜像，不存在就从公有仓库下载
2.利用镜像创建并启动一个容器
3.分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
4.从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
5.从地址池配置一个 ip 地址给容器
6.执行用户指定的应用程序
7.执行完毕后容器被终止
```
## 删除容器
```
可以使用 docker container rm 来删除一个处于终止状态的容器
终止容器
$ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
54b42e0e9891        registry            "/entrypoint.sh /etc…"   3 months ago        Up 4 days           0.0.0.0:5000->5000/tcp   registry
$ docker container stop 54b42e0e
54b42e0e

删除容器
$ docker container rm 54b42e0e
54b42e0e
$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

```
# 仓库（Repository）
```
仓库是存放镜像的场所
```
## 共有仓库（Docker Hub）
## 私有仓库 
```
用户可以创建一个本地私有仓库
通过获取官方 registry 镜像来运行
$ docker run -d -p 5000:5000 --restart=always --name registry registry
aa3a1647476ebf2a25bdda1cc968222c321f447f0100fc5a56d09ad630ff20b1

$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
aa3a1647476e        registry            "/entrypoint.sh /etc…"   4 seconds ago       Up 3 seconds        0.0.0.0:5000->5000/tcp   registry
查看仓库中的镜像
$  curl -X GET http://127.0.0.1:5000/v2/_catalog
{"repositories":[]}

```
### 私有仓库的上传
```
创建好私有仓库之后，就可以使用 docker tag 来标记一个镜像，然后推送它到仓库
$ docker tag python:latest 127.0.0.1:5000/python:latest
$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
127.0.0.1:5000/python   latest              0a3a95c81a2b        3 weeks ago         932MB
python                  latest              0a3a95c81a2b        3 weeks ago         932MB
test/ubuntu             v2                  3547930cdaf3        2 months ago        146MB
nginx                   latest              5a3221f0137b        4 months ago        126MB
python                  3.5                 61bbcc36b492        4 months ago        909MB
127.0.0.1:5000/ubuntu   16.04               5e13f8dd4c1a        4 months ago        120MB
ubuntu                  16.04               5e13f8dd4c1a        4 months ago        120MB
registry                latest              f32a97de94e1        9 months ago        25.8MB
django                  latest              eb40dcf64078        2 years ago         436MB

上传镜像
$ docker push 127.0.0.1:5000/python
The push refers to repository [127.0.0.1:5000/python]
00947a3aa859: Pushed
7290ddeeb6e8: Pushed
d3bfe2faf397: Pushed
cecea5b3282e: Pushed
9437609235f0: Pushed
bee1c15bf7e8: Pushed
423d63eb4a27: Pushed
7f9bf938b053: Pushed
f2b4f0674ba3: Pushed
latest: digest: sha256:76aace0349933165c34ae8f99b32d269103bc14818c1914b104c34521b92f288 size: 2217

查看仓库中镜像
$ curl -X GET http://127.0.0.1:5000/v2/_catalog
{"repositories":["python"]}
```
### 私有仓库的镜像下载
```
删除本地127.0.0.1:5000/python:latest 镜像
$ docker image rm 127.0.0.1:5000/python:latest
Untagged: 127.0.0.1:5000/python:latest
Untagged: 127.0.0.1:5000/python@sha256:76aace0349933165c34ae8f99b32d269103bc14818c1914b104c34521b92f288

$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
python                  latest              0a3a95c81a2b        3 weeks ago         932MB
test/ubuntu             v2                  3547930cdaf3        2 months ago        146MB
nginx                   latest              5a3221f0137b        4 months ago        126MB
python                  3.5                 61bbcc36b492        4 months ago        909MB
127.0.0.1:5000/ubuntu   16.04               5e13f8dd4c1a        4 months ago        120MB
ubuntu                  16.04               5e13f8dd4c1a        4 months ago        120MB
registry                latest              f32a97de94e1        9 months ago        25.8MB
django                  latest              eb40dcf64078        2 years ago         436MB

下载仓库镜像
$ docker pull 127.0.0.1:5000/python
Using default tag: latest
latest: Pulling from python
Digest: sha256:76aace0349933165c34ae8f99b32d269103bc14818c1914b104c34521b92f288
Status: Downloaded newer image for 127.0.0.1:5000/python:latest

```
# 参考
```
https://yeasy.gitbooks.io/docker_practice/
```
