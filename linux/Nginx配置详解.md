# Nginx介绍

Nginx是一个高性能的HTTP和反向代理服务器，特点是占有内存少，并发能力强。

## Nginx常用命令

```shell
nginx -c /file/path/nginx.conf
# 为nginx指定一个配置文件，替代缺省的配置文件

nginx -t
# 测试配置文件是否正确，并尝试打开配置文件中所引用到的文件

nginx -v
# 显示nginx的版本
nginx -V
# 显示nginx的版本，编译器版本和配置参数

nginx
# 启动nginx

nginx -s stop
nginx -s quit
# 停止nginx进程，stop会立即停止服务，可能不会保存相关信息
# 而quit会让进程完成当前工作后再停止

nginx -s reload
# 重新加载配置文件，nginx服务不会中断，如果出错会回滚用上一次正确的配置文件保持正常运行

ps -ef | grep nginx
# 查看nginx进程

pkill -9 nginx
# 强制停止正在运行的nginx进程
```

## Nginx核心配置详解

### Nginx的目录结构

```plaintext
├── conf                          #Nginx的主配置文件所在的目录
│   ├── fastcgi.conf              #fastcgi相关参数的配置文件
│   ├── fastcgi_params            #fastcgi参数文件
│   ├── koi-utf
│   ├── koi-win
│   ├── mime.types                #媒体类型
│   ├── nginx.conf                #Nginx的主配置文件，一般都是修改这个文件，修改之前先备份下nginx.conf.default
│   ├── scgi_params
│   ├── uwsgi_params
│   └── win-utf
├── html                          #默认站点目录
│   ├── 50x.html                  #错误页面优雅替代显示文件
│   └── index.html                #默认的首页文件，在实际环境中，大家习惯用
├── logs                          #日志文件目录
│   ├── access.log                #访问日志文件
│   ├── error.log                 #错误日志文件
│   └── nginx.pid                 #这是nginx的PID文件，nginx进程启动后，这个文件会随着nginx启动而产生
└── nginx.exe					  #Windows下Nginx的主程序，Linux下是sbin/nginx，这个是Nginx的二进制文件
```

### Nginx配置文件

```nginx
#启动子进程程序默认用户
#user  nobody;
#一个主进程和多个工作进程。工作进程是单进程的，且不需要特殊授权即可运行；这里定义的是工作进程数量
worker_processes  1;

#全局错误日志的位置及日志格式
#指令的第1个参数用于存放错误日志的路径。第2个参数用于指定错误记录详细程度的等级，默认值为error
#其他常用的等级还有debug、info、notice、warn、error和crit,日志记录详细程度依次递减,debug记录的内容最详细,crit记录的内容最简洁。
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
#关闭错误日志
#error_log /dev/null;

#nginxPID文件的存放位置
#pid        logs/nginx.pid;


events {
    #每个工作进程最大的并发数
    worker_connections  1024;
}

#http服务器设置
http {
    #设定mime类型，类型由mime.types文件定义
    include       mime.types;
    #默认的mime.type类型是application/octet-stream
    default_type  application/octet-stream;

    #日志格式
    #指令的第1个参数main是定义日志格式的名称，第2个参数是具体的日志格式样式
    #log_format指令只能在http模块下使用
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
    #$remote_addr与$http_x_forwarded_for用以记录客户端的ip地址；$http_x_forwarded_for包括中间经过的代理
    #$remote_user：用来记录客户端用户名称；
    #$time_local： 用来记录访问时间与时区；
    #$request： 用来记录请求的url与http协议；
    #$status： 用来记录请求状态；成功是200，
    #$body_bytes_sent ：记录发送给客户端文件主体内容大小；
    #$http_referer：用来记录从那个页面链接访问过来的；
    #$http_user_agent：记录客户浏览器的相关信息；

    #全局访问日志路径
    #指令的第1个参数用于存放访问日志的路径，第2个参数用于指定访问日志的格式，是
    #access_log  logs/access.log  main;

    #sendfile指令指定 nginx 是否调用sendfile 函数（zero copy 方式）来输出文件，对于普通应用，必须设为on。
    #如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络IO处理速度，降低系统uptime。
    sendfile        on;
    #此选项允许或禁止使用socket的TCP_CORK的选项，此选项仅在使用sendfile的时候使用
    #tcp_nopush     on;

    #长连接超时时间, 单位是秒
    #keepalive_timeout  0;
    keepalive_timeout  65;

    #开启压缩
    #gzip  on;

    #配置虚拟主机
    server {
        #虚拟主机使用的端口
        listen       80;
        #虚拟主机域名
        server_name  localhost;

        #虚拟主机支持的字符集
        #charset koi8-r;

        #虚拟主机的访问日志路径
        #access_log  logs/host.access.log  main;

        #定义web根路径
        location / {
            #根目录路径
            root   html;
            #默认的页面文件名称
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        #根据错误码 返回对应的页面
        error_page   500 502 503 504  /50x.html;
        #定义页面路径
        location = /50x.html {
            root   html;
        }

        #定义反向代理服务器 数据服务器是lamp模型
        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        #定义PHP为本机服务的模型
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
        #拒绝nginx DR目录及子目录下的.htaccess文件访问
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

    #https的配置方案
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

}
```

