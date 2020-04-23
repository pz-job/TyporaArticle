  今天使用存储过程和定时器实现了一个定时每月删除上一月之前的所有数据

#### 一、编写删除上个月之前所有数据的sql

~~~sql
delete from bs_gis_position where data_time <to_char(add_months(trunc(sysdate),-1),'yyyy-mm-dd hh24:mi:ss');
~~~



#### 二、创建存储过程

这里我使用的工具是plsqldev

首先我们去新建一个测试窗口打开是下面这个样子的



![1567069297697](C:/Users/16781/AppData/Roaming/Typora/typora-user-images/1567069297697.png)



正式创建存储过程是![1567069778003](C:/Users/16781/AppData/Roaming/Typora/typora-user-images/1567069778003.png)

其中--local variables here 处填写的是一些自定义的变量

变量的写法 为 【 i integer := 15；】 使用 **:=**进行复制

begin之后写的就是所要执行的sql语句了

这里就将我们第一步准备好的sql放入

####三、创建定时任务

之后我们去新建一个job定时任务 （在jobs文件夹处右键）

![1567079502083](C:/Users/16781/AppData/Roaming/Typora/typora-user-images/1567079502083.png)

在what值中填写我们刚才创建的procedures存储过程的名称 以；号结尾

间隔中我们根据下列找寻我们需要的时间间隔填写即可。

- 每分钟执行
  TRUNC(sysdate,'mi') + 1/ (24*60)

- 每小时执行

  TRUNC(sysdate,'hh') + 1/ (24)

- 每天的凌晨1点执行
  TRUNC(sysdate+ 1)  +1/ (24)

- 每周一凌晨1点执行
   TRUNC(next_day(sysdate,'星期一'))+1/24

- 每月1日凌晨1点执行
  TRUNC(LAST_DAY(SYSDATE))+1+1/24

- 每季度的第一天凌晨1点执行
  TRUNC(ADD_MONTHS(SYSDATE,3),'Q') + 1/24

- 每年7月1日和1月1日凌晨1点
  ADD_MONTHS(trunc(sysdate,'yyyy'),6)+1/24

- 每年1月1日凌晨1点执行
  ADD_MONTHS(trunc(sysdate,'yyyy'),12)+1/24

#### 四、查看定时任务信息

~~~sql
select * from user_jobs;
~~~





