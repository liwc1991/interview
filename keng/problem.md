# Java异步调用不生效
ApplicationContext.publishEvent ——Java异步调用的一种方式
原因：里面的sql有报错


# 微众双边
1、maven菜单找不到  
解决办法：没有的话，还可以 在任意一个pom文件里，右击；找到，点击“Add as Maven Project”

2、配置tomcat  
解决：小微是用tomcat启动的

3、名称[mfsdata]未在此上下文中绑定。找不到[mfsdata]   
解决：在tomcat下的context.xml 需要配置

4、http 404 未找到


# 蚂蚁
1、数据导入：sqlload 正常速度应该为 1分钟导入100万数据，1小时导入6000万数据，1天导入14亿条数据。 且表不加索引的话，sqlload的速度会加快。

2、分区表查询慢：40分钟。  索引创建需要用global，从40分钟查询时间减少到1秒。

# 网银流水机器人
1、外部调用经常超时怎么弄

可以降低单次的查询量吗，我看接口也是用SQL从数据库查询然后汇总结果返回的，应该是一次查询600个用时太长，可以试下减少每次查询数量增加查询频率。 接口里没有设置最大时长，查询汇总完再返回的，要看数据库查询的速度，您可以试下降低到比如300，然后看下情况


# 现金流测算
1、本地连sit库地址的测算结果与服务器测算出来的结果不一致。参数用的同一套参数。
原因：资金端的包不一样。

2、启动报找不到bean的错
原因：bootstrap 没配置dev，配了prod生产的。

3、启动了但是感觉没有读取到正确的数据库
原因：bootstrap 虽然配置了dev，但是读取的nacos文件名不对

4、repo层能读到数据的新加的属性，但是通过vo转换属性值丢失。
原因：因为vo转换方法是用mapper注解编译出来的，需要clean，重新编译。

5、启动时  Could not find artifact xxx
5.1   问题原因
因为项目子包是依赖父包的快照文件，父包快照文件不存在时，maven就会先去找本地仓库是否存在该快照文件，如果本地仓库不存在的话，就会向远程仓库请求该父包快照文件，如果本地仓库和远程仓库都找不到就会报错。

5.2解决方法
（1）首先打开该项目的maven管理界面，找到该项目的根目录，打开项目的生命周期，点击clean清楚之前编译失败的target文件夹内容。然后再install将根目录的父包的快照文件安装到本地仓库中即可。
（2）install命令
（3）SNAPSHOT pom文件类型

6、idea debug模式下，在断点停止后，仍然往下执行
Preferences -> Build,Execution,Deployment -> Debugger 找到Java那一栏下边可以看到 Kill the debug process immediately 选项，勾选后点击确定就可以了。

或是直接在设置搜索框里输入 Kill the debug process immediately 后，就会出现这个复选框。

7、在多线程时使用@Autowired总是获取不到bean
原因是：new thread不在spring容器中，也就无法获得spring中的bean对象。
解决方法：增加一个BeanContext类，进行手动获取
this.testService= BeanContext.getApplicationContext().getBean(TestService.class);

8、在主线程里直接开线程，子线程的log获取不到主线程的@log4j 注解，导致日志无法打印

9、log输出时用占位符，用字符串拼接的方式速度会慢

10、本地启动时，用postman发请求报http 404 未找到
原因：一般是因为请求的地址没配置对


11、
org.springframework.jdbc.BadSqlGrammarException:
### Error updating database. Cause: java.sql.sQLSyntaxErrorException: ORA-01790: 表达式必须具有与对应表达式相同的数据类型

原因：批量插入时，某个字段为null的和不为null的不能放在同一个list里插入

12、Error updating database.  Cause: java.sql.SQLException: sql injection violation, syntax error: ERROR. token : FROM, pos : 499 :

原因：sql写错了，多字段或少字段

13、nullpointException ： 存量资金导入填了0，导致空指针


14、java.lang.ArithmeticException: / by zero  ： 生产环境报错，但测试环境没报错

测试环境配置的方案参数跟生产不一样。

15、fail to load application 远程主机强迫关闭了一个现有连接怎么解决

最终确认是数据库的问题。

16、java: Internal error in the mapping processor: java.lang.NullPointerException: Cannot invoke "java.net.URL.toExternalForm()" because "resource" is null

File | Settings | Build, Execution, Deployment | Compiler | user-local build process vm options

-Djps.track.ap.dependencies=false



# 生产事故
1、界面特别慢（影响1个小时）

原因：应用服务器连接数据库服务器特别慢，ping的速度超过70ms，但在测试环境<1ms。数据库做容灾备份时影响。

2、生产日志进try 但没有进catch

原因：有数据库表被锁了。后续经排查，数据库语句上的超时对dblink不生效。

3、切换账号，查询速度不一样。  切换账号，抽取ta集合分配数据抽不出来。一个select sql执行了5分钟左右。

原因：通过监控系统发现，上述问题都是同一个原因导致的，就是在某些时间点的数据库io负载太高。如晚上10点08分，早上10点左右。

4、查询干系人，速度慢

原因：sql里使用了wm_concat函数拼接了干系人，后来改为listagg(fxwt,',') within GROUP (order by fxwt) as fxwt 速度提升10几倍

5、omp直接挂了（影响1个小时）

原因：sql各种抛错

oracle的锁分两种：一种是oracle层面的锁，另一种是dblink那种锁。

此SQL从12月1日开始，一直暂用session得不到释放，产生了很多游标锁，后续通过kill掉 1441这个session，之前的dblink锁，草稿表的锁均得到释放。收据管理下-查询信保基金dblink也可以正常返回。

改进方式：1.不用dblink

2.dblink是事务操作，建议在mapper.xml中涉及dblink的操作，用begin commit end的方式来写。（这点大家可以试试，在什么情况下dblink select是事务操作。）