### 必要的全局配置选项

```nginx
user  nobody;  #启动Nginx工作进程的用户和组
worker_processes  auto;  #启动Nginx工作进程的数量，auto：自动根据当前服务器的核数来定义

worker_cpu_affinity 00000001 00000010 00000100 00001000; #将Nginx⼯作进程绑定到指定的CPU核⼼，默认Nginx是不进⾏进程绑定的，绑定并不是意味着当前nginx进程独占⼀核⼼CPU，但是可以保证此进程不会运⾏在其他核⼼上，这就极⼤减少了nginx的⼯作进程在不同的cpu核⼼上的来回跳转，减少了CPU对进程的资源分配与 回收以及内存管理等，因此可以有效的提升nginx服务器的性能。

error_log   "/worker/log/error.log"   warn;  #错误日志记录配置
pid         "/worker/pid/nginx.pid";  #pid文件保存路径

worker_priority 0; #⼯作进程nice值，-20~19 
worker_rlimit_nofile 65536; #这个数字包括Nginx的所有连接（例如与代理服务器的连接等），⽽不仅仅是与客⼾端的连接,另⼀个考虑因素是实际的并发连接数不能超过系统级别的最⼤打开⽂件数的限制。最好与ulimit -n或者limits.conf的值保持一致

daemon off; #前台运⾏Nginx服务⽤于测试、docker等环境。

events {
  use epoll;  #使用epoll事件驱动，Nginx支持众多的时间驱动，比如select、poll、epoll，只能设置在events模块中设置
  worker_connections  65536;  #设置单个工作进程的最大并发连接数
  
  accept_mutex on;  #优化同⼀时刻只有⼀个请求⽽避免多个睡眠进程被唤醒的设置，on为防⽌被同时唤醒默认为off，全部唤醒的过程也成为"惊群"，因此nginx刚安装完以后要进⾏适当的优化。 
  multi_accept on;  #Nginx服务器的每个⼯作进程可以同时接受多个新的⽹络连接，但是需要在配置⽂件中配置，此指令默认为关闭，即默认为⼀个⼯作进程只能⼀次接受⼀个新的⽹络连接，打开后⼏个同时接受多个。
}

http {
  include       mime.types;  #导入支持的文件类型
  default_type  application/octet-stream;  #设置默认的类型，会提示下载不匹配的类型文件

  #日志配置部分
  log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                        '$status $body_bytes_sent "$http_referer" '
                        '"$http_user_agent" "$http_x_forwarded_for"';

  access_log  "/worker/log/access.log"  main;  #定义访问日志的路径
  
  server_tokens off;  #是否在响应报文的server首部显示nginx版本，off表示不显示，默认值为on

  server_names_hash_bucket_size 128;
  client_header_buffer_size 4k;
  large_client_header_buffers 4 32k;
  client_max_body_size 20m;  #设置允许客户端上传单个文件的最大值，默认值为1m，上传文件超过此值会出现413错误
  client_body_buffer_size 4m;  #用于接收每个客户端请求报文的body部分的缓冲大小。默认为16K。超出此大小时，其将被暂存到磁盘上的由下面client_body_temp_path指令所定义的位置
  client_body_temp_path path;  #设定存储客户端请求报文的body部分的临时存储路径及子目录结构和数量，目录名为16进制的数字，使用hash之后的值从后往前截取1位、2位、2位作为目录名。

  sendfile        on;  #实现文件零拷贝
  tcp_nopush      on;  #在开启了sendfile的情况下，合并请求后统⼀发送给客⼾端
  tcp_nodelay     on;  #在开启了keepalived模式下的连接是否启⽤TCP_NODELAY选项，当为off时，延迟0.2s发送，默认On时，不延迟发送，⽴即发送⽤⼾相应报⽂
  keepalive_timeout 65 65; #设置会话保持时间，0表示禁止长连接，默认为75s，通常配置在http字段作为站点全局配置
  keepalive_requests 3;  #在一次长连接上所允许请求的资源的最大数量，默认为100次，建议适当调大，比如：500
  #开启长连接后，返回客户端的会话保持时间为60s，单次长连接累计请求达到指定次数请求或65秒就会被断开，后面的65是发送给客户端响应报文头部中显示的超时时间，如果不设置客户端将不显示超时时间。
  

  gzip on;  #开启文件压缩
  gzip_min_length 1k;
  gzip_buffers 4 16k;
  gzip_http_version 1.0;
  gzip_comp_level 2;
  gzip_types text/plain application/x-javascript text/css application/xml;
  gzip_vary on;

  server {
    listen  80;  #设置监听地址和端口
    server_name www.test.com;  #定义主机名
    charset utf-8; #设置编码格式，默认是俄语格式，可以改为utf-8
    location ~ \.php$ {  #以http的⽅式转发php请求到指定web服务器
      root            /project/src;
      fastcgi_pass    127.0.0.1:9000;
      fastcgi_index   index.php;
      fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
      include         fastcgi.conf;
    }
    location / {
      root            /static;
    }
  }
}
```

