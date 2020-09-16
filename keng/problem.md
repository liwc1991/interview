# 项目管理流程查看报错
解决方法：1、用的数据治理员账户  2、服务器时钟问题 3、


# 批量合并发起付款单报错
解决方法：1、限制一次发起一个产品  2、分批10条发起付款单 3、加入线程机制


# oa接口报超时
1、收付款单：回调时因为对于每条明细，切面打印日志，导致处理超时，100条处理了1分半钟。解决方法：把切面日志去掉。


# 费用公式开始日跟公式列表开始日不相同
原理：对于集合信托，在受益权成立时，系统会插一条成立日、初始规模到实收信托表里。 但是对于数据迁移环境，第一条的成立日和初始规模是由数据迁移组插入的，导致，计划列表的开始日和公式的开始日不一样。


# 程序报错，日志里没有异常
问题：1、只能跟到有null的行，但是不显示具体行数。
解决：因为try catch时没有打印日志信息，在外层的catch里应该把异常栈打出来，log.error(ExceptionUtil.getStackTrace(e))

# aws流程发起报错
1、报参数为null
1.1 解决方法：调aws的时候有的参数为空。比如流程定义id没传，为空

# aws流程回调报错
1、日志里显示：接口服务推送其他系统异常，fail to connect 10.100.6.195:8180
2、原因：接口服务所在的30.6网络跟本地环境10.100.6.189不通，找荣幸打通了后就行了
3、aws那边点报错，但是把请求拷出来后用postman发送却返回200错误
3.1解决方法：打断点调试
3.2 原因：有sql报错


# 一个用户进系统后什么菜单都看不见
1、这个用户被分配了一个权限为空的角色，
1.1 解决：给角色分配一个菜单的权限


# 首页增加oa待办统计方法
1、main.html里加一段html
2、pub_boardconfig添加一条记录。
3、sys_page_element添加一条记录


# sql报错
1、parameters'' not found.availabe parameters are [collection,list]
1.1 解决：查询的入参需要加个 @Param("recvpamtUuids") 注解，否则xml里无法识别
2、




# 继承abstract类时报错
1、明明写对了方法名和参数，但还是报错
2、解决方案：把方法体拷贝出来，然后删除掉方法，重新写，把方法体再拷贝进去。
3、继承非抽象方法时，入参不能有泛型，否则会报错。比如functionname（Map<String> params）



# oracle数据库插入时间问题
1、如果是在for循环里直接对updateTime设为new Date，则更新时间最后都会变成一样，分辨不出先后顺序
解决方法：
for的时候对每个时间+1毫秒

# mybatis和事务的相关问题
1、如果if语句是放在set里面的话，会自动把最后一个语句的逗号过滤
2、如果同一个事务里有两个sql操作，第一条插入，第二条查询这条插入的数据，是可以查到的
3、如果是不同事务的两个sql操作，第二条sql是查不到第一条的数据的。
4、同一个sql传入的参数一样，但结果不一样
5、查询语句是不允许重名的，即使传入的参数不一样
```
问题现象：getformulabyid（），第一次传入查出来的结果是两条，第二次查出来的结果是一条，不知道怎么回事
解决：  https://blog.csdn.net/wzy999157/article/details/88056933
问题描述

产生该问题的业务是,   在一个事务的循环中重复执行一条查询,  返回结果类型为list

第一次查询的结果为空list, 对该list添加一条记录

第二次查询的结果也理应为空list, 但查询得到的返回值却是上次循环中对list进行手动添加后的结果
 

产生原因
mybatis的一级缓存，mybatis会对查询的sql进行缓存， 查询在同一事务的前提下，mybatis对同一个session多次查询相同sql的情况, 会直接找上次查询的缓存, 不进行数据库查询

第一次查询的结果list被人为操作改变后, 第二次查询时, mybatis在缓存中查找, 返回了第一次查询的对象引用, 此时第一次查询的结果已被修改, 所以导致上述问题的出现

 

解决方案
1. 对可能产生缓存的查询结果进行修改时, 不直接对其操作, 而是将结果值复制到中间变量上(注意! 复制值, 而不是引用), 对中间变量进行修改操作,  则不会产生上述问题


2. xml文件 的<select>标签添加 flushCache="true", 禁用此查询的一级缓存

MyBatis的缓存机制
1、一级缓存(SqlSession级别) ——
含义：在操作数据库时需要创建SqlSession对象，在对象中有一个HashMap用来存储缓存数据，并且不同的SqlSession之间的缓存数据区域是不会互相影响的。
作用域：一级缓存的作用域是SqlSession范围，就是当同一个SqlSession中执行两次相同的sql查询语句时，第一次回去数据库中查询数据并写到缓存中，第二次在查询的时候，不会再去数据库中去查询，而是直接在缓存中读取数据。在使用时需要注意：当SqlSession执行DML操作（insert、update、delete）时，MyBatis会清空SqlSession中的一级缓存，这样做的目的是保证缓存中的数据永远是最新的数据，防止出现脏读数据。

2.二级缓存(mapper级别) ——
含义：使用二级缓存时，多个SqlSession共享一个Mapper的sql语句去操作数据库，得到的数据，同样用HashMap来存储缓存数据，相比较于一级缓存，二级缓存的范围更大，多个SqlSession共享二级缓存，二级缓存时跨SqlSession的。
作用域：二级缓存的作用域是mapper的同一个namespace，当不同的SqlSession执行相同的namespace下的sql语句，并向sql语句中传递的参数也相同时，第一次回去数据库中查询数据并写到缓存中，第二次在查询的时候，不会再去数据库中去查询，而是直接在缓存中读取数据。
备注：java的namespace可以理解为java的在同一级别的不同的类加载器,即classloader。java的namespace就是每个类
```



