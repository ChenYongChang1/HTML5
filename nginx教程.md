# 教程
1. mac 安装

	1. 安装
	
	    ```
	    brew search nginx
	    brew install nginx
	    //查看版本
	    nginx -v
	    ```
	2. 目录
	
	    ```
	    默认项目目录
	    /usr/local/nginx/html
	    
	    nginx.conf目录
	    /usr/local/etc/nginx
	    
	    
	    ```
2. linux 安装

	1. 开始前，请确认gcc g++开发类库是否装好，默认已经安装

		安装make：

				yum -y install gcc automake autoconf libtool make
			
		安装g++:

				yum install gcc gcc-c++
			
		ububtu平台编译环境可以使用以下指令

			apt-get install build-essential
			apt-get install libtool
	2. 选择一个目录，用于下载和解压安装文件
	3. 安装PCRE库

		ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/ 下载最新的 PCRE 源		码包，使用下面命令下载编译和安装 PCRE 包：（本文参照下载文件版本：		pcre-8.37.tar.gz 经过验证未发现这个版本，若想下载最新版本请打开上面网址。本		文选择pcre-8.39.tar.gz）
		
			cd /usr/local/src
			wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.39.tar.gz 
			tar -zxvf pcre-8.37.tar.gz
			cd pcre-8.34
			./configure
			make
			make install
			
			或者
			./configure && make && make install
			

	3. 安装zlib库

		http://zlib.net/zlib-1.2.11.tar.gz 下载最新的 zlib 源码包，使用下面命令下载编译和安装 zlib包：（本文参照下载文件版本：zlib-1.2.8.tar.gz 经过验证未发现这个版本，若想下载最新版本请打开上面网址。本文选择zlib-1.2.11.tar.gz ）
		
			cd /usr/local/src
 
			wget http://zlib.net/zlib-1.2.11.tar.gz
			tar -zxvf zlib-1.2.11.tar.gz
			cd zlib-1.2.11
			./configure
			make
			make install
			
	2. 安装openssl（某些vps默认没装ssl)

			cd /usr/local/src
			wget https://www.openssl.org/source/openssl-1.0.1t.tar.gz
			tar -zxvf openssl-1.0.1t.tar.gz
			
	1. 安装nginx

			cd /usr/local/src
			wget http://nginx.org/download/nginx-1.1.10.tar.gz
			tar -zxvf nginx-1.1.10.tar.gz
			cd nginx-1.1.10
			./configure
			make
			make install
			
		**这里可能会出现报错**
		
			ubuntu下

				apt-get install openssl
				apt-get install libssl-dev
				
			centos下
				yum -y install openssl openssl-devel
	
	4. 启动nginx

		因为可能apeache占用80端口，apeache端口尽量不要修改，我们选择修改nginx端口。
		
		linux 修改路径/usr/local/nginx/conf/nginx.conf，Windows 下 安装目录\conf\nginx.conf。
		
		修改端口为8090，localhost修改为你服务器ip地址。
		
			sudo /usr/local/nginx/nginx
			
			
2. 常用命令

    ```
    nginx               启动（mac 启动1024以内端口需要授权：sudo nginx）
    nginx -s stop       快速关闭Nginx，可能不保存相关信息，并迅速终止web服务。
nginx -s quit       平稳关闭Nginx，保存相关信息，有安排的结束web服务。
nginx -s reload     因改变了Nginx相关配置，需要重新加载配置而重载。
nginx -s reopen     重新打开日志文件。
nginx -c filename   为 Nginx 指定一个配置文件，来代替缺省的。
nginx -t            不运行，而仅仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。
nginx -v            显示 nginx 的版本。
nginx -V            显示 nginx 的版本，编译器版本和配置参数。
    ```