### location配置

在一个server中location配置段可存在多个，用于实现uri到文件系统的路径映射。nginx会根据用户请求的URI来检查定义的所有location，按一定的优化级找出一个最佳匹配，而后应用其配置。

在没有使用正则表达式的时候，nginx会先在server中的多个location选取匹配度最高的一个uri，uri是用户请求的字符串，即域名后面的web文件路径，然后使用该location模块中的正则url和字符串，如果匹配成功就结束搜索，并使用此location处理此请求。

```nginx
#语法规则
= 严格匹配。如果这个查询匹配，那么将停止搜索并立即处理此请求。
~ 为区分大小写匹配(可用正则表达式)
!~为区分大小写不匹配
~* 为不区分大小写匹配(可用正则表达式)
!~*为不区分大小写不匹配
^~ 如果把这个前缀用于一个常规字符串,那么告诉nginx 如果路径匹配那么不测试正则表达式。
不带符号  匹配起始于此uri的所有的uri

#匹配优先级从高到低
= ^~ ~/~* 不带符号
```

举个例子，有如下匹配规则：

```nginx
location = / {
   #规则A
}
location = /login {
   #规则B
}
location ^~ /static/ {
   #规则C
}
location ~ \.(gif|jpg|png|js|css)$ {
   #规则D
}
location ~* \.png$ {
   #规则E
}
location !~ \.xhtml$ {
   #规则F
}
location !~* \.xhtml$ {
   #规则G
}
location / {
   #规则H
}

那么产生的效果如下：
访问根目录/， 比如http://localhost/ 将匹配规则A

访问 http://localhost/login 将匹配规则B，http://localhost/register 则匹配规则H

访问 http://localhost/static/a.html 将匹配规则C

访问 http://localhost/a.gif, http://localhost/b.jpg 将匹配规则D和规则E，但是规则D顺序优先，规则E不起作用，而 http://localhost/static/c.png 则优先匹配到 规则C

访问 http://localhost/a.PNG 则匹配规则E， 而不会匹配规则D，因为规则E不区分大小写。

访问 http://localhost/a.xhtml 不会匹配规则F和规则G，http://localhost/a.XHTML不会匹配规则G，因为不区分大小写。规则F，规则G属于排除法，符合匹配规则但是不会匹配到，所以想想看实际应用中哪里会用到。

访问 http://localhost/category/id/1111 则最终匹配到规则H，因为以上规则都不匹配，这个时候应该是nginx转发请求给后端应用服务器，比如FastCGI（php），tomcat（jsp），nginx作为方向代理服务器存在
```

