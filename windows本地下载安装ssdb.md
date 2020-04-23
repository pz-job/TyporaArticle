### 官方下载

<http://ssdb.io/docs/install.html> 这是官方网站

官方建议

>Do not run SSDB server on Windows system for a production environment. If you wish to stick with Windows system, please run a Linux virtual machine on Windows, and run SSDB server on that Linux.
>
>大致就是说不建议win下面安装，如果坚持用win系统，就将ssdb放在虚拟机上运行

我们点击github的源代码去点击releases就可以看到有四个版本

这里我们根据自身需求下载所需要的版本

![版本](D:\DevelopTools\Typora\md\pic\微信截图_20190716152521.png)

下载之后的文件我们进行解压

解压后会发现缺少运行文件，我们需要再去下载[运行文件](https://github.com/ideawu/ssdb-bin)

将下载好的文件放在我们我们第一个文件夹当中

然后在ssdb-server.exe 所在的目录下面运行cmd

执行命令：

```java
ssdb-server.exe ssdb.conf
```

当页面出现

```java
ssdb-server 1.9.4
Copyright (c) 2012-2015 ssdb.io

```

表示启动成功

如果我们外网访问不到服务的话我们需要去更改一下配置文件（ssdb.conf）

因为配置默认绑定的是本机ip，这里我们可以更换ip；我这里设置的是所有ip

```java
server:
	ip: 0.0.0.0
```