3. 反向代理配置

    **配置文件路径：/usr/local/etc/nginx/nginx.conf**
    
    ```
        #运行用户
    #user somebody;
    
    #启动进程,通常设置成和cpu的数量相等
    worker_processes  1;
    
    #全局错误日志
    error_log  D:/Tools/nginx-1.10.1/logs/error.log;
    error_log  D:/Tools/nginx-1.10.1/logs/notice.log  notice;
    error_log  D:/Tools/nginx-1.10.1/logs/info.log  info;
    
    #PID文件，记录当前启动的nginx的进程ID
    pid        D:/Tools/nginx-1.10.1/logs/nginx.pid;
    
    #工作模式及连接数上限
    events {
        worker_connections 1024;    #单个后台worker process进程的最大并发链接数
    }
    
    #设定http服务器，利用它的反向代理功能提供负载均衡支持
    http {
        #设定mime类型(邮件支持类型),类型由mime.types文件定义
        include       D:/Tools/nginx-1.10.1/conf/mime.types;
        default_type  application/octet-stream;
        
        #设定日志
        log_format  main  '[$remote_addr] - [$remote_user] [$time_local] "$request" '
                          '$status $body_bytes_sent "$http_referer" '
                          '"$http_user_agent" "$http_x_forwarded_for"';
                          
        access_log    D:/Tools/nginx-1.10.1/logs/access.log main;
        rewrite_log     on;
        
        #sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，对于普通应用，
        #必须设为 on,如果用来进行下载等应用磁盘IO重负载应用，可设置为 off，以平衡磁盘与网络I/O处理速度，降低系统的uptime.
        sendfile        on;
        #tcp_nopush     on;
    
        #连接超时时间
        keepalive_timeout  120;
        tcp_nodelay        on;
        
        #gzip压缩开关
        #gzip  on;
     
        #设定实际的服务器列表 
        upstream zp_server1{
            server 127.0.0.1:8089;
        }
    
        #HTTP服务器
        server {
            #监听80端口，80端口是知名端口号，用于HTTP协议
            listen       80;
            
            #定义使用www.xx.com访问
            server_name  www.helloworld.com;
            
            #首页
            index index.html
            
            #指向webapp的目录
            root D:\01_Workspace\Project\github\zp\SpringNotes\spring-security\spring-shiro\src\main\webapp;
            
            #编码格式
            charset utf-8;
            
            #代理配置参数
            proxy_connect_timeout 180;
            proxy_send_timeout 180;
            proxy_read_timeout 180;
            proxy_set_header Host $host;
            proxy_set_header X-Forwarder-For $remote_addr;
    
            #反向代理的路径（和upstream绑定），location 后面设置映射的路径
            location / {
                proxy_pass http://zp_server1;
            } 
    
            #静态文件，nginx自己处理
            location ~ ^/(images|javascript|js|css|flash|media|static)/ {
                root D:\01_Workspace\Project\github\zp\SpringNotes\spring-security\spring-shiro\src\main\webapp\views;
                #过期30天，静态文件不怎么更新，过期可以设大一点，如果频繁更新，则可以设置得小一点。
                expires 30d;
            }
        
            #设定查看Nginx状态的地址
            location /NginxStatus {
                stub_status           on;
                access_log            on;
                auth_basic            "NginxStatus";
                auth_basic_user_file  conf/htpasswd;
            }
        
            #禁止访问 .htxxx 文件
            location ~ /\.ht {
                deny all;
            }
            
            #错误处理页面（可选择性配置）
            #error_page   404              /404.html;
            #error_page   500 502 503 504  /50x.html;
            #location = /50x.html {
            #    root   html;
            #}
        }
    }
    ```
    
    代理的写法
    
    ```
        第一种：
    location /proxy/ {
         proxy_pass http://127.0.0.1:81/;
    }
    会被代理到http://127.0.0.1:81/test.html 这个url
     
        第二种(相对于第一种，最后少一个 /)
  
    location /proxy/ {
         proxy_pass http://127.0.0.1:81;
    }
    会被代理到http://127.0.0.1:81/proxy/test.html 这个url
     
        第三种：
    location /proxy/ {
         proxy_pass http://127.0.0.1:81/ftlynx/;
    }
    会被代理到http://127.0.0.1:81/ftlynx/test.html 这个url。
     
        第四种情况(相对于第三种，最后少一个 / )：
    location /proxy/ {
         proxy_pass http://127.0.0.1:81/ftlynx;
    }
    ```
4. 网站有多个webapp的配置

    当一个网站功能越来越丰富时，往往需要将一些功能相对独立的模块剥离出来，独立维护。这样的话，通常，会有多个 webapp。
    举个例子：假如 www.helloworld.com 站点有好几个webapp，finance（金融）、product（产品）、admin（用户中心）。访问这些应用的方式通过上下文(context)来进行区分:

    www.helloworld.com/finance/
    
    www.helloworld.com/product/
    
    www.helloworld.com/admin/
    
    我们知道，http的默认端口号是80，如果在一台服务器上同时启动这3个 webapp 应用，都用80端口，肯定是不成的。所以，这三个应用需要分别绑定不同的端口号。
    
    那么，问题来了，用户在实际访问 www.helloworld.com 站点时，访问不同 webapp，总不会还带着对应的端口号去访问吧。所以，你再次需要用到反向代理来做处理。
    
    ```
    http {
        #此处省略一些基本配置
        
        upstream product_server{
            server www.helloworld.com:8081;
        }
        
        upstream admin_server{
            server www.helloworld.com:8082;
        }
        
        upstream finance_server{
            server www.helloworld.com:8083;
        }
    
        server {
            #此处省略一些基本配置
            #默认指向product的server
            location / {
                proxy_pass http://product_server;
            }
    
            location /product/{
                proxy_pass http://product_server;
            }
    
            location /admin/ {
                proxy_pass http://admin_server;
            }
            
            location /finance/ {
                proxy_pass http://finance_server;
            }
        }
        }
    ```
5. 代理多个服务器

    ```
    server
    {
        listen 80;
        server_name product.localhost;
        location / {
             index index.html;
             root  /Users/lzhan/Lzhan/Angular2_0217/jobapp.com/dist/;
        }
    
    }
    server {
        listen       80;
        server_name  localhost;
        ...

    ```
