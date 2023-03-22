---
title: 前端需要掌握的Nginx知识点
date: 2021-05-31 13:56:08
tags: Nginx
---

<meta name="referrer" content="no-referrer"/>

## 什么是Nginx?
---

Nginx是一款免费开源的`高性能HTTP服务器`以及`反向代理服务器`（Reverse Proxy）,同时可以提供IMAP/POP3/SMATP代理服务等功能。能够快速的响应静态页面请求和支持第三方功能模块扩展。

![下载 (2).png](https://upload-images.jianshu.io/upload_images/11846892-55b9a71caa94f146.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Nginx的优点
---
* `高并发`(单机支持10万以上的并发连接)
* `高性能`
* `轻量级、内存消耗少`(一般情况下，10000个非活跃的HTTP Keep-Alive连接在Nginx中仅消耗2.5MB的内存，这
是Nginx支持高并发连接的基础。)
* `稳定性高，宕机概率低`(高可靠性是每个worker进程相对独立，master进程在1个worker进程出错时可以快速“拉起”新的worker子进程提供服务。)
* `支持热部署`(master管理进程与worker工作进程的分离设计，使得Nginx能够提供热部署功能，即可以
在7×24小时不间断服务的前提下，升级Nginx的可执行文件。当然，它也支持不停止服务就
更新配置项、更换日志文件等功能。)
* `模块化设计，扩展性较好`(Nginx的设计极具扩展性，它完全是由多个不同功能、不同层次、不同类型且耦合度极低的模块组成。因此，当对某一个模块修复Bug或进行升级时，可以专注于模块自身，无须在意其他)
* `cpu亲和`

## Nginx常用的场景
---
* 静态资源服务器
* 动态匹配
* 反向代理
* Gzip压缩
* 负载均衡

## Nginx的安装配置
---
mac下镜像飞速安装Homebrew教程: https://zhuanlan.zhihu.com/p/90508170
```
$ brew install nginx
```

## 查看macos中nginx所在目录

```
$ which nginx
```

### Nginx常用的命令
---
* 启动: `nginx`
* 查看版本号: `nginx -v`
* 查看nginx 编译的参数: `nginx -V`
* 重新启动nginx: `nginx -s reload`
* 优雅重启，并重新载入配置文件nginx.conf: `/usr/local/nginx/sbin/nginx  -s  reload`
* 优雅停止nginx，有连接时会等连接请求完成再杀死worker进程 `/usr/local/nginx/sbin/nginx  -s  quit`
* 编辑配置文件:`vim /usr/local/etc/nginx/nginx.conf`
具体常用的命令参考如下：
```
nginx -s stop       快速关闭Nginx，可能不保存相关信息，并迅速终止web服务。
nginx -s quit       平稳关闭Nginx，保存相关信息，有安排的结束web服务。
nginx -s reload     因改变了Nginx相关配置，需要重新加载配置而重载。
nginx -s reopen     重新打开日志文件。
nginx -c filename   为 Nginx 指定一个配置文件，来代替缺省的。
nginx -t            不运行，仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。
nginx -v            显示 nginx 的版本。
nginx -V            显示 nginx 的版本，编译器版本和配置参数。
ps -ef | grep nginx 检查一下nginx服务是否在运行
```
成功看到欢迎页面～！（对应的html是/usr/local/var/www/index.html）
![截屏2021-05-31 下午3.14.28.png](https://upload-images.jianshu.io/upload_images/11846892-2f6135da18e116c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### Nginx的默认配置
---
>Nginx 安装目录下的nginx.conf就是Nginx全局的配置文件，我们主要修改这里的内容。nginx.conf.default作为配置文件的备份。

nginx默认使用8080端口 如果发现端口被占用了，可以杀掉使用使用改端口的进程，也可以修改`/usr/local/etc/nginx/nginx.conf `下的
```
http {
    server {
        listen       8181;
        server_name  localhost;  

        #charset koi8-r;
        .....
        }
    }
```

其中`nginx.conf`中的配置信息如下：
```

#user  nobody;
#设置工作进程的数量
worker_processes  1;

#错误日志存放目录
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#进程pid存放位置
#pid        logs/nginx.pid;

# 处理连接
events {
    # 设置连接数,单个后台进程的最大并发数
    worker_connections  1024;
}


http {
    # 文件拓展名查找集合
    include       mime.types;
    # 当查找不到对应类型的时候默认值
    default_type  application/octet-stream;

    # 日志格式，定义别名为 main
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
    
    #nginx访问日志存放位置
    #access_log  logs/access.log  main;

    # 调用 sendfile 系统传输文件
    sendfile        on;  #开启高效传输模式
    #tcp_nopush     on; #减少网络报文段的数量

    # 客户端与服务器连接超时时间，超时自动断开
    #keepalive_timeout  0;
    keepalive_timeout  65;

    # 开启gizip 压缩
    #gzip  on;

    # 虚拟主机
    server {
        listen       8181;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        # 路由
        location / {
            root   html;
            index  index.html index.htm;

            #第一种情况 拒绝访问ip地址段为 50-100 的ip访问
            deny 192.168.10.50/100;
            
            # 第二种情况 只允许ip地址为 192.168.10.50 的ip访问
            allow 192.168.10.50;
            deny all;
            
            # 第三种情况 这样配置都不能访问，从上到下依次匹配
            deny all;
            allow 192.168.10.50;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
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

### 搭建静态站点
```
# 虚拟主机server块
server {
    # 端口
    listen   8080;
    # 匹配请求中的host值
    server_name  localhost;
    
    # 监听请求路径
    location / {
        # 查找目录
        root /source;
        # 默认查找
        index index.html index.htm;
    }
}
```
字段说明： 
* server 配置虚拟主机的相关参数，可以有多个
* server_name 通过请求中的host值 找到对应的虚拟主机的配置
* location 配置请求路由，处理相关页面情况
* root 查找资源的路径

配置完成后执行 `nginx -t` 看是否有错误，如果看到的是下面这种就是成功了
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
然后执行`nginx -s reload` 更新Nginx配置文件,这时候打开浏览器 输入 localhost:8080 应该就能看到你的页面了。
* 如果启动失败
```nginx: [emerg] bind() to 0.0.0.0:8080 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:8080 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:8080 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:8080 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:8080 f
```
排查原因：可能是nginx自己占用了80端口，又把自己block住，只要`killall -9 nginx` 即可

参考链接: https://blog.csdn.net/msbls5/article/details/14165519

### 动态匹配（请求过滤）
> 通常在开发环境或者测试环境的时候呢我们修改了代码，因为浏览器缓存，可能不会生效，需要手动清除缓存，才能看到修改后的效果，这里我们做一个配置让浏览器不缓存相关的资源。
```
location ~* \.(js|css|png|jpg|gif)$ {
    add_header Cache-Control no-store;
}
```
`~* .(js|css|png|jpg|gif)$` 是匹配以相关文件类型然后单独处理。 `add_header` 是给请求的响应加上一个头信息`Cache-Control no-store`，告知浏览器禁用缓存，每次都从服务器获取 效果如下：
![截屏2021-05-31 下午3.37.13.png](https://upload-images.jianshu.io/upload_images/11846892-eac9fb9d093c1c11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 匹配规则
```
location = / {
    [ configuration A ]
}

location / {
    [ configuration B ]
}

location /documents/ {
    [ configuration C ]
}

location ^~ /images/ {
    [ configuration D ]
}

location ~* \.(gif|jpg|jpeg)$ {
    [ configuration E ]
}

```
**`location =|～|～*|^~| /uri/`**

* = 表示精确匹配。只有请求的url路径与后面的字符串完全相等时，才会命中（优先级最高）。
* ^~ 表示如果该符号后面的字符是最佳匹配，采用该规则，不再进行后续的查找。
* ~ 表示该规则是使用正则定义的，区分大小写。
* ~* 表示该规则是使用正则定义的，不区分大小写。
* / 通用匹配，任何请求都会匹配到
#### 通过状态码来过滤请求
```
# 通过状态码，返回指定的错误页面
error_page 500 502 503 504 /50x.html;
location = /50x.html {
    root /source/error_page;
}
```

## 反向代理解决跨域
---
在前后端分离的开发中，跨域问题是一个非常常见的问题，现在解决跨域问题比较常用的两种方式为：

* 跨域资源请求（CORS）
* Nginx反向代理
![612653-20190226142303336-49757621.png](https://upload-images.jianshu.io/upload_images/11846892-6da0da0820a53e49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![截屏2021-05-31 下午4.29.29.png](https://upload-images.jianshu.io/upload_images/11846892-a382f291e2bb04e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



先来看上面的图 ，当用户请求xx.720ui.com/server1的时候，Nginx会将请求转发给Server1这个服务器上的具体应用，从而达到跨域的目的

同时nginx解决跨域时常用的参数配置。
```
location /api {   
    # 请求host传给后端
    proxy_set_header Host $http_host;
    # 请求ip 传给后端
    proxy_set_header X-Real-IP $remote_addr;
    # 请求协议传给后端
    proxy_set_header X-Scheme $scheme;
    # 路径重写
    rewrite  /api/(.*)  /$1  break;
    # 代理服务器
    proxy_pass http://localhost:9000;
}
```
* 拦截路径/api, 可以通过正则匹配
* proxy_set_header 允许重新定义或添加字段传递给代理服务器的请求头
*  `$http_host`  $remote_addr、$scheme 为Nginx内置变量
* rewrite 根据rewrite后的请求URI，将路径重写，如：接口路径为 /user, 我们可以请求 /api/user。（为什么需要重写uri？因为在使用Nginx做反向代理的时候，需要匹配到跨域的接口再做转发，为了方便匹配，会人为的在原接口中添加一段路径（或标示， 如例子中的api），因此需要在匹配之后、转发之前把添加的那段去掉，因此需要rewrite）
* break 继续本次请求后面的处理 ,停止匹配下面的location。需要注意的是与之类似的last执行过程则是停止当前这个请求，并根据rewrite匹配的规则重新发起一个请求，从上到下依次匹配location后面的规则
* proxy_pass 代理服务器
>原理：Nginx拦截到相关匹配规则, Nginx再将请求转发到http://localhost:9090，Nginx得到请求后再响应到前端，可以直接请求/api/user完成请求。
nginx跨域请求一个简单的dome:
```
server
{
    listen 80;
    server_name www.1212.com;

    location ^~ /blog/ {
        proxy_pass http://blog.12121.com/;
    }   
}
```
---

总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [Mac连接远程服务器](https://www.jianshu.com/p/7cf4b2b28721)
* [前端需要掌握的Nginx知识点](https://juejin.cn/post/6844903817595584525)
* [Nginx 入门实战](https://juejin.cn/post/6844904082067423246)
* [前端不能不会的 Nginx 入门 （图文并茂的服务器环境教程）](https://juejin.cn/post/6844904020360986631)
