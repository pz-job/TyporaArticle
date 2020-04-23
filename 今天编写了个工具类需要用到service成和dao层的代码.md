今天编写了个工具类需要用到service成和dao层的代码

如下：

```java
//第一步：需要将工具类注入到容器中
@Component
public class RuleUtils {

   //第二部注入
    @Autowired
    private  BsVehicleInfoServiceImpl bsVehicleInfoImpl;
    @Autowired
    private  BsAlarmInfoMapper alarmInfoMapper;
  
    //第三步 建一个静态的本类 
    private static  RuleUtils ruleUtils;

    //第四步 初始化
    @PostConstruct
    public void init() {
        ruleUtils = this;
    }

    
    public static BsAlarmInfo isInandoutRule(String[] ms){
      //第五步  这里用类的属性去进行调用
      ruleUtils.bsVehicleInfoImpl.add(ms);
    }
```

讲解一下代码编写的思路：

1.容器去管理我们的组件

2.组件中我们先执行的是静态方法，因为我们的方法是静态方法所以会先执行，但是这个时候我们@autowired注入的组件还没有被注入进来，所以此时会是null

3.静态方法执行完之后是执行我们的构造器

4.构造器之后是我们@autowired的组件被注入进来

5.然后执行@postconstruct标注的方法（PostConstruct在构造函数之后执行，只会执行一次）方法中我们对组件进行初始化，因为这时候service/dao已经被注入进来，所以我们将注入好的类赋值一下

6.最后我们用类的属性去调用方法就可以了。

7.Constructor >> @Autowired >> @PostConstruct