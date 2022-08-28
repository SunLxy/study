
## Docker

#### 1. 新建 Dockerfile
```
cd docker-demo && touch Dockerfile
```

#### 2. 准备 Nginx 镜像
```
docker pull nginx 
```


  控制台会出现如下信息： 
```
Using default tag: latest
latest: Pulling from library/nginx
8559a31e96f4: Pull complete
8d69e59170f7: Pull complete
3f9f1ec1d262: Pull complete
d1f5ff4f210d: Pull complete
1e22bfa8652e: Pull complete
Digest: sha256:21f32f6c08406306d822a0e6e8b7dc81f53f336570e852e25fbe1e3e3d0d0133
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```


在项目根目录创建nginx配置文件
```
touch default.conf  
```

写入：
```
server {
    listen       80;
    server_name  localhost;
    #charset koi8-r;
    access_log  /var/log/nginx/host.access.log  main;
    error_log  /var/log/nginx/error.log  error;
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

#### 3. 配置镜像

打开 Dockerfile ，写入如下内容：
```
FROM nginx
COPY dist/ /usr/share/nginx/html/
COPY default.conf /etc/nginx/conf.d/default.conf
```

* FROM nginx 指定该镜像是基于 nginx:latest 镜像而构建的。
* COPY dist/ /usr/share/nginx/html/ 命令的意思是将项目根目录下 dist 文件夹中的所有文件复制到镜像中 /usr/share/nginx/html/ 目录下。
* COPY default.conf /etc/nginx/conf.d/default.conf 将 default.conf 复制到 etc/nginx/conf.d/default.conf，用本地的 default.conf 配置来替换 Nginx 镜像里的默认配置。


#### 4. 构建镜像

Docker 通过 build 命令来构建镜像：
```
docker build -t docker-demo .
```
* -t 参数给镜像命名 docker-demo。
* . 是基于当前目录的 Dockerfile 来构建镜像。

执行成功后，将会输出：
```
Sending build context to Docker daemon  115.4MB
Step 1/3 : FROM nginx
 ---> 2622e6cca7eb
Step 2/3 : COPY dist/ /usr/share/nginx/html/
 ---> Using cache
 ---> 82b31f98dce6
Step 3/3 : COPY default.conf /etc/nginx/conf.d/default.conf
 ---> 7df6efaf9592
Successfully built 7df6efaf9592
Successfully tagged docker-demo:latest

```

镜像制作成功！我们来查看一下容器：
```
docker image ls | grep docker-demo
```
可以看到，我们打出了一个 133MB 的项目镜像：
```
docker-demo latest 7df6efaf9592 About a minute ago 133MB
```

##### 5. 运行容器

```
docker run -d -p 3000:80 --name docker-demo docker-demo
```
* -d 设置容器在后台运行。
* -p 表示端口映射，把本机的 3000 端口映射到 container 的 80 端口（这样外网就能通过本机的 3000 端口访问了。
* --name 设置容器名 docker-demo
* docker-demo 是我们上面构建的镜像名字。

#### 6.更新

1. 停止并删除当前容器
``` 
docker stop 7df6efaf9592 && docker rmi -f 7df6efaf9592
```
2. 重新创建容器
```
docker build -t docker-demo .
```
3. 运行和端口映射 
```
docker run -d -p 3000:80 --name docker-demo docker-demo
```

#### 7. docker 命令
```
    1. docker start [OPTIONS] CONTAINER [CONTAINER...]  启动一个或多个已经被停止的容器
    2. docker stop [OPTIONS] CONTAINER [CONTAINER...] 停止一个运行中的容器
    3. docker restart [OPTIONS] CONTAINER [CONTAINER...]  重启容器
```




## 常规操作

①参数使用

FROM：
```
   * 指定基础镜像，所有构建的镜像都必须有一个基础镜像，且 FROM 命令必须是 Dockerfile 的第一个命令
   * FROM <image> [AS <name>] 指定从一个镜像构建起一个新的镜像名字
   * FROM <image>[:<tag>] [AS <name>] 指定镜像的版本 Tag
   * 示例：FROM mysql:5.0 AS database
```
MAINTAINER：
```
   * 镜像维护人的信息
   * MAINTAINER <name>
   * 示例：MAINTAINER Jartto Jartto@qq.com
```
RUN：
```
   * 构建镜像时要执行的命令
   * RUN <command>
   * 示例：RUN [executable, param1, param2]
```
ADD：
```
   * 将本地的文件添加复制到容器中去，压缩包会解压，可以访问网络上的文件，会自动下载
   * ADD <src> <dest>
   * 示例：ADD *.js /app 添加 js 文件到容器中的 app 目录下
```
COPY：
```
   * 功能和 ADD 一样，只是复制，不会解压或者下载文件
```
CMD：
```
   * 启动容器后执行的命令，和 RUN 不一样，RUN 是在构建镜像是要运行的命令
   * 当使用 docker run 运行容器的时候，这个可以在命令行被覆盖
   * 示例：CMD [executable, param1, param2]
```
ENTRYPOINT：
```
   * 也是执行命令，和 CMD 一样，只是这个命令不会被命令行覆盖
   * ENTRYPOINT [executable, param1, param2]
   * 示例：ENTRYPOINT [donnet, myapp.dll]
```
LABEL：为镜像添加元数据，key-value 形式
```
   * LABEL <key>=<value> <key>=<value> ...
   * 示例：LABEL version=1.0 description=这是一个web应用
```
ENV：设置环境变量，有些容器运行时会需要某些环境变量
```
   * ENV <key> <value> 一次设置一个环境变量
   * ENV <key>=<value> <key>=<value> <key>=<value> 设置多个环境变量
   * 示例：ENV JAVA_HOME /usr/java1.8/
```
EXPOSE：暴露对外的端口（容器内部程序的端口，虽然会和宿主机的一样，但是其实是两个端口）
```
   * EXPOSE <port>
   * 示例：EXPOSE 80
   * 容器运行时，需要用 -p 映射外部端口才能访问到容器内的端口
```
VOLUME：指定数据持久化的目录，官方语言叫做挂载
```
   * VOLUME /var/log 指定容器中需要被挂载的目录，会把这个目录映射到宿主机的一个随机目录上，实现数据的持久化和同步
   * VOLUME [/var/log,/var/test.....] 指定容器中多个需要被挂载的目录，会把这些目录映射到宿主机的多个随机目录上，实现数据的持久化和同步
   * VOLUME /var/data var/log 指定容器中的 var/log 目录挂载到宿主机上的 /var/data 目录，这种形式可以手动指定宿主机上的目录   * 
```
WORKDIR：设置工作目录，设置之后 ，RUN、CMD、COPY、ADD 的工作目录都会同步变更
```
  * WORKDIR <path>
  * 示例：WORKDIR /app/test
```
USER：指定运行命令时所使用的用户，为了安全和权限起见，根据要执行的命令选择不同用户
```
   * USER <user>:[<group>]
   * 示例：USER test
```
ARG：设置构建镜像是要传递的参数
```
   * ARG <name>[=<value>]
   * ARG name=sss
```

更多操作，请移步官方使用文档[3]：
https://docs.docker.com/

引用原文：
https://segmentfault.com/a/1190000038280613