实际的生产环境：

```nginx
#直接匹配网站根，通过域名访问网站首页比较频繁，使用这个会加速处理，官网如是说。
#这里是直接转发给后端应用服务器了，也可以是一个静态首页
# 第一个必选规则
location = / {
    proxy_pass http://tomcat:8080/index
}

# 第二个必选规则是处理静态文件请求，这是nginx作为http服务器的强项
# 有两种配置模式，目录匹配或后缀匹配,任选其一或搭配使用
location ^~ /static/ {
    root /webroot/static/;
}
location ~* \.(gif|jpg|jpeg|png|css|js|ico)$ {
    root /webroot/res/;
}

#第三个规则就是通用规则，用来转发动态请求到后端应用服务器
#非静态文件请求就默认是动态请求，自己根据实际把握
#毕竟目前的一些框架的流行，带.php,.jsp后缀的情况很少了
location / {
    proxy_pass http://tomcat:8080/
}
```

## Nginx虚拟主机实战

### 什么是虚拟主机

虚拟主机就是多个网站使用同一个物理服务器，每一个服务器都可以单独的向外界提供服务，互不影响。虚拟主机之间完全独立，在外界看来，每一台虚拟主机都和一台独立的主机表现的完全一致。

虚拟主机的出现，对互联网来说是一个非常重要的贡献，由于可以让多个虚拟主机共享一台物理主机的所有资源，所以让每个用户承担的成本也大大的降低了。

Nginx作为一个广泛应用的Web服务器，支持三种形式的虚拟主机配置：

- 基于端口号的主机配置
- 基于ip的主机配置
- 基于域名的主机配置

### 基于端口号的主机配置

所谓基于端口的主机配置就是多个虚拟主机的ip地址相同，但是它们的端口不同，我们可以通过端口号来访问不同的虚拟主机。我们以8081, 8082,8083三个端口为例来了解这种配置。

**为每一个端口号创建目录**

1）在nginx安装位置的html/目录下面创建port子目录

2）在port下面创建8081,8082,8083三个子目录

3）每个子目录分别创建各个端口的index.html文件

- 8081/index.html内容：port 8081
- 8082/index.html内容：port 8082
- 8083/index.html内容：port 8083

**修改配置文件**

修改nginx.conf文件

```nginx
server {
    listen       8081;
    server_name  localhost;


    location / {
        root   html/port/8081/;
        index  index.html index.htm;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }

}

server {
    listen       8082;
    server_name  localhost;

    location / {
        root   html/port/8082/;
        index index.htm index.html;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }

}

server {
    listen       8083;
    server_name  localhost;

    location / {
        root html/port/8083/;
        index inde.htm index.html;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```

### 基于IP的虚拟主机

所谓基于IP的虚拟主机就是将虚拟主机绑定到不同的IP地址。我们可以通过不同的IP地址访问不同的虚拟主机。但是这种模式企业里面用的比较少。

一般情况下，我们自己的电脑都只会有最多两个网卡，我们可以虚拟多出几个ip，针对每一个IP，设置配置文件

### 基于域名的虚拟主机

所谓基于域名的虚拟主机是非常广泛的一种使用方式，绝大多数公司都是使用该种方式配置的虚拟主机。

**配置域名** 

在/etc/hosts（windows是C:\Windows\System32\drivers\etc\hosts）文件中配置三个域名：

```plaintext
127.0.0.1   www.hr.com
127.0.0.1   www.platmis.com
127.0.0.1   www.napi.com
```

**为域名创建目录及文件**

我们在html目录为每个域名创建一个单独的目录，创建index.html文件。

修改nginx.conf文件：

