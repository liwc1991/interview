# 校验的相关问题
1、自定义校验和分组校验不能同时生效
@valid 和 @validated 的注解好像不能同时生效，需看源码。
2、dto校验和 自定义校验会同时进，所以需要注意自定义校验里加判空判断
3、@Valid @NotEmpty () 的校验不生效了。 这是因为需要在list<A>里的A类上定义一个自定义校验类valid logic，框架才会帮你校验。
4、校验时如果要显示表格名称，要在html里对table加上name和title属性。

# 请求，controller接收不到参数
1、ajax请求。后台发送的是ajaxJson请求，后台需要加get参数才能接收到。或者用requestbody dto方式接收。
2、微服务请求。后台参数需要加requestbody才能接收到。


#  数据字典增加码值怎么配置？
1、sys_page_dictlist 里加配置
2、ompdictcodelist里加配置


#  新加页面需要在哪配置？当前页面在数据库未配置
1、sys_page里配置页面
2、controller上加pageid注解

#  下拉框，访问的地址不存在
maven update project重新扫描，但不知道为啥要这么做


#  出现诡异的现象，程序本地启动报错，数据库数据与页面对不上
出现问题是因为后台多启动了一个8180端口的程序，运行的是后台的程序。
```
step1: 根据端口找PID

netstat -ano | findstr 8180 //包含8180的所有都会列出来
step2: 根据PID找应用

tasklist | findstr 3268
step3: 找到应用后在任务管理器中关闭进程即可，或通过以下命令

taskkill -PID <PID> -F //强制关闭某个进程
```


# 数据查询希望不存在也能查出结果
比如明细合并发起时，信托专户不存在，那么需要不找出信托专户，然后生成收款单。 写查询语句的筛选条件时要在left join后面加筛选条件，而不要在查出来后再加查询条件。


# 数据查询少数据的问题
数据查询加了分页，所以查出来的数据少了


# omp 权限过滤
omp在涉及到产品的查询中都加上了产品的uuid进行过滤
如果表里确实不存在产品uuid这个字段，需要在OmpDataAuthorityServiceImpl里加过滤。


# ajax请求的坑
ajax不是同步的请求，想用同步用ajaxSync。



# BigDecimal的加法 和 除法
如果这么写：a.add(b); 是不对的；
如果这么写：a=a.add(b);是对的；

当divide除不尽时BigDecimal num1 = new BigDecimal("10");
要写成 BigDecimal.divide(BigDecimal divisor, int scale, RoundingMode roundingMode) ;



# 报错在开发环境没有，在测试环境有
## 问题现象

## 问题解决
1、开发环境的tomcat用的内置，测试环境的tomcat是自己部署的 
2、数据问题，开发环境的sql写错了 



# 找不到或无法加载主类的问题
## 问题现象

## 问题解决
一般是maven相关的问题
1、一种情况：删掉maven库下的joyin文件夹 
2、第二种情况：删掉了一部分interface包，但没有删干净。建议全删。全部删光了就好了





# insertBatch问题
## 问题现象
ORA-00903: 表名无效
## 问题解决
insertBatch前需要判断列表是否为空，如果插空列表会报表名无效的错误。

# pom.xml第一行报错
## 问题现象

## 问题解决
右击项目名称 -> maven -> update project -> 选中force update of snapshots/release
等待项目更新完成之后，错误消失。
错误的原因：pom.xml在中途增加了一些包，而run as 无法将这些包导入到maven dependency中。通过如上操作强制更新项目来解决问题

# pom.xml文件报错
## 问题现象
用的还是旧的maven地址下载包
## 问题解决
更改maven的地址
错误的原因：maven地址更新了，但xml文件没改。


# mapper.xml出现莫名其妙的错误
## 问题现象

## 问题解决
在pl-sql里用中文逗号不会报错，但是在xml文件会有错误。


# 页面上取码值在数据库字典里的设置
## 问题现象

## 问题解决
在sys_page_dictlist里进行配置 
还要加上@SubPageId 注解



# java变量生命周期的问题
## 问题现象
引用传递时在函数内已经设置好map的值，但是传出来时map的值没有被更改。
## 问题定位
java引用传递，变量的生命周期好像会被清掉。。。
并不是上述的原因，而是说 在函数里对map进行了清空。


# java的putAll的使用注意事项
## 问题现象
putAll方法抛NullPointerException，查看源码得知，这竟是putAll方法的唯一一个异常。
## 问题定位
public void putAll(Map<? extends K,? extends V> m)
调用此方法将获得参数m中的所有映射，如果存在key相同的，则被m中的value覆盖。
此方法实现了Map<K,V>的同名方法，重写了AbstractMap<K,V>中的同名方法。
当m为空时，该方法就会抛出NullPointerException。
1、putAll要判断插入的集合是否为空
2、for(a:list) 要判断list是否为null



# mapper.xml的使用注意事项
## 问题现象
编译报错：元素内容必须由格式正确的字符数据或标记组成。。
## 问题定位
Mapper.xml 文件里  < (小于)号 ,   >(大于)号 ,会被认为是括号,需要额外注意,
解决办法 : 将 < 号换成  &lt;     > 号 换成&gt;  或者用CDATA括起来


# 集合的keySet()的使用注意事项
## 问题现象
keySet清空后，会影响到原来的集合。
## 问题定位

## 问题解决
。


# java.util.List接口的方法subList()的使用注意事项
## 问题现象
用户注册接口注册时，传入10000个用户，只注册了一半的用户，从0~1000,2000~3000...。
## 问题定位

## 问题解决
经查看subList方法，对subList截取的列表进行修改的时候，会影响原来list的结构。


# 注册程序不跑的原因
## 问题现象
注冊程序跑一段时间后，不再注册新用户，程序也没停。

## 问题定位
其实应该用jstack查看线程状态。
最后用肉眼看代码，发现是锁的问题。


## 问题解决
加释放锁的逻辑代码。


# 程序不跑的原因
## 问题现象
webmanager运行了一段时间后，不产生日志。

## 问题定位
jstack查看线程状态。发现是数据库连接泄露问题。查看进程的内存使用量，也属于偏高的状态。


## 问题解决
将关闭数据库连接的代码写在finally的模块里。


# 数据库数据修改
## 问题现象
在pl/sql oracle的command window里进行insert，显示insert成功，且select查询有返回结果。但是直接对表进行query data，没有显示新插入数据。
## 问题定位
没有commit
## 问题解决
command window没有进行commit操作，得点击或输入commit，才能提交成功。



# 压测问题
## 问题现象
cmp压测的连接数上不去，cpu、内存都没到占用满状态。
用户收一段时间的消息后被踢下线。
推送1500万用户只有200w用户收到消息。

## 问题定位
。


## 问题解决
。



# bean同名问题
## 问题现象
不同工程定义同名dto时，编译时报错！

## 问题定位
。


## 问题解决
。


