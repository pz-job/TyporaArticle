

## nginx

####关于nginx的作用和好处

- 反向代理
- 动静分离
- 高并发
- 占用资源小
- 配置简单更加灵活

#### nginx 的安装和启动



略

#### nginx的动静分离使用

####nginx的配置说明



```shell
#第一部分 公共部分

#nginx用户，用户组，win下不指定
user  nobody;

#指定开启的进程数 默认为1 
worker_processes  1;

#指定日志路径和级别
error_log  logs/error.log;
error_log  logs/error.log  notice;
error_log  logs/error.log  info;

#进程运行文件存放地址
pid        logs/nginx.pid;	


#第二部分 events设定nginx的工作模式和连接数上线
events {
    #工作模式 linux下建议epoll，win下不指定
    use epoll;
    
	#每个进程允许的最大连接数
    worker_connections  1024;
}

#第三部分 http服务器设置
http {

	#默认类型和载入mime文件
    include       mime.types;
    default_type  application/octet-stream;

	#自定义格式
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
    
    #用log_format指定日志格式后，乣用access_log来指定日志存放的位置
    #access_log  logs/access.log  main;
    
    #是否调用sendfile函数来输出文件，一般为on
    sendfile        on;
    
    #允许或者禁止使用socket使用tcp_cork选型，在使用sendfile中使用
    #tcp_nopush     on;
    
    #连接超时
    #keepalive_timeout  0;
    keepalive_timeout  65;
    
    #开启gzip压缩
    #gzip  on;
    
    #负载均衡服务器列表
    upstream myserver{
    
    	 server 192.168.8.1:80
         server 192.168.8.2:80
    }
    
    
    server {
   		#监听端口和地址
        listen       80;
        server_name  localhost;
    
        #charset koi8-r;
    
        #access_log  logs/host.access.log  main;
    
        location / {
            root   html;
            index  index.html index.htm;
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

}

```



#### nginx的负载均衡 反向代理使用

- 正向代理：代理的是客户端
- 反向代理：代理的是服务端



listen属性详解

- 属性格式

| 形式            | 描述                          | 例子                      | 默认补全                  |
| :-------------- | :---------------------------- | :------------------------ | :------------------------ |
| ip地址+port端口 | 完整形式                      | listen 123.123.123.123:23 | listen 123.123.123.123:23 |
| 只有ip          | 会自动加上80的web监听端口     | listen 2.2.2.2            | listen 2.2.2.2:80         |
| 只有port        | 会自动加上0.0.0.0的全监听地址 | listen 45                 | listen 0.0.0.0:45         |

- 匹配规则
   第一步 当nginx匹配的时候，会将缩写的格式补全
   第二步 匹配描述的最精确的(缩写的没有完整的格式准确)
   第三步 如果第二步的情况下还有多个，那么listen就判断不出来了，交给server_name判断

```shell

worker_processes  1;

events {
    worker_connections  1024;
}

http {

#1.将我们需要用到的服务器配置在负载均衡模块
	upstream myserver{
		server 127.0.0.1:8081
		server 127.0.0.2:8082
		
		#这里对服务器的请求有五种算法
        #轮询(默认)：按照请求时间逐一分配到每个服务器上
        #weight(权重)：weight越大，分配到的请求越大 
        	#server 127.0.0.1:8081 weight=1
        	#server 127.0.0.2:8082 weight=2
        #ip_hash(地址)：同一个ip的访客固定一个服务器，可以解决session问题
        	#ip_hash 
        	#server 127.0.0.1:8081
			#server 127.0.0.2:8082
        #fair：根据后台响应时间来分发请求，响应时间短的分发的请求多。
        #url_hash：按照每个请求的url地址来分配服务器请求
	}

#虚拟主机
   server {
  		#2.配置需要监听的端口和服务名
       listen       8080;
       #指定ip地址或者域名，多个用空格分开
       server_name  localhost 127.0.0.1;
       
       
       #访问的默认页面
       #index 
       
      #3.匹配值需要拦截后台请求
      location / {
     	#拦截到交给服务器处理
        proxy_pass  http://myserver;
        #proxy_set_header X-Real-IP $remote_addr;
      }

      #拦截静态资源 
      location ~ .*\.(html|htm|gif|jpg|jpeg|bmp|png|ico|js|css)$ {
      #root 指定目录
        root /Users/dalaoyang/Downloads/static;
       }

    }

}
```