6、发起付款单时，明细丢失了

原因：未知

7、费用交易明细表锁表

原因：未知  bug 12368

8、度小满授信接口，小微收到报文响应超过5秒，被机构拉入黑名单

原因：1、可能是没有配置数据库连接池druid的原因。参考omp的配置。

9、sql硬解析过多，导致内存耗尽，数据库重启。  排查问题，数据库问题导出awr报告进行分析。

原因：（1）硬解析过多
小微系统早期核算代码的问题，SQL没有通过preparestatment组装预编译，而是过字符串拼接的，拼接的SQL被进行硬解析，当业务量大的时候，内存占用速度超过释放速度，导致内存占满，数据库按自身机制自动重启，重启后又未配置自动开连接，进而导致业务系统不可用。

解决方案：
（1）长期方案-从根本解决：量很大，从数据库后台查询问题SQL，每周修改上线，持续更新。
（2）短期方案-通过数据库自动重启释放内存，配置自动开连接，不会影响实时业务，但需7*24运维通知项目组（聂道宏），手工启动定时任务。

（2）重启数据库 有个参数配置的有问题

10、前置跟小微中间的限流问题

现象：流量忽高忽低

解决方案：重新写了个限流器，注意限流器的配置

11、数据库缓存配置的太少导致批量慢

现象：批量跑的时间从每天18点19点 提前到  14点完成

原因：数据库缓存本来只配置了500M，现在扩到几个G，处理速度提升。


# 代码优化
1、循环单笔插入改为批量插入
2、循环查询改为一次性查询放入内存中，注意可以先估算对象的大小字节数，来看占用多少内存。



# 项目管理流程查看报错
解决方法：1、用的数据治理员账户  2、服务器时钟问题 3、根据项目id或产品id查不到，因为产品未成立导致的


# 批量合并发起付款单报错
解决方法：1、限制一次发起一个产品

2、分批10条发起付款单 

3、加入线程机制

4、加入进度条机制，这样可以防重复点击，也让界面更友好
```
前端：JoyinProgress.showInDialog('uploadFileProgressBar')
后端：progressBarService.updatePercent(pgeid,,uploadPercent,false)
```


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

2、ResponseResult{retcode='500', msg='流程发起失败:exepct '[', but {, pos 1, json : {"data":"NULL","uuId":"NULL"}', responseBody={"data":"NULL","uuId":"NULL"}}
2.1 参数漏传 uid ,产品/项目ID。  
2.2后来发现跟用户有关系，用tanl发起就报错，用liwch发期间就不报。

# aws流程回调报错
1、日志里显示：接口服务推送其他系统异常，fail to connect 10.100.6.195:8180
2、原因：接口服务所在的30.6网络跟本地环境10.100.6.189不通，找荣幸打通了后就行了
3、aws那边点报错，但是把请求拷出来后用postman发送却返回200错误
3.1解决方法：打断点调试
3.2 原因：有sql报错


# 权限问题
一个用户进系统后什么菜单都看不见
1、这个用户被分配了一个权限为空的角色，
1.1 解决：给角色分配一个菜单的权限

2、sql查询如果没有produuid时，sql语句不会过滤权限，这个时候需要在sql里自己加上权限控制
```
<if test="userId!=null and userId!=''">
 and (PROJ_UUID IN
 (SELECT OBJECT_ID
 FROM PUB_USER_PERMISSION
 WHERE USER_ID =#{userId,jdbcType=VARCHAR}) OR EXISTS
 (SELECT 1
 FROM PUB_USER_PERMISSION
 WHERE USER_ID = #{userId,jdbcType=VARCHAR}
 AND ALL_PERM = 'Y' AND OBJECT_TYPE='PROJ'))
 </if>
```


# 首页增加oa待办统计方法
1、main.html里加一段html
2、pub_boardconfig添加一条记录。
3、sys_page_element添加一条记录


# 继承abstract类时报错
1、明明写对了方法名和参数，但还是报错
2、解决方案：把方法体拷贝出来，然后删除掉方法，重新写，把方法体再拷贝进去。
3、继承抽象方法时，入参不能有泛型，否则会报错。比如
```
父类：
public abstract void setPamtDtl（BokRecvpamtVo pamt, Map<String,Object> params）

@Override
public void setPamtDtl(BokRecvpamtVo pamt, Map param)  对的

@Override
public void setPamtDtl（BokRecvpamtVo pamt, Map<String,Object> params） 报错

```


# oracle数据库插入时间问题（小公式显示顺序）
1、如果是在for循环里直接对updateTime设为new Date，则更新时间最后都会变成一样，分辨不出先后顺序
解决方法：
for的时候对每个时间+1毫秒

# sql报错、insertBatch问题
## 问题现象
问题1：ORA-00903: 表名无效
问题2：parameters'' not found.availabe parameters are [collection,list]
## 问题解决
1、insertBatch前需要判断列表是否为空，如果插空列表会报表名无效的错误。
2、sql语句的查询的入参需要加个 @Param("recvpamtUuids") 注解，否则xml里无法识别

# mybatis和事务的相关问题
1、如果if语句是放在set里面的话，会自动把最后一个语句的逗号过滤
2、如果同一个事务里有两个sql操作，第一条插入，第二条查询这条插入的数据，是可以查到的
3、如果是不同事务的两个sql操作，第二条sql是查不到第一条的数据的。
4、同一个sql传入的参数一样，但结果不一样
5、查询语句是不允许重名的，即使传入的参数不一样
6、mapper的写法  <if test="jhBnftExpportTMark!=null and jhBnftExpportTMark=='Y'.toString() ">。
 jhBnftExpportTMark传的Y，但是没有进if。
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


