---
t
ypora-root-url: D:\DevelopTools\Typora\md\pic
---

## 写一个SpringBoot的HelloWord

### springboot是什么

> Spring Boot可以轻松创建可以运行的独立的，生产级的基于Spring的应用程序。 
>
> 大多数Spring Boot应用程序只需要很少的Spring配置。
>
> 提供了一个运行“spring脚本”的命令行工具

##### 优点

> 更快且更广泛的入门体验
>
> 开箱即用
>
> 提供大型项目通用的一系列非功能型功能

### springboot能干什么

### springboot怎么玩

1. 我们先去创建一个maven项目 这里我用的是idea
   - 新建一个maven项目 需要注意我们的我们的sdk是不是自己使用的
   - 然后我们对自己的maven进行一下配置，在我们的settings下找到maven，在Maven home directory中不要使用bundled maven 合适idea自己捆绑的maven，我们将路径设置为自己的maven路径，然后将local repository 设置为自己的maven库
2. 编写我们的pom.xml文件引入依赖

```java
<!-- Inherit defaults from Spring Boot -->
    <!--springboot 依赖-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
    </parent>

    <!-- Add typical dependencies for a web application -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <!-- Package as an executable jar -->
    <!--打包可执行jar的插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```



3. 然后编写我们的主程序

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
    }
}

```

4. 接下来我们就可以编写我们的controller service dao 三层架构了

```java
package com.gzh;

import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@EnableAutoConfiguration
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello() {
        return  "Hello Word";
    }

}

```

5. 我们直接在地址栏输入我们的路径就可以访问到(这里我们端口后面不用跟羡慕名称直接跟路径就可以)

```java
http://localhost:8080/hello
```

