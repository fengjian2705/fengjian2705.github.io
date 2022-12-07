---
title: Nginx 安装
tags:
  - nginx
index_img: https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_init.jpg
# excerpt: 搭建自己的 maven 仓库
categories:
  - 运维
  - java
---

## 一、传统方式安装

1. 去官网 http://nginx.org/ 下载对应的 nginx 包，推荐使用稳定版本

2. 上传 nginx 到 linux 系统

3. 安装依赖环境

   (1) 安装 gcc 环境：
     ```shell
       yum install gcc-c++
    ```
   (2) 安装 PCRE 库，用于解析正则表达式:
    ```shell
       yum install -y pcre pcre-devel
    ```
   (3) zlib 压缩和解压缩依赖
    ```shell
        yum install -y zlib zlib-devel 
    ```
   (4) SSL 安全的加密的套接字协议层，用于 HTTP 安全传输，也就是 https
    ```shell
        yum install -y openssl openssl-devel
    ```
4. 解压，需要注意，解压后得到的是源码，源码需要编译后才能安装
   ```shell 
      tar -zxvf nginx-1.16.1.tar.gz
   ```
5. 编译之前，先创建nginx临时目录，如果不创建，在启动nginx的过程中会报错
    ```shell 
       mkdir /var/temp/nginx -p
    ```
6. 在nginx目录，输入如下命令进行配置，目的是为了创建makefile文件
    ```shell 
       ./configure --prefix=/usr/local/nginx \
        --pid-path=/var/run/nginx/nginx.pid \
        --lock-path=/var/lock/nginx.lock \
        --error-log-path=/var/log/nginx/error.log \
        --http-log-path=/var/log/nginx/access.log \
        --with-http_gzip_static_module \
        --http-client-body-temp-path=/var/temp/nginx/client \
        --http-proxy-temp-path=/var/temp/nginx/proxy \
        --http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
        --http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
        --http-scgi-temp-path=/var/temp/nginx/scgi
    ```
    * 注： `\n` 代表在命令行中换行，用于提高可读性
    * 配置命令：  
      |命令|解释|
      |---|---|
      |-prefix|指定 nginx 安装目录|
      |–pid-path |指向nginx的pid|
      |–lock-path |锁定安装文件，防止被恶意篡改或误操作|
      |–error-log |错误日志|
      |–http-log-path |http日志|
      |–with-http_gzip_static_module |启用gzip模块，在线实时压缩输出数据流|
      |–http-client-body-temp-path |设定客户端请求的临时目录|
      |–http-proxy-temp-path |设定http代理临时目录|
      |–http-fastcgi-temp-path |设定fastcgi临时目录|
      |–http-uwsgi-temp-path |设定uwsgi临时目录|
      |–http-scgi-temp-path |设定scgi临时目录|

7. make编译: make
8. 安装: make install
9. 进入sbin目录启动 nginx:
    ```shell 
     cd /usr/local/nginx
     ./nginx
    ```
   停止：./nginx -s stop
   重新加载：./nginx -s reload'

10. 打开浏览器，访问虚拟机所处内网ip即可打开nginx默认页面，显示如下便表示安装成功：

    注意事项:
    1. 如果在云服务器安装，需要开启默认的nginx端口：80
    2. 如果在虚拟机安装，需要关闭防火墙
    3. 本地win或mac需要关闭防火墙

## 二、docker方式安装

1. 拉取镜像
   ```shell
      docker pull nginx:1.19.3-alpine
   ```

2. 备份镜像
   ```shell
      docker save nginx:1.19.3-alpine -o nginx.1.19.3.alpine.tar
   ```
3. 导入镜像
   ```shell
      docker load -i nginx.1.19.3.alpine.tar
   ```
4. 运行镜像
   ```shell
      docker run -itd --name nginx -p 80:80 nginx:1.19.3-alpine
      进入容器
      docker exec -it nginx sh
      查看html目录
      cd /usr/share/nginx/html
      配置文件目录
      cat /etc/nginx/nginx.conf
   ```
5. 浏览器测试
   ```shell
      http://机器IP
   ```
   
## 三、核心配置文件 nginx.conf