```nginx
user  nobody;
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;


    sendfile        on;

    keepalive_timeout  65;


    server {
        listen       80;
        server_name  www.hr.com;


        location / {
            root   html/domain/hr/;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

    server {
        listen       80;
        server_name  www.platmis.com;

        location / {
            root   html/domain/platmis/;
            index index.htm index.html;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

    server {
        listen       80;
        server_name  www.napi.com;

        location / {
            root html/domain/napi/;
            index inde.htm index.html;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

### root和alias

root：指定web的家⽬录，在定义location的时候，⽂件的绝对路径等于 root+location，如：

```nginx
server { 
   listen 80; 
   server_name www.magedu.net; 
   location / { 
       root /data/nginx/html/pc; 
       }
   location /about { 
       root /data/nginx/html/pc; #必须要在html⽬录中创建⼀个about⽬录才可以访问，否则报错。 
       index index.html; 
       } 
}
```

alias：定义路径别名，会把访问的路径重新定义到其指定的路径，如：

```nginx
server { 
    listen 80; 
    server_name www.magedu.net; 
    location / { 
        root /data/nginx/html/pc; 
    }
    location /about { #使⽤alias的时候uri后⾯如果加了斜杠则下⾯的路径配置必须加斜杠，否则403 
        alias /data/nginx/html/pc; #当访问about的时候，会显⽰alias定义的/data/nginx/html/pc⾥⾯的内容。
        index index.html; 
    } 
}
```

## Nginx应用场景

### HTTP服务器

Nginx本身也是一个静态资源的服务器，当只有静态资源的时候，就可以使用Nginx来做服务器，如果一个网站只是静态页面的话，那么就可以通过这种方式来实现部署。

首先在文档根目录/usr/local/var/www下创建html目录, 然后在html中放一个test.html;

配置nginx.conf中的server：

```nginx
user mengday staff;

http {
    server {
        listen       80;
        server_name  localhost;
        client_max_body_size 1024M;

        # 默认location
        location / {
            root   /usr/local/var/www/html;
            index  index.html index.htm;
        }
    }
}
```

> 注意：如果访问出现403 Forbidden错误，可能是因为nginx.conf 的第一行user配置不对，默认是#user  nobody;是注释的，linux下改成user root; macos下改成user 用户名 所在组;  然后重新加载配置文件或者重启，再试一下就可以了， 用户名可以通过who am i 命令来查看。

### 静态服务器

在公司中经常会遇到静态服务器，通常会提供一个上传的功能，其他应用如果需要静态资源就从该静态服务器中获取。

在/usr/local/var/www 下分别创建images和img目录，分别在每个目录下放一张test.jpg

```nginx
http {
    server {
        listen       80;
        server_name  localhost;


        set $doc_root /usr/local/var/www;

        # 默认location
        location / {
            root   /usr/local/var/www/html;
            index  index.html index.htm;
        }

        location ^~ /images/ {
            root $doc_root;
       }

       location ~* \.(gif|jpg|jpeg|png|bmp|ico|swf|css|js)$ {
           root $doc_root/img;
       }
    }
}
```

自定义变量使用set指令，语法：set 变量名值 引用使用变量名值

引用使用变量名，这里自定义了doc_root变量。

静态服务器location的映射一般有两种方式：

- 使用路径，如 /images/ 一般图片都会放在某个图片目录下，
- 使用后缀，如 .jpg、.png 等后缀匹配模式

访问http://localhost/test.jpg 会映射到 $doc_root/img

访问http://localhost/images/test.jpg 当同一个路径满足多个location时，优先匹配优先级高的location，由于^~ 的优先级大于 ~, 所以会走/images/对应的location

常见的location路径映射路径有以下几种：

- =   进行普通字符精确匹配。也就是完全匹配。
- ^~   前缀匹配。如果匹配成功，则不再匹配其他location。
- ~   表示执行一个正则匹配，区分大小写
- ~*   表示执行一个正则匹配，不区分大小写
- /xxx/ 常规字符串路径匹配
- /   通用匹配，任何请求都会匹配到

> location优先级从高到底：
>
> (location =) > (location 完整路径) > (location ^~ 路径) > (location ~,~* 正则顺序) > (location 部分起始路径) > (/)

```nginx
location = / {
    # 精确匹配/，主机名后面不能带任何字符串 /
    [ configuration A ]
}
location / {
    # 匹配所有以 / 开头的请求。
    # 但是如果有更长的同类型的表达式，则选择更长的表达式。
    # 如果有正则表达式可以匹配，则优先匹配正则表达式。
    [ configuration B ]
}
location /documents/ {
    # 匹配所有以 /documents/ 开头的请求，匹配符合以后，还要继续往下搜索。
    # 但是如果有更长的同类型的表达式，则选择更长的表达式。
    # 如果有正则表达式可以匹配，则优先匹配正则表达式。
    [ configuration C ]
}
location ^~ /images/ {
    # 匹配所有以 /images/ 开头的表达式，如果匹配成功，则停止匹配查找，停止搜索。
    # 所以，即便有符合的正则表达式location，也不会被使用
    [ configuration D ]
}

