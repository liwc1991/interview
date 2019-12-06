# ajax请求的坑
ajax不是同步的请求，想用同步用ajaxSync。

# 校验的自定义校验和分组校验不能同时生效
@valid 和 @validated 的注解好像不能同时生效，需看源码。

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
1、删掉maven库下的joyin文件夹 

# list校验问题
## 问题现象
@Valid @NotEmpty () 的校验不生效了
## 问题解决



# insertBatch问题
## 问题现象
ORA-00903: 表名无效
## 问题解决


# pom.xml第一行报错
## 问题现象

## 问题解决
右击项目名称 -> maven -> update project -> 选中force update of snapshots/release
等待项目更新完成之后，错误消失。
错误的原因：pom.xml在中途增加了一些包，而run as 无法将这些包导入到maven dependency中。通过如上操作强制更新项目来解决问题


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


