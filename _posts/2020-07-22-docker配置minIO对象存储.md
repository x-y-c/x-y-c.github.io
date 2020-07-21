---
layout: post
author: yc
tags: docker
---

# docker 配置minIO对象存储

### minIO 是什么

MinIO 是一个基于Apache License v2.0开源协议的对象存储服务。它兼容亚马逊S3云存储服务接口，非常适合于存储大容量非结构化的数据，例如图片、视频、日志文件、备份数据和容器/虚拟机镜像等，而一个对象文件可以是任意大小，从几kb到最大5T不等。

MinIO是一个非常轻量的服务,可以很简单的和其他应用的结合，类似 NodeJS, Redis 或者 MySQL。

所以，对于图片视频等，可以使用minIO实现更方便的存储

### minIO的部署

- 作为一个存储服务，肯定要做到把上传的文件保存到主机中，如果仅仅保存到容器中，不进行数据卷的映射，会导致在docker停止后，数据全部丢失。

- 此外，作为存储服务器，需要有登录的用户名和密码，即`ACCESS_KEY` 和`SECRET_KEY`,这两项，可以在该容器启动时进行配置

  #### minIO的docker启动命令

  ```
  docker run -p 9000:9000 \
    --name minio1 \
    -v /mnt/data:/data \
    -e "MINIO_ACCESS_KEY=AKIAIOSFODNN7EXAMPLE" \
    -e "MINIO_SECRET_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" \
    minio/minio server /data
  ```

  - -p 为端口映射，即将本机的9000端口和docker启动的容器的9000端口做映射

  - --name 为指定该容器的名称为 minio1

  - -e 指定了secret_key和access_key

  - -v 是指将主机中的/mnt/data 目录映射到docker容器中的data目录

  - minio/minio server 是指定在容器中，上传的文件存储在容器的data目录，结合 上一步，可以知道，实际是存储在了本机的/mnt/data目录中。

    

通过该命令，可以完成部署，通过浏览器访问ip:9000端口，即可看到minIO的登录界面，用户名和密码为 access_key 和 secret_key 

同时，minIO也兼容亚马逊S3的存储接口，可以通过接口进行文件的上传、返回地址等操作。