1. 设置worker进程的用户，指的linux中的用户，会涉及到nginx操作目录或文件的一些权限，默认为 nobody
   ```shell
      user root;
   ```
2. worker进程工作数设置，一般来说CPU有几个，就设置几个，或者设置为N-1也行
   ```shell
      worker_processes 1;
   ```
3. nginx 日志级别 debug | info | notice | warn | error | crit | alert | emerg ，错误级别从左到右越来越大

4. 设置nginx进程 pid
   ```shell
      pid logs/nginx.pid;
   ```
5. 设置工作模式
   ```shell
      events {
        # 默认使用epoll
        use epoll;
        # 每个worker允许连接的客户端最大连接数
        worker_connections 10240;
      }
   ```
6. http 是指令块，针对http网络传输的一些指令配置
   ```shell
      http {
      }
   ```
7. include 引入外部配置，提高可读性，避免单个配置文件过大
   ```shell
      include mime.types;
   ```
8. 设定日志格式， main 为定义的格式名称，如此 access_log 就可以直接使用这个变量了
   
   | 参数名                   |参数意义|
   |-------------------------|------|
   | $remote_addr          |客户端ip|
   | $remote_user          |远程客户端用户名，一般为：’-’|
   | $time_local           |时间和时区|
   | $request              |请求的url以及method|
   | $status               |响应状态码|
   | $body_bytes_send      |响应客户端内容字节数|
   | $http_referer         |记录用户从哪个链接跳转过来的|
   | $http_user_agent      |用户所使用的代理，一般来时都是浏览器|
   | $http_x_forwarded_for |通过代理服务器来记录客户端的ip|

9. sendfile 使用高效文件传输，提升传输性能。启用后才能使用 tcp_nopush ，是指当数据表累积一定大小后才发送，提高了效率。
   ```shell
   sendfile on;
   tcp_nopush on;
   ```
10. keepalive_timeout 设置客户端与服务端请求的超时时间，保证客户端多次请求的时候不会重复建立新的连接，节约资源损耗。
   ```shell
   #keepalive_timeout 0;
   keepalive_timeout 65
   ```

## 四、nginx 日志切割

1. 手动切割
   现有的日志都会存在 access.log 文件中，但是随着时间的推移，这个文件的内容会越来越多，体积会越来越大，不便于运维人员查看，所以我们可以通过把文件切割为多份不同的小文件作为日志，切割规则可以以 天 为单位，如果每天有几百G或者几个T的日志的话，则可以按需以`每半天`或者`每小时`对日志切割
   
   步骤如下：
   *  创建一个shell可执行文件： cut_my_log.sh ，内容为：
      ```shell
         #!/bin/bash
         LOG_PATH="/var/log/nginx/"
         RECORD_TIME=$(date -d "yesterday" +%Y-%m-%d+%H:%M)
         PID=/var/run/nginx/nginx.pid
         mv ${LOG_PATH}/access.log ${LOG_PATH}/access.${RECORD_TIME}.log
         mv ${LOG_PATH}/error.log ${LOG_PATH}/error.${RECORD_TIME}.log
         #向Nginx主进程发送信号，用于重新打开日志文件
         kill -USR1 `cat $PID`
      ```
   * 为`cut_my_log.sh`添加可执行的权限：
     ```shell
        chmod +x cut_my_log.sh
     ```
   * 测试日志切割后的结果:
     ```shell
        ./cut_my_log.sh
     ```
     
2. 定时切割
   
   * 安装定时任务：
     ```shell
      yum install crontabs
     ```
   * crontab -e 编辑并且添加一行新的任务：
     ```shell
      */1 * * * * /usr/local/nginx/sbin/cut_my_log.sh
     ```
   * 重启定时任务：
     ```shell
      service crond restart
     ```
   * 附：常用定时任务命令
     ```shell
      service crond start //启动服务
      service crond stop //关闭服务
      service crond restart //重启服务
      service crond reload //重新载入配置
      crontab -e // 编辑任务
      crontab -l // 查看任务列表
     ```
   * 定时任务表达式：
     Cron表达式是，分为5或6个域，每个域代表一个含义，如下所示：
   
      |   |分  |时  |日 |月  |星期几|年（可选）|
      |------|-----|---|---|---|-----|--------|
      |取值范围|0-59|0-23|1-31|1-12|1-7|2022/2023...|
   
     常用表达式：
      * 每分钟执行：
         ```shell
            */1 * * * *
         ```
      * 每日凌晨（每天晚上23:59）执行：
         ```shell
            59 23 * * *
         ```
      * 每日凌晨1点执行：
         ```shell
            0 1 * * * 
         ```
     参考文献：
     每天定时为数据库备份：https://www.cnblogs.com/leechenxiang/p/7110382.html

