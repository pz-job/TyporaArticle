# @Component

不会被代理返回的都是不同的实例



# @Configuration

会被动态代理导致调用方法的话返回的是同一个实例

## @bean

一般和@Configuration 一起使用表示

### @autowired

注入bean 使用被@component 或者@bean所创建的对象