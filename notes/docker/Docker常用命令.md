## Docker常用命令
* `docker images`  查看当前docker镜像

*  `docker ps` 查看当前运行的容器

*  `docker stop <container_id>` 停用指定id的容器

*  `docker rm <container_id>` 移除指定id的容器

*  `docker search <image_keyword>` 搜索镜像

*  `docker pull <image_name>` 拉取镜像

*  `docker run -d -p <ip>:<port>:<port>  --name  <alias> <image_name>` 启动容器并指定端口映射关系指定镜像别名