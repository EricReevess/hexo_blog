---
title: Docker容器的基本使用
tags:
 - "Linux"
 - "运维"
 - "Docker"
 - "学习笔记"
categories: 
 - 运维
keywords: "运维, Linux, Docker"
cover: https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=3134195079,2533312711&fm=26&gp=0.jpg
---


# Docker容器的基本使用

## Docker的基本架构

关键字：

* Dockerfiles：Docker本身的配置文件，配置如何构建容器以及镜像，和端口

* Image：虚拟系统的镜像

* DockerContainer(容器)：镜像运行的虚拟环境，类似虚拟机

* 镜像仓库：虚拟系统的基本镜像，或者已保存的镜像

  

## Docker的基本指令

* 查看当前`运行的/所有的`镜像

  > docker ps 
  >
  > docker ps -a

* 从仓库拉取原始镜像，以Nginx为例

  > docker pull nginx

* 查看所有镜像：

  > docker images

* 在使用一个镜像，在后台创建并运行一个容器，并添加镜像别名以及进行端口映射：

  > docker run --name testNginx -p 80:80 -d nginx

* 在后台创建并运行一个容器，并将`宿主主机指定目录`映射到`容器的指定目录`

  * 将宿主主机下的`/home/index` 文件夹映射到 容器的`/usr/share/nginx/html`文件夹中
  * 当前文件夹使用`pwd`

  > docker run --name mappedNginx -p 8080:80 -d  -v /home/index:/usr/share/nginx/html nginx(镜像名)

* 进入一个正在运行的容器：

  > docker exec -it 镜像id bash

* 运行/停止/重启一个容器

  > docker start/stop/restart 容器ID

* 提交某个镜像为新镜像

  > docker commit 镜像id 新镜像名

* 从容器里面拷贝文件到宿主主机

  > docker cp 容器名：要拷贝的文件在容器里面的路径  要拷贝到宿主机的相应路径

  * 例：容器名为1d1d,从容器里/cert路径下，将nginx.zip从容器里面拷到宿主机的/var/backup路径下面
    在宿主机上执行命令： 

    `docker cp 1d1d:/cert/nginx.zip /var/backup`

    

* 从宿主主机拷贝文件到容器中

  > docker cp 要拷贝的文件路径  容器名：要拷贝到容器里面对应的路径

  * 例：假设容器名为68b99,现在要将宿主机`/var/backup/nginx.zip`文件拷贝到容器里面的/cert路径下面   在宿主机上执行命令：`docker cp /var/backup/nginx.zip 68b99:/cert`

* 将一个镜像保存到本地文件中

  > docker save 镜像名称 >自定义镜像文件名.tar或者zip

* 从文件恢复镜像

  > docker load  < 自定义镜像文件名.tar或者zip

* 删除docker镜像

  > docker rmi 镜像名称



## Nginx配置

进入镜像后，默认网页文件在`/usr/share/nginx/html`下

* 使用Dockerfile以指定文件创建一个Nginx镜像，并运行

  * 使用vim新建一个dockerfile文件，指定使用的镜像，将当前路径下的所有文件拷贝到目标nginx环境的html目录下

    > vim dockerfile
    >
    > 进入编辑
    >
    > FROM nginx
    >
    > ADD ./  /usr/share/nginx/html  

  * 使用命令构建一个镜像

    > docker build -t 自定义镜像名 



