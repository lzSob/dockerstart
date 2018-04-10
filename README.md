##  docker认识与自动化交付部署实现
* 什么是docker？
	* docker 不是虚拟机，所以不要把虚拟机的概念引入docker。docker容器内没有内核，也没有硬件虚拟，比传统虚拟机更加轻便。
	* 具体请看 [what-docker](https://www.docker-cn.com/what-docker)
* 为什么用docker？
	* 节约系统资源
	* 快速搭建和启动
	* 环境一致性
	* 持续交付与部署
	* 跨平台迁移
	* 社区活跃
* 三个概念 镜像(image) 容器(container) 仓库(repository)
	* 镜像
		* 可以理解成文件系统 
		* 不包含任何动态数据
		* 一般使用Dockerfile定制镜像
	*  容器
		* 可以理解为镜像的实例 
		* 可以被创建、启动、停止、删除、暂停等
		* 保持无状态化，文件读写应该使用数据卷（Volume）、或者绑定宿主目录
	* 仓库
		* 一个 Docker Registry 中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像。
* 安装docker(略)
*  常用命令介绍
	*  docker build
		*  基于Dockerfile 构建镜像 例: docker build -t someApp:1.1 .
	*  docker pull
		*  拉取镜像 例： docker pull mysql
	*  docker push
		*  上传镜像 例： docker push someApp:1.1
	*  docker run
		*  运行容器 
		*  -d 后台运行
		*   －p publish port
		*   --name 定义容器名称
		*  －v 挂载
		*  --link 连接两个容器（现在版本不用了） 用 --network
	*  docker rm
		*  删除容器， －f强制删除
	*  docker rmi
		*  删除镜像
	*  docker stop
		*  停止容器
	*  docker start
		*  开始运行容器
	*    更多请看[Docker — 从入门到实践](https://legacy.gitbook.com/book/yeasy/docker_practice/details)	 

##### 使用Dokerfile构建镜像
直接看栗子
* 常见的前端打包
```
FROM nginx:alpine

COPY dist /usr/share/nginx/html

COPY nginx_site.conf /etc/nginx/conf.d/default.conf
```
* node.js后端打包 (可配置 .dockerignore 过滤文件)
```
FROM keymetrics/pm2-docker-alpine

COPY . /app/

EXPOSE 7000
```
* 当然也可以做其他操作， 但尽可能写在一起。减少层数
```
FROM node

COPY . /app/

RUN npm i && npm cache clean

EXPOSE 7000
```

Dokerfile的其他语法命令可以去看文档

##### docker-compose介绍
使用docker compose我们可以在Run的层面解决很多实际问题,可以用一条命令管理所有的容器。
下面是一个docker-compose.yml栗子
```
version: '3'
services:
  mola_mysql: 
    image: 'mysql'
    restart: 'always'
    environment:
      MYSQL_ROOT_PASSWORD: '123456'
    ports:
      - '3306:3306'
    volumes:
      - '/root/foxtail/data:/var/lib/mysql'
    networks:
      mola_net:
        ipv4_address: 172.18.0.2
  mola:
    image: 'index.alauda.cn/foxtail000/mola'
    restart: 'always'
    ports:
      - '7000:7000'
    links:
      - 'mola_mysql'
    volumes:
      - './config.js:/app/config.js:ro'
    networks:
      mola_net:
        ipv4_address: 172.18.0.3
    command: 'pm2-docker start ./process.yml --only mola-api'

  molaline:
    image: 'index.alauda.cn/foxtail000/mola-line'
    restart: 'always'
    ports:
      - '80:80'
    volumes:
      - './config.line.js:/usr/share/nginx/html/static/js/config.js:ro'
    networks:
      mola_net:
        ipv4_address: 172.18.0.4

networks:
  mola_net:
    driver: 'bridge'
    ipam:
      driver: 'default'
      config:
      -
        subnet: 172.18.0.0/24

```
下面是compose的一些相关的命令
```
docker-compose up -d #后台运行所有容器
docker-compose ps #查看容器运行状态
docker-compose restart #重启所有容器
docker-compose restart App1  #重启App1
docker-compose stop #停止所有容器
docker-compose stop App1  #停止App1
docker-compose down #停止且删除所有容器
```

##### 基于docker实现nodejs服务自动化部署
* 方案一：本地构建，实现test 和dev环境自动部署
	*	 本地代码打包生成image，上传到线上仓库，触发web钩子，服务器上的钩子服务执行相应的脚本任务pull新的image，删除原有容器，运行新容器。test和dev环境可以一致性
	*	 不同环境挂载不同的配置文件
	*	 github只做代码仓库.

*	 方案二： 基于dockerhub自动构建
	*	 将dockerhub与github关联
	*	 配置自动构建选项与web钩子
	*	 不同环境搭建钩子服务，执行部署脚本，也可使用jenkins

两个方案各有利弊： 
方案一： 缺点：代码可能有异同， 优点： 速度快。 dev和test环境可一致
方案二： 缺点：自动构建速度慢， 优点： 全程自动化
	

#### 一些docker教程
* 新手教程：
	* （英文）：https://docs.docker.com/get-started/
	* （中文）：https://docs.docker-cn.com/get-started/
	* （老麦的翻译）：https://docs-cn.docker.octowhale.com/get_started/001.Orientation.html
* Docker 课程：http://training.play-with-docker.com/
* 运维人员课程：http://training.play-with-docker.com/ops-landing/
* 开发人员课程：http://training.play-with-docker.com/dev-landing/
* 大量的例子：
	* （英文）：https://docs.docker.com/samples/
	* （中文）：https://docs.docker-cn.com/samples/
* 用户文档：
	* （英文）：https://docs.docker.com/engine/userguide/
	* （中文）：https://docs.docker-cn.com/engine/userguide/
* 镜像：
	 * (英文）：https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/
	* (老麦的翻译)：https://docs-cn.docker.octowhale.com/engine/userguide/eng-image/dockerfile_best-practices.html
* 存储：https://docs.docker.com/engine/userguide/storagedriver/
* 网络：https://docs.docker.com/engine/userguide/networking/
* 管理文档：https://docs.docker.com/engine/admin/
* 存储：https://docs.docker.com/engine/admin/volumes/
* 安全：https://docs.docker.com/engine/security/security/
* 集群：https://docs.docker.com/engine/swarm/
