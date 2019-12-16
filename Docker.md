
# 安装
```
Mac 安装
brew cask install docker
安装成功可以查看docker版本
$ docker --version
查看docker 的信息
$ docker info
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
$ docker pull ubuntu:16.04 // 获取名为 ubuntu 标签为16.04的官方镜像
# 存储是一层一层存储，所以下载也是一层一层下载
```
## 查看镜像
```
docker images
```

# 容器（Container）
```
镜像和容器的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。
容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的 命名空间。
```
# 仓库（Repository）
```

```
# 参考
```
https://yeasy.gitbooks.io/docker_practice/
```
