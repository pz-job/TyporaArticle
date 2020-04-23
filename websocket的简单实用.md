

#####说一下实用springboot搭建一个简单的websocket 的实时推送应用

#### websocket是什么

> **WebSocket**是一种在单个[TCP](https://baike.baidu.com/item/TCP)连接上进行[全双工](https://baike.baidu.com/item/%E5%85%A8%E5%8F%8C%E5%B7%A5)通信的协议

> 我们以前用的http协议只能单向的浏览器给服务器发请求，然后服务器再去相应返回数据。
>
> websocket呢就是可以服务器主动给浏览器发数据

#### 优点

> 较少的控制开销
>
> 更强的实时性
>
> 保持连接状态
>
> 更好的二进制的支持
>
> 支持扩展
>
> 更换的压缩效果

#### pom文件	

springboot项目的话只需要下面这个依赖就可以了

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```



#### springboot的项目搭建

这里看我上一篇文章就不在多说了

#### 我们需要去注入ServerEndpointExporter

这是一个检测类型的bean 检测带注释@ServerEndpoint的bean并注册它们

稍后我们可以讲一下@configuration @bean 和@component @autowired 等注解

```java
package com.gzh;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.server.standard.ServerEndpointExporter;

/**
 * @Configuration
 * @bean
 * 表示给spring容器注入bean  他们两个一般一起使用
 * 他们和@component的区别是他们使用了动态代理cglib 所以每次调用都会返回同一个实例
 */
@Configuration
public class WebSocketConfig {

    /**
     * 给spring容器注入这个ServerEndpointExporter对象
     * 相当于xml：
     * <beans>
     *     <bean id="serverEndpointExporter" class="org.springframework.web.socket.server.standard.ServerEndpointExporter"/>
     * </beans>
     *
     * 检测所有带有@serverEndpoint注解的bean并注册他们。
     * @return
     */
    @Bean
        public  ServerEndpointExporter serverEndpointExporter(){
        System.out.println("我被注入了");
        return  new ServerEndpointExporter();
    }
}

```

#### 下面使我们的websocket类

下面一共写了五种方法

> 建立通信
>
> 关闭通信
>
> 接收消息
>
> 发送消息
>
> 异常

```java

package com.gzh;

import org.springframework.stereotype.Component;

import javax.websocket.*;
import javax.websocket.server.ServerEndpoint;
import java.io.IOException;
import java.util.concurrent.CopyOnWriteArraySet;

/**
 * @Component  将类注入到容器
 * @ServerEndpoint  前端通过这个url进行访问通信 建立连接
 */
@ServerEndpoint("/websocket")
@Component
public class MyWebSocket {

    //存放websocket 的线程安全的无序的集合
    private  static  CopyOnWriteArraySet<MyWebSocket> websocket = new CopyOnWriteArraySet<MyWebSocket>();

    private Session session;

    public static CopyOnWriteArraySet<MyWebSocket> getWebsocket() {
        return websocket;
    }

    public static void setWebsocket(CopyOnWriteArraySet<MyWebSocket> websocket) {
        MyWebSocket.websocket = websocket;
    }

    public Session getSession() {
        return session;
    }

    public void setSession(Session session) {
        this.session = session;
    }

    /**
     * 连接建立成功调用的方法
     * */
    @OnOpen
    public void onOpen(Session session) {
        this.session = session;
        websocket.add(this);     //加入set中
       // addOnlineCount();           //在线数加1
         System.out.println("进入onOpen方法");
        try {
            sendMessage("连接已建立成功.");
        } catch (Exception e) {
            System.out.println("IO异常");
        }
    }


    /**
     * 关闭通信连接
     * @param session
     */
    @OnClose
    public void onClose(Session session){
        //关闭连接后将此socket删除
        websocket.remove(this);
        System.out.println("进入onClose方法");
    }

    /**
     * 获取客户端发来的信息
     */
    @OnMessage
    public void onMessage(String message){
        System.out.println("进入onMessage方法; message = " + message);
    }

    /**
     * 给客户端推送信息
     */
    public void sendMessage(String message) throws IOException {
        System.out.println("进入sendMessage方法");
        this.session.getBasicRemote().sendText(message);
    }

    /**
     * 异常方法
     */
    @OnError
    public void onError(Session session, Throwable error){
        System.out.println("进入error方法");
        error.printStackTrace();
    }

}

```

这里呢我们稍微分析下这个方法里面的东西

- 注解@ServerEndpoint  

```

package javax.websocket.server;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import javax.websocket.Decoder;
import javax.websocket.Encoder;
import javax.websocket.server.ServerEndpointConfig.Configurator;

@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
public @interface ServerEndpoint {
	//注释类应映射到的URI或URI模板（必写的参数）。
    String value();
    
	//子协议
    String[] subprotocols() default {};

	//解码器	输入websocket消息 输出java对象
    Class<? extends Decoder>[] decoders() default {};

	//编码器  输入java对象 输出websocket
    Class<? extends Encoder>[] encoders() default {};

	//配置器
    Class<? extends Configurator> configurator() default Configurator.class;
}

```

- CopyOnWriteArraySet（看完就知道我们为什么要用它了）
  -  java.util.concurrent包下  俗称 JUC
  - 线程安全的无序的集合，可以将它理解成线程安全的HashSet
  - Set：不可重复，检索元素效率低下，删除和插入效率高，
  - List：可重复，和数组类似，List可以动态增长，查找元素效率高，插入删除元素效率低
- @onopen @onclose等注解 
  - 和前台websocket.onerror等方法相对应



#### 我们的主方法执行服务

这里我们每三秒启动个线程

```java
package com.gzh;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @SpringBootApplication   标注一个主程序，说明这是一个springboot应用
 */
@SpringBootApplication
public class SpringBootMainApplication {

    //编写主程序方法
    public static void main(String[] args) {
        SpringApplication.run(SpringBootMainApplication.class);

        for (int i = 0; i <= 10 ; i++){
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            MyThread myThread = new MyThread();
            Thread thread = new Thread(myThread);
            thread.start();
        }

    }
}

```

#### 线程类

在线程的run方法里让他去发送消息

```java
package com.gzh;

import java.io.IOException;
import java.util.concurrent.CopyOnWriteArraySet;

public class MyThread implements  Runnable{

    @Override
    public void run() {

        CopyOnWriteArraySet<MyWebSocket> websocket = MyWebSocket.getWebsocket();
        for (MyWebSocket myWebSocket : websocket) {
            try {
                myWebSocket.sendMessage("我要发消息了"+ Math.random());
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

```

#### html前端

这里我们只需要更改我们的地址路径就即可

```javascript
<!DOCTYPE HTML>
<html>
<head>
    <title>My WebSocket</title>
</head>

<body>
Welcome<br/>
<input id="text" type="text" /><button onclick="send()">Send</button>    <button onclick="closeWebSocket()">Close</button>
<div id="message">
</div>
</body>

<script type="text/javascript">
    var websocket = null;

    //判断当前浏览器是否支持WebSocket  ,主要此处要更换为自己的地址
    if('WebSocket' in window){
        websocket = new WebSocket("ws://localhost:8080/websocket");
    }
    else{
        alert('Not support websocket')
    }

    //连接发生错误的回调方法
    websocket.onerror = function(){
        setMessageInnerHTML("error");
    };

    //连接成功建立的回调方法
    websocket.onopen = function(event){
        setMessageInnerHTML("open");
    }

    //接收到消息的回调方法
    websocket.onmessage = function(event){
        setMessageInnerHTML(event.data);
    }

    //连接关闭的回调方法
    websocket.onclose = function(){
        setMessageInnerHTML("close");
    }

    //监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
    window.onbeforeunload = function(){
        websocket.close();
    }

    //将消息显示在网页上
    function setMessageInnerHTML(innerHTML){
        document.getElementById('message').innerHTML += innerHTML + '<br/>';
    }

    //关闭连接
    function closeWebSocket(){
        websocket.close();
    }

    //发送消息
    function send(){
        var message = document.getElementById('text').value;
        websocket.send(message);
    }
</script>
</html>
```

这里说一下我们的路径 ws  我们一般会看到wss 什么时候用哪个呢？

- 他们的使用规则主要是和浏览器的兼容有关
- [http/https与websocket的ws/wss的关系](https://blog.csdn.net/garrettzxd/article/details/81674251)

>1.Firefox环境下https不能使用ws连接
>
>2.chrome内核版本号低于50的浏览器是不允许https下使用ws链接
>
>3.Firefox环境下https下使用wss链接需要安装证书

所以我们可以对这部分浏览器做个处理

```java
let protocol = location.protocol === 'https' 
	? 'wss://localhost:8888' 
	: 'ws://localhost:8889';
new WebSocket(protocol);
```

#####以上就是springboot下实现简单的websocket的应用

<u></u>