location ~* \.(gif|jpg|jpeg)$ {
    # 匹配所有以 gif jpg jpeg结尾的请求。
    # 但是 以 /images/开头的请求，将使用 Configuration D，D具有更高的优先级
    [ configuration E ]
}

location /images/ {
    # 字符匹配到 /images/，还会继续往下搜索
    [ configuration F ]
}


location = /test.htm {
    root   /usr/local/var/www/htm;
    index  index.htm;
}
```

注意：location的优先级与location配置的位置无关。

### 反向代理

反向代理应该是Nginx使用最多的功能了，反向代理(Reverse  Proxy)方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

简单来说就是真实的服务器不能直接被外部网络访问，所以需要一台代理服务器，而代理服务器能被外部网络访问的同时又跟真实服务器在同一个网络环境，当然也可能是同一台服务器，端口不同而已。

反向代理通过proxy_pass指令来实现。

启动一个Java Web项目，端口号为8081

```nginx
server {
    listen       80;
    server_name  localhost;

    location / {
        proxy_pass http://localhost:8081;
        proxy_set_header Host $host:$server_port;
        # 设置用户ip地址
        proxy_set_header X-Forwarded-For $remote_addr;
        # 当请求服务器出错去寻找其他服务器
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503;
    }

}
```

当我们访问localhost的时候，就相当于访问 localhost:8081了。

### 负载均衡

负载均衡也是Nginx常用的一个功能，负载均衡其意思就是分摊到多个操作单元上进行执行，例如Web服务器、FTP服务器、企业关键应用服务器和其它关键任务服务器等，从而共同完成工作任务。

简单而言就是当有2台或以上服务器时，根据规则随机的将请求分发到指定的服务器上处理，负载均衡配置一般都需要同时配置反向代理，通过反向代理跳转到负载均衡。而Nginx目前支持自带3种负载均衡策略，还有2种常用的第三方策略。

负载均衡通过upstream指令来实现。

**RR(round robin：轮询 默认)**

每个请求按时间顺序逐一分配到不同的后端服务器，也就是说第一次请求分配到第一台服务器上，第二次请求分配到第二台服务器上，如果只有两台服务器，第三次请求继续分配到第一台上，这样循环轮询下去，也就是服务器接收请求的比例是 1:1， 如果后端服务器down掉，能自动剔除。轮询是默认配置，不需要太多的配置。

同一个项目分别使用8081和8082端口启动项目。

```nginx
upstream web_servers {
    server localhost:8081;
    server localhost:8082;
}

server {
    listen       80;
    server_name  localhost;
    #access_log  logs/host.access.log  main;


    location / {
        proxy_pass http://web_servers;
        # 必须指定Header Host
        proxy_set_header Host $host:$server_port;
    }
}
```

**权重**

指定轮询几率，weight和访问比率成正比, 也就是服务器接收请求的比例就是各自配置的weight的比例，用于后端服务器性能不均的情况,比如服务器性能差点就少接收点请求，服务器性能好点就多处理点请求。

```nginx
upstream test {
    server localhost:8081 weight=1;
    server localhost:8082 weight=3;
    server localhost:8083 weight=4 backup;
}
```

**ip_hash**

上面的2种方式都有一个问题，那就是下一个请求来的时候请求可能分发到另外一个服务器，当我们的程序不是无状态的时候(采用了session保存数据)，这时候就有一个很大的很问题了，比如把登录信息保存到了session中，那么跳转到另外一台服务器的时候就需要重新登录了，所以很多时候我们需要一个客户只访问一个服务器，那么就需要用iphash了，iphash的每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。

```nginx
upstream test {
    ip_hash;
    server localhost:8080;
    server localhost:8081;
}
```

**fair(第三方)**

按后端服务器的响应时间来分配请求，响应时间短的优先分配。这个配置是为了更快的给用户响应。

```nginx
upstream backend {
    fair;
    server localhost:8080;
    server localhost:8081;
}
```

**url_hash(第三方)**

按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。在upstream中加入hash语句，server语句中不能写入weight等其他的参数，hash_method是使用的hash算法

```nginx
upstream backend {
    hash $request_uri;
    hash_method crc32;
    server localhost:8080;
    server localhost:8081;
}
```

以上5种负载均衡各自适用不同情况下使用，所以可以根据实际情况选择使用哪种策略模式,不过fair和url_hash需要安装第三方模块才能使用。

### 动静分离

动静分离是让动态网站里的动态网页根据一定规则把不变的资源和经常变的资源区分开来，动静资源做好了拆分以后，我们就可以根据静态资源的特点将其做缓存操作，这就是网站静态化处理的核心思路。

```nginx
upstream web_servers {
    server localhost:8081;
    server localhost:8082;
}

