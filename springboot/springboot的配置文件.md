





今天给大家讲述一下配置文件

配置文件主要分为properties ，xml 和yml

其中properties只是适合简单的赋值操作

在我们项目的全局配置文件中，配置文件名是固定（application.properties）

#### yml（YAML）

先了解一下yml的语法

这里我们定义一下User类

```java

package com.guomie.springboot;


import com.guomie.springboot.Dog;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;

import java.util.ArrayList;
import java.util.HashMap;
@Component
@ConfigurationProperties(prefix = "user")//和我们的配置文件中的属性进行对应
public class User {
        private String lastName;
        private int age;
        private HashMap<String, String> map;
        private ArrayList<String> list;
        private Dog dog;
  //这里我把tostring和get/set方法省略了
}
@Component
public class Dog {
    private  String name;
    private  int age;
}
```

这里标注了一些写法包括基本对象，list，map

```java
user:
    lastName: '张三'
    age: 12
    dog:
        name: 123
    list:
        - 111
        - 1111
    map: {"k1": "1","k2": "2"}
```

我们要使用yml给组件注入值的话我们需要添加**@ConfigurationProperties**注解并且注解属性**prefix**指定配置文件中的属性

### @value和@ConfigurationProperties的区别

提到了给组件属性注值，我们就比较一下**@value**这个注解和**@ConfigurationProperties**的区别、

- @value
  - .#{}可以用SpEL表达式
  - ${} 从配置文件里获取到值
  - 也可以直接赋值
- @ConfigurationProperties
  - 可以使用数据验证（JSR303）
  - 批量注入属性

我们除了使用默认的配置文件还可以指定配置文件，我们用**@PropertySource**注解来指定(**只能用于properties文件)**

```java
@PropertySource(values = {"classpath:application.properties"})//加载指定的配置文件,可以加载多个
@Component
@ConfigurationProperties(prefix = "user")
public class User {
```

我们以前来编写spring的配置文件，但是springboot不能识别怎么办；这时候我们用到**@ImportResource**注解
数组类型，可以加载多个配置文件

```java
@ImportResource(locations:{"classpath:beans.xml"})
```

但是springboot推荐我们使用全注解的方式

@Configuration == spring的配置文件

@bean == 往配置文件中注入bean

注解的写法

```java
@Configuration
public class HelloConfiguration {
    @Bean
    public User getHello(){
        User user = new User();
        System.out.println("注入成功"+ user );
        return user;
    }
}
```

xml配置文件写法（springboot使用配置文件需要**@ImportResource**注解）

```xml
<bean name="getHello" class="com.guomie.springboot.User">
        <property name="lastName" value="pz"></property>
</bean>
```

### 配置文件的执行顺序

### 配置文件如何快速切换配置信息

在项目开发中我们经常会切换环境，一般都会有个测试环境，一个线上环境，每次我们更换ip，数据源或者是一些其他参数很麻烦需要一个一个切换，这时候我们可以yml的多文档块模式

```yml

spring:
    profiles:
        active: dev

---
spring:
    profiles:  dev
server:
    port: 8089

---
spring:
       profiles: www
server:
    port: 8088
```

我们每个文档块之间用 --- 分割 我们只需要active不同的profiles就可以切换配置信息

