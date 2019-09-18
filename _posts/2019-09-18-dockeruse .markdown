---
layout:     post
title:      "Docker使用方法"
date:       2019-09-17 12:00:00
author:     "HongF"
header-img: "img/post-bg-miui6.jpg"
tags:
    - Docker
---

> 这篇文章转载自[这篇博客](https://yeasy.gitbooks.io/docker_practice/install/ubuntu.html)

## Docker使用方法总结

### Docker好处





### Docker常用命令

1. 之前提到过，[Docker Hub](https://hub.docker.com/explore/) 上有大量的高质量的镜像可以用，这里我们就说一下怎么获取这些镜像。

   从 Docker 镜像仓库获取镜像的命令是 `docker pull`。其命令格式为：

   ```
   docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
   ```

   例如：

   ```
   docker pull ubuntu:18.04
   ```

   上面的命令中没有给出 Docker 镜像仓库地址，因此将会从 Docker Hub 获取镜像。而镜像名称是 `ubuntu:18.04`，因此将会获取官方镜像 `library/ubuntu` 仓库中标签为 `18.04` 的镜像。

2. 启动一个容器

   启动容器有两种方式，一种是基于镜像新建一个容器并启动，另外一个是将在终止状态（`stopped`）的容器重新启动。

   新建并启动：

   ```
   docker run ubuntu:18.04 /bin/echo 'Hello world'
   ```

   这跟在本地直接执行 `/bin/echo 'hello world'` 几乎感觉不出任何区别。

   下面的命令则启动一个 bash 终端，允许用户进行交互。

```bash
$ docker run -t -i ubuntu:18.04 /bin/bash
root@af8bae53bdd3:/#
```

其中，`-t` 选项让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上， `-i` 则让容器的标准输入保持打开。

3. 启动已终止的容器

   可以利用 `docker container start` 命令，直接将一个已经终止的容器启动运行。

   容器的核心为所执行的应用程序，所需要的资源都是应用程序运行所必需的。除此之外，并没有其它的资源。可以在伪终端中利用 `ps` 或 `top` 来查看进程信息。

   ```bash
   root@ba267838cc1b:/# ps
     PID TTY          TIME CMD
       1 ?        00:00:00 bash
      11 ?        00:00:00 ps
   ```

   可见，容器中仅运行了指定的 bash 应用。这种特点使得 Docker 对资源的利用率极高，是货真价实的轻量级虚拟化。

4. 终止容器

   可以使用 `docker container stop` 来终止一个运行中的容器。

   终止状态的容器可以用 `docker container ls -a` 命令看到。

   处于终止状态的容器，可以通过 `docker container start` 命令来重新启动。

5. 进入容器

在使用 `-d` 参数时，容器启动后会进入后台。

exec命令

-i -t 参数

`docker exec` 后边可以跟多个参数，这里主要说明 `-i` `-t` 参数。

只用 `-i` 参数时，由于没有分配伪终端，界面没有我们熟悉的 Linux 命令提示符，但命令执行结果仍然可以返回。

当 `-i` `-t` 参数一起使用时，则可以看到我们熟悉的 Linux 命令提示符。

```bash
$ docker run -dit ubuntu
69d137adef7a8a689cbcb059e94da5489d3cddd240ff675c640c8d96e84fe1f6

$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
69d137adef7a        ubuntu:latest       "/bin/bash"         18 seconds ago      Up 17 seconds                           zealous_swirles

$ docker exec -i 69d1 bash
ls
bin
boot
dev
...

$ docker exec -it 69d1 bash
root@69d137adef7a:/#
```

如果从这个 stdin 中 exit，不会导致容器的停止。这就是为什么推荐大家使用 `docker exec` 的原因。

更多参数说明请使用 `docker exec --help` 查看。

6. 删除容器

   可以使用 `docker container rm` 来删除一个处于终止状态的容器。例如

   ```bash
   $ docker container rm  trusting_newton
   trusting_newton
   ```

   如果要删除一个运行中的容器，可以添加 `-f` 参数。Docker 会发送 `SIGKILL` 信号给容器。

   

   