server {
    listen       80;
    server_name  localhost;

    set $doc_root /usr/local/var/www;

    location ~* \.(gif|jpg|jpeg|png|bmp|ico|swf|css|js)$ {
        root $doc_root/img;
    }

    location / {
        proxy_pass http://web_servers;
        # 必须指定Header Host
        proxy_set_header Host $host:$server_port;
    }

    error_page 500 502 503 504  /50x.html;
    location = /50x.html {
        root $doc_root;
    }

}
```

## 其他

**return指令**

返回http状态码 和 可选的第二个参数可以是重定向的URL

```nginx
location /permanently/moved/url {
    return 301 http://www.example.com/moved/here;
}
```

**rewrite指令**

重写URI请求 rewrite，通过使用rewrite指令在请求处理期间多次修改请求URI，该指令具有一个可选参数和两个必需参数。

第一个(必需)参数是请求URI必须匹配的正则表达式。

第二个参数是用于替换匹配URI的URI。

可选的第三个参数是可以停止进一步重写指令的处理或发送重定向(代码301或302)的标志

```nginx
location /users/ {
    rewrite ^/users/(.*)$ /show?user=$1 break;
}
```

**error_page指令**

使用error_page指令，您可以配置NGINX返回自定义页面以及错误代码，替换响应中的其他错误代码，或将浏览器重定向到其他URI。在以下示例中，error_page指令指定要返回404页面错误代码的页面(/404.html)。

```nginx
error_page 404 /404.html;
```

**日志**

访问日志：需要开启压缩 gzip on; 否则不生成日志文件，打开log_format、access_log注释

```nginx
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

access_log  /usr/local/etc/nginx/logs/host.access.log  main;

gzip  on;
```

**deny命令**

```nginx
# 禁止访问某个目录
location ~* \.(txt|doc)${
    root $doc_root;
    deny all;
}
```

**内置变量**

nginx的配置文件中可以使用的内置变量以美元符$开始，也有人叫全局变量。其中，部分预定义的变量的值是可以改变的。

- $args ：#这个变量等于请求行中的参数，同$query_string
- $content_length ：请求头中的Content-length字段。
- $content_type ：请求头中的Content-Type字段。
- $document_root ：当前请求在root指令中指定的值。
- $host ：请求主机头字段，否则为服务器名称。
- $http_user_agent ：客户端agent信息
- $http_cookie ：客户端cookie信息
- $limit_rate ：这个变量可以限制连接速率。
- $request_method ：客户端请求的动作，通常为GET或POST。
- $remote_addr ：客户端的IP地址。
- $remote_port ：客户端的端口。
- $remote_user ：已经经过Auth Basic Module验证的用户名。
- $request_filename ：当前请求的文件路径，由root或alias指令与URI请求生成。
- $scheme ：HTTP方法（如http，https）。
- $server_protocol ：请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
- $server_addr ：服务器地址，在完成一次系统调用后可以确定这个值。
- $server_name ：服务器名称。
- $server_port ：请求到达服务器的端口号。
- $request_uri ：包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。
- $uri ：不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
- $document_uri ：与$uri相同