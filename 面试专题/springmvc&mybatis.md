#### springmvc的优点

- 容易扩展
- 支持多种资源的映射
- 任意使用视图技术
- 单例的



#### springmvc的运行流程

- 我们关注点有三个
  - 前端控制器DispatcherServlet，
  - 映射器HandlerMapping：保存请求地址和处理器之间的映射信息
  - 适配器HandlerAdapter：使用他去执行目标方法

- 首先在服务启动的时候，会对组件进行初始化，其中包括HandlerMapping和HandlerAdapter
- HandlerMapping初始化将我们所标记的@requestmapping的路径注册在map中
- 当request请求过来的时候，我们的前端控制器会去根据路径去HandlerMapping查找相对应的handler
- HandlerMapping返回我们所需要的handler和他的执行链给前端控制器
- 前端控制器通过得到的handler去获得相对应的适配器，
- 适配器去帮我们执行目标方法
- 执行完成会返回一个ModelAndView对象
- 前端控制器请求视图解析器去解析真正的视图view
- 前端控制器根据view进行视图渲染，将数据保存在request域中，并转发到页面当中
- 向用户响应结果





#### mybatis是什么

- 他是一个半自动ORM（对象关系映射，需要编写sql）框架，内部封装了jdbc，开发时候只需要关乎sql本身，无需处理加载驱动，创建连接等过程
- 可以和spring兼容
- sql写在xml文件便于统一管理



#### ${}和#{}的区别

${}是字符串拼接 #{}是占位符，先将sql的#{}替换成？，然后调用PreparedStatement 的set来进行复制，防止了sql注入，提高安全性。



#### 一对一查询映射，一对多查询映射

- association
- collection



#### 一级缓存和二级缓存

- 一级缓存默认打开，储存作用域是sesssion
- 二级缓存默认不开启，开启的话需要实现序列化接口，可自定义缓存储存源，
- 当缓存作用域进行创建，更新，删除之后，默认作用域将删除所有的select缓存	



#### 接口绑定

- 注解绑定，在接口方法上添加@update，@select等注解
- xml绑定，通过namespace来进行接口全类名绑定



####myBatis 里面的动态Sql是怎么设定的?用什么语法?、

利用标签，根据表达式的值来完成sql的逻辑并动态拼接sql

trim | where | set | foreach | if | choose| when | otherwise | bind 。