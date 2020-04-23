##安装和使用nginx-rtmp视频流插件

### win下安装带有rtmp插件的nginx

将压缩包解压即可

### 启动

**启动**：start nginx

终端窗口进入安装目录下

启动：start nginx

**终止**：nginx -s stop

**重新载入nginx配置**：nginx -s reload

启动成功后输入localhost:8090 查看是否启动成功

### 配置nginx的配置文件

首先在config文件夹下找到nginx.config文件

先将配置文件备份之后在进行使用

nginx的简单配置详情：

~~~shell
worker_processes  1;

error_log  logs/error.log debug;

events {
    worker_connections  1024;
}

#设置rtmp服务器参数
rtmp {
    server {
        listen 1935; #监听的端口号
		
		#点播
		application vod{
		#配置点播路径
			play temp/rec;
		}

		#录制
        application live {
            live on;#开启rtmp直播功能
			record  all; #开启录制功能
			record_path temp/rec; #录制视频存放的地址
			record_unique on; #将当前时间戳添加到录制的文件
        }
        
		#直播
        application hls {
            live on; #开启rtmp直播功能
            hls on;  #开启hls功能
            hls_path temp/hls;  #设置HLS播放列表和片段目录
            hls_fragment 8s;   #设置片段长度
			hls_cleanup off; #hls清理，默认启用
			hls_nested on; #为每个建立子目录
        }
    }
}



http {
    server {
        listen      9080;
		server_name localhost,47.111.3.184;
		
        location / {
            root html;
        }
		
        location /stat {
            rtmp_stat all;
            rtmp_stat_stylesheet stat.xsl;
        }

        location /stat.xsl {
            root html;
        }
		
        location /hls {  
            #server hls fragments  
            types{  
                application/vnd.apple.mpegurl m3u8;  
                video/mp2t ts;  
            }  
            alias temp/hls;  
            expires -1;  
        }  
    }
}

~~~

配置完成之后我们进行访问

#####直播访问：

推流链接：rtmp://localhost/hls/ss

访问链接：rtmp://localhost/hls/ss



##### 点播访问：

配置好 vod 之后

我们通过以下链接进行访问

rtmp://ip:1935/vod/文件名

