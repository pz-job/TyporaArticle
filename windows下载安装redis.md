### 下载地址

我们这里下载的是windows版本的

官方上已经没有windows版本下载的入口了 所以我们需要去github去下载

> 下载地址：<https://github.com/MicrosoftArchive/redis/releases>

然后我们选择我们需要的版本进行下载解压

解压之后我们可以对配置做下操作

打开文件**redis.windows.conf**

- 设置密码 查找 requirepass  

  ```java
  requirepass  password 
  ```

- 设置绑定ip (默认设置的为本机ip) 

  ```jav
  bind 0.0.0.0 (表示所有ip都可访问)
  ```

  启动服务

  在服务目录下进入cmd窗口并输入下面命令

  ```java
  redis-server.exe redis.windows.conf
  ```

  启动后我们可以去双击redis-cli.exe 

  ```java
  auth password
  ```

  ok 即为成功