6. 负载均衡配置

    上一个例子中，代理仅仅指向一个服务器。
    
    但是，网站在实际运营过程中，多半都是有多台服务器运行着同样的app，这时需要使用负载均衡来分流。
    
    nginx也可以实现简单的负载均衡功能。
    
    假设这样一个应用场景：将应用部署在 192.168.1.11:80、192.168.1.12:80、192.168.1.13:80 三台linux环境的服务器上。网站域名叫 www.helloworld.com，公网IP为 192.168.1.11。在公网IP所在的服务器上部署 nginx，对所有请求做负载均衡处理。
    
    nginx.conf 配置如下：

    ```
        http {
         #设定mime类型,类型由mime.type文件定义
        include       /etc/nginx/mime.types;
        default_type  application/octet-stream;
        #设定日志格式
        access_log    /var/log/nginx/access.log;
    
        #设定负载均衡的服务器列表
        upstream load_balance_server {
            #weigth参数表示权值，权值越高被分配到的几率越大
            server 192.168.1.11:80   weight=5;
            server 192.168.1.12:80   weight=1;
            server 192.168.1.13:80   weight=6;
        }
    
       #HTTP服务器
       server {
            #侦听80端口
            listen       80;
            
            #定义使用www.xx.com访问
            server_name  www.helloworld.com;
    
            #对所有请求进行负载均衡请求
            location / {
                root        /root;                 #定义服务器的默认网站根目录位置
                index       index.html index.htm;  #定义首页索引文件的名称
                proxy_pass  http://load_balance_server ;#请求转向load_balance_server 定义的服务器列表
    
                #以下是一些反向代理的配置(可选择性配置)
                #proxy_redirect off;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
                proxy_set_header X-Forwarded-For $remote_addr;
                proxy_connect_timeout 90;          #nginx跟后端服务器连接超时时间(代理连接超时)
                proxy_send_timeout 90;             #后端服务器数据回传时间(代理发送超时)
                proxy_read_timeout 90;             #连接成功后，后端服务器响应时间(代理接收超时)
                proxy_buffer_size 4k;              #设置代理服务器（nginx）保存用户头信息的缓冲区大小
                proxy_buffers 4 32k;               #proxy_buffers缓冲区，网页平均在32k以下的话，这样设置
                proxy_busy_buffers_size 64k;       #高负荷下缓冲大小（proxy_buffers*2）
                proxy_temp_file_write_size 64k;    #设定缓存文件夹大小，大于这个值，将从upstream服务器传
                
                client_max_body_size 10m;          #允许客户端请求的最大单文件字节数
                client_body_buffer_size 128k;      #缓冲区代理缓冲用户端请求的最大字节数
            }
        }
    }

```

---

3. 安装方式二

1. 在nginx官方网站下载一个rpm包，
    下载地址是：http://nginx.org/en/download.html
    wget http://nginx.org/packages/centos/6/noarch/RPMS/nginx-release-centos-6-0.el6.ngx.noarch.rpm

2. 安装这个rpm包

    rpm -ivh nginx-release- centos-6-0.el6.ngx.noarch.rpm 

    安装过程中会出现错误提示：

    warning: nginx-release-centos-6-0.el6.ngx.noarch.rpm: Header V4 RSA/SHA1 Signature, key ID 7bd9bf62: NOKEY

    不知道干什么的，忽略即可

3 开始正式安装nginx

    yum install nginx

    会显示一大堆信息，问你ok不ok啊：Is this ok [y/N]:

输入y，屏幕滚了一会之后就安装完毕，最后提示“Complete!”就是安完了。

4 nginx的几个默认目录

    whereis nginx
    nginx: /usr/sbin/nginx /etc/nginx /usr/share/nginx

1 配置所在目录：/etc/nginx/
2 PID目录：/var/run/nginx.pid
3 错误日志：/var/log/nginx/error.log
4 访问日志：/var/log/nginx/access.log
5 默认站点目录：/usr/share/nginx/html

5 常用命令

1 启动nginx：nginx
2 重启nginx：killall -HUP nginx
3 测试nginx配置：nginx -t

6 Nginx无法站外访问？

刚安装好nginx一个常见的问题是无法站外访问，本机wget、telnet都正常。而服务器之外，不管是局域网的其它主机还是互联网的主机都无法访问站点。如果用telnet的话，提示：

正在连接到192.168.0.xxx...不能打开到主机的连接， 在端口 80: 连接失败

如果用wget命令的话，提示：

Connecting to 192.168.0.100:80... failed: No route to host.

如果是以上的故障现象，很可能是被CentOS的防火墙把80端口拦住了，尝试执行以下命令，打开80端口：

iptables -I INPUT -p tcp --dport 80 -j ACCEPT

然后用：

/etc/init.d/iptables status

查看当前的防火墙规则，如果发现有这样一条：

ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:80

就说明防火墙规则已经添加成功了，再在站外访问就正常了。

  