## 五、虚拟主机-使用nginx为静态资源提供服务

1. 将静态资源放置到服务器上（例如：图片拷贝到 /home/images 文件夹下）

2. 配置 nginx location
    * root方式
    ```shell
       server {
        listen       89;
        server_name  localhost;
   
        location /images {
            root   /home;
        }
       }
    ```
    测试：`http://ip:89/images/001.jpg`
    * alias别名方式
    ```shell
       server {
        listen       89;
        server_name  localhost;
   
        location /static {
            alias   /home/images;
        }
       }
    ```
   测试：`http://ip:89/static/001.jpg`

## 六、使用 Gzip 压缩提升请求效率

```shell
# 开启 gzip 压缩功能，目的：提高传输效率，节约带宽
  gzip  on;
# 限制最小压缩，小于 1 字节文件不会被压缩
  gzip_min_length 1;
# 定义压缩的级别 1-9（压缩比，文件越大，压缩越多，但是 cpu 使用就越多）
  gzip_comp_level 3;
# 定义压缩文件的类型
  gzip_types  text/plain application/javascript application/css text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
```

## 七、location 路径匹配规则

1. / 默认匹配规则

    访问根路径 / ，nginx 会去 root 对应的 html 文件夹下寻找 index 对应的 index.html 或 index.htm 文件
    ```shell
    server {
        listen       89;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
    }
    ```
2. = 精确匹配
    
    ```shell
    server {
        listen       91;
        server_name  localhost;
        location =/ {
            root   html;
            index  index.html index.htm;
        }
        location =/images/001.jpg {
            root   /home;
        }
    }
    ```
3. ~* 正则匹配，* 表示不区分大小写
    
    ```shell
    server {
        listen       91;
        server_name  localhost;
        location ~* \.(GIF|png|bmp|jpg|jpeg) {
            root   /home;
        }
    }
    ```
4. ~ 正则匹配，区分大小写

    ```shell
    server {
        listen       91;
        server_name  localhost;
        location ~ \.(GIF|png|bmp|jpg|jpeg) {
            root   /home;
        }
    }
    ```
5. ^~ 以某个字符路径开头请求
    ```shell
    server {
        listen       91;
        server_name  localhost;
        location ^~ \images {
            root   /home;
        }
    }
    ```
   
## 八、DNS 域名解析

1. linux / mac 中 hosts 文件路径：/etc/hosts
2. window 中 hosts 文件路径 C:\Windows\System32\drivers\etc\hosts

可以借助 SwitchHosts 工具进行域名映射

```shell
127.0.0.1 www.fengjian.com
```

## 九、nginx 中解决跨域问题

在 server 中添加配置：
```shell
#允许跨域请求的域，*代表所有
add_header 'Access-Control-Allow-Origin' *;
#允许带上cookie请求
add_header 'Access-Control-Allow-Credentials' 'true';
#允许请求的方法，比如 GET/POST/PUT/DELETE
add_header 'Access-Control-Allow-Methods' *;
#允许请求的header
add_header 'Access-Control-Allow-Headers' *;
```

## 十、nginx 中配置静态资源防盗链

html 代码引用我们 nginx 服务器中的一张图片：

```html
<img src="http://www.fengjian.com:89/static/001.jpg">
```

加上防盗链配置：

```shell
#对源站点验证
valid_referers *.fengjian.com;
#非法引入会进入下方判断
if ($invalid_referer) {
  return 404; # 状态码可自定义，403 500 ...
}
```
浏览器有缓存，强制刷新后（Ctrl+F5）图片禁止访问！

## 十一、nginx 模块化设计

1. core
