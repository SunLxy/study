# nginx

## 安装nginx

```bash
brew install nginx 
```

## nginx 操作

**1. 启动**

```bash
nginx  -s start # nginx 
```

**2. 重新加载配置文件**

```bash
nginx -s reload
```

**3. 停止**

```bash
nginx -s stop
```

## nginx 配置

`brew`安装的`nginx`路径为`/opt/homebrew/etc/nginx`

### 1. `nginx`配置文件树

```bash
.
├── fastcgi.conf
├── fastcgi.conf.default
├── fastcgi_params
├── fastcgi_params.default
├── koi-utf
├── koi-win
├── logs
│   └── nginx.pid
├── mime.types
├── mime.types.default
├── nginx.conf
├── nginx.conf.default
├── scgi_params
├── scgi_params.default
├── servers
│   └── 9001.conf
├── uwsgi_params
├── uwsgi_params.default
└── win-utf
```

### 2. `nginx.conf`配置

```nginx
# 1、几个常见配置项：

# 1.$remote_addr 与 $http_x_forwarded_for 用以记录客户端的ip地址；
# 2.$remote_user ：用来记录客户端用户名称；
# 3.$time_local ： 用来记录访问时间与时区；
# 4.$request ： 用来记录请求的url与http协议；
# 5.$status ： 用来记录请求状态；成功是200；
# 6.$body_bytes_s ent ：记录发送给客户端文件主体内容大小；
# 7.$http_referer ：用来记录从那个页面链接访问过来的；
# 8.$http_user_agent ：记录客户端浏览器的相关信息；
# 2、惊群现象：一个网路连接到来，多个睡眠的进程被同时叫醒，但只有一个进程能获得链接，这样会影响系统性能。

# 3、每个指令必须有分号结束。
#
#
#
#
#
#配置用户或者组，默认为nobody nobody。
#user  nobody;
# user root admin;
# user root wheel;
#允许生成的进程数，默认为1
#user  nobody;
worker_processes 1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;
#制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
#指定nginx进程运行文件存放地址
pid /opt/homebrew/etc/nginx/logs/nginx.pid;
events {
    #设置网路连接序列化，防止惊群现象发生，默认为on
    # accept_mutex on;
    #设置一个进程是否同时接受多个网络连接，默认为off
    # multi_accept off;

    #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    #use epoll;
    worker_connections 1024;
}


http {
    include mime.types;
    default_type application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;
    #tcp_nopush     on;
    #access_log off; #取消服务日志
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
    #自定义格式
    # log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for';
    #combined为日志格式的默认值
    #  access_log log/access.log myFormat;
    # 开启gzip
    gzip on;

    # 启用gzip压缩的最小文件，小于设置值的文件将不会压缩
    gzip_min_length 1k;

    # gzip 压缩级别，1-9，数字越大压缩的越好，也越占用CPU时间，后面会有详细说明
    gzip_comp_level 1;

    # 进行压缩的文件类型。javascript有多种形式。其中的值可以在 mime.types 文件中找到。
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png application/vnd.ms-fontobject font/ttf font/opentype font/x-woff image/svg+xml;

    # 是否在http header中添加Vary: Accept-Encoding，建议开启
    gzip_vary on;

    # 禁用IE 6 gzip
    gzip_disable "MSIE [1-6]\.";

    # 设置压缩所需要的缓冲区大小
    gzip_buffers 32 4k;

    # allow all;
    #deny all;
    #access_log  logs/access.log  main;
    sendfile on;#允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    #tcp_nopush     on;

    client_max_body_size 1024m;
    client_body_buffer_size 1000m;

    #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    # sendfile_max_chunk 100k;

    #连接超时时间，默认为75s，可以在http，server，location块。
    #keepalive_timeout  0;
    keepalive_timeout 65;

    #gzip  on;

    # proxy_cache_path /path/to/cache levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=60m use_temp_path=off;
    #  upstream mysvr {
    #       server 127.0.0.1:7878;
    #       server 192.168.10.121:3333 backup;  #热备
    #     }
    # error_page 404 https://xxxx.xxxx.xxx; #错误页
    server {
        listen 8080;
        server_name localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;
        location / {
            root html;
            index index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            root html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}
        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;
    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;
    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;
    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    include servers/*;
}

```

### 3. `servers/9001.conf`文件配置

```nginx

server {
  listen 9001;
  server_name localhost;
  error_page 500 502 503 504 /50x.html;
  client_body_buffer_size 1000m;
  client_max_body_size 1000m;

  location /api {
    proxy_pass http://192.168.1.1:8000; #请求转向 定义的服务器列表
  }

  location / {
    root /Users/zhangsan/demo/build; #根目录
    index index.html index.htm;  # 设置默认页
    try_files $uri $uri/ /index.html$args;# 页面刷新重定向
    deny 127.0.0.1;  #拒绝的ip
    allow 172.18.5.54; #允许的ip  
  }
}
```