# 从缓存中获取信息的方法
```
SysDataUtil.getDeptName
```


# 各种注解的作用
```
@DictFormat : html里下拉框显示能显示中文名，而不是码值
```


# java程序跟问题跟丢了
1、卡在一个sql上，sql也不报错，是因为传入的param参数名对不上。  prdproddealdtlmapper.deleterepeat

# 花半天排查的问题
1、事务操作 dto的planList没转换出来
解决方法： 小dto里面不再塞planlist，只在最外面的dto塞planlist。
2、不同公式的交易对手反显时有问题。
资金用途下拉查交易对手的ajax请求去掉。
3、做反显时，要先清除掉原来的数据。不然会有联动的问题。





# 调整明细的相关问题
1、不加ignore就可以比较list。
2、展示名称时，在changerecord里写死。（如公式名称、收息定义名称）
3、比较list时，要加getprimarykey才能知道list哪个和哪个比。
4、复核时只查当次的调整明细:operationuuid要加上。
5、复核时多余的字段怎么样才能不比较？cnctTypeName这种怎么不比较:在dto里加cnctTypeName属性。
6、

# 单例的相关问题
1、计算工具类。用的时候每次都要new。不用autowired的原因是，如果是单例，里面的成员变量的值不会变，多个公式可能会混用。
2、


# 校验的相关问题
1、自定义校验和分组校验不能同时生效
@valid 和 @validated 的注解好像不能同时生效，需看源码。
2、dto校验和 自定义校验会同时进，所以需要注意自定义校验里加判空判断
3、@Valid @NotEmpty () 的校验不生效了。 这是因为需要在list<A>里的A类上定义一个自定义校验类valid logic，框架才会帮你校验。
4、校验时如果要显示表格名称，要在html里对table加上name和title属性。

# 请求，controller接收不到参数
1、ajax请求。后台发送的是ajaxJson请求，后台需要加get参数才能接收到。或者用requestbody dto方式接收。
2、微服务请求。后台参数需要加requestbody才能接收到。（微服务必须加requestbody）


#  数据字典增加码值怎么配置？
1、sys_page_dictlist 里加配置
2、ompdictcodelist里加配置
3、各个页面配置相同的dictcode但不同的码值在sys_page_dictlist里面配置。
4、选完一个码值带出不同的码值，在sys_datadict_list_link里配置。


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
1.omp在涉及到产品的查询中都加上了产品的uuid进行过滤
如果表里确实不存在产品uuid这个字段，需要在OmpDataAuthorityServiceImpl里加过滤。
2.如果查询语句不查prod_uuid的话，则不会做数据权限过滤。


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


