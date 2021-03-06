# PHP面试
---
## php基础知识
### php的整型溢出问题是怎么样的？
php的整型数的字长与平台有关， 对于32位的操作系统，最多的整型数是二十多亿， 其实就是2的31次方，最小为-2的31次方， php不支持无符号的整数。 如果一个数超出了integer范围， 将会自动解释为float类型，如果执行的运算结果超出了integer范围，也会返回float。

### 如何理解OOP？
OOP， 即面向对象编程， 包括三个方面，继承性、封装性、多态性， 其中最根本的东西就是抽象。    
1、继承性，即扩展性，通过子类对已经存在的父类进行功能扩展。    
2、封装性， 要求外部不能随意存储对象的内部数据， 即对该类中的具体实现做封装， 用户不必知道哪部的具体实现， 只需知道它是干什么，如何用就好了。    
3、多态性， 就是类的抽象和接口， 同一个类能够处理多种类型对象的能力。 我们在现实中， 看到的任何东西都可以看作为一个对象， 然后通过进一步的抽象为类， 类又可演变为很多类似的对象。  

### 对于设计模式和MVC的理解
Model-View-Controller, 模型，视图，控制器， 一想到MVC就会想到JAVA， 因为Java是一个完全面向对象的语言， Mvc最早出现在smalltalk中，其核心就是要将视图和数据模型分离， 这样不同的程序就可以有不同的展示。     
1. 模型， 即程序员写的功能、算法和数据模型， 也就是我们说的系统业务逻辑层。   
2. 视图， 即前端， 图形界面。 展示给用户看的。
3. 控制器， 主要负责对请求处理、转发和加载视图。   
设计模式， 说白了就是代码的设计经验的总结和归类， 设计模式最早应用于建筑行业， 编程的设计模式按照最早的Gof所述， 包括23种设计模式， 主要用于面向对象的程序编程。  遵循以下几个设计原则：    
开闭原则、 单一职责原则、 里氏替换原则、依赖注入、 接口分离、 迪米特原则、 优先使用组合而不是继承等等。 包括创建型模式、 结构性模式、 行为模式三类。


### HTTP协议1.0及1.1的区别， 并简单说下HTTP协议
Http， 超文本传输协议。 它定义了浏览器和服务器的通信规则。 Http协议是基于Tcp／IP的TCP协议上， 现在万维网使用的HTTP1.1版本， 其特点包括，C／S模式， 请求简单（GET／POST／HEAD）， 灵活（可以传输任何类型的数据Html，XML， Json， 自定义等）， 无连接（每次连接只处理一个请求， 从发出请求到收到200状态为止断开连接）， 无状态。 对Http协议的考察主要包括， 响应消息格式、请求消息格式、状态码等知识点。 一般一个Http协议包括： 起始行（start line）、首部（header）、主体(body)三个部分。      
状态码：         
<pre>
1xx-消息   2xx-成功   3xx-重定向  4xx-请求错误  5xx-服务器错误
常见状态码  
200 OK。请求已成功，请求所希望的响应头或数据体将随此响应返回。
300 Multiple Choices。请求资源有多个选择，如请求文本有法语版、英语版等等。
301 Moved Permanently。表示请求的网页已永久转移到新位置
302 Found。表示页面在做临时跳转时返回的状态。
304 Not Modified。客户的缓存资源是最新的， 要客户端使用缓存。
400 Bad Request。错误的请求。
403 Forbidden。请求被服务器拒绝了。
404 Not Found。表示服务器找不到请求的网页，服务器上不存在的网页经常会返回的HTTP状态码。
500 Internal Server Error。
503 Service Unavailable。服务器目前无法为请求提供服务，但过一段时间就可以恢复服务。
504 Gateway Timeout。与状态吗408类似， 但是响应来自网关或代理，此网关或代理在等待另一台服务器的响应时出现了超时。
505 HTTP Version not supported。 HTTP协议版本不支持。
</pre>  

### 简单说下Apache中rewrite机制和php框架中url路由机制
* URL重写， 其实就是把用户通过浏览器请求的URL， 到了后台， apache根据预先配置的重写规则将该请求指向真正的资源路径， 说白了就是打马虎眼， 把真正的路径改头换面后给用户访问， 可问题是这样做有啥用处呢？
  1. SEO方面。 将动态的URL静态化， 以满足搜索引擎的胃口。
  2. 访问控制。 比如，通过重写规则后，客户端不能轻易判断后台程序类型
  3. URL重定向， 当网站更改域名或增加别名URL后， 可以通过规则轻松的指向要访问的URL。
mod_rewrite 使用基于正则的表达式动态的修改传入请求的url， 它可以将url映射到文件系统路径， 也可以将一个url映射到另一个url， 它使用了没有限制数量的规则来操作url， 每条规则可以包括没有限制数量的附加条件。 你可以通过多种方式来检测。 如， 服务器变量， 环境变量， HTTP头， 时间戳等。 mod_rewrite操作整个url路径， 包括path-info部分， 一个重写规则可以被设置在http.conf或者.htaccess中， 一个重写规则生成的路径可以包括一个请求字符串，或者内部的子程序处理， 外部请求的重定向，或者穿透内部代理等。   
* php框架中的url路由机制， 与apache重写类似， 一般包括通配符合正则两种规则。 让用户可以重新定向到（remap）url处理程序， 设定自己的路由规则。

### php trait特性

### interface接口 -- 需完善
可以存放public未实现的方法和const常量

### 如何实现反射 -- 需完善
序列号，反序列化数据进行反射

### 对两个有序链表进行排序

### php中变量的实现 -- 需完善
结构体 hash zval

### 如何把stirng转换成int
1. 强制转换
2. 使用intval函数

### 如何把一个int类型转换成string
1. 强制转换
2. settype

### COOKIE、SESSION的联系和区别， 多台web服务器如何共享Session
session是通过cookie来工作的，session和cookie之间是通过`$_COOKIE['PHPSESSID']`来联系的，通过`$_COOKIE['PHPSESSID']`可以知session的id，从而获取到其他的信息。cookie数据存放在客户的浏览器上,session数据放在服务器上,cookie有大小限制，为4K。

如何共享SESSION

* 利用数据库同步session

	sessionid 还是利用cookie机制存储到客户端， 但session数据却存放在mysql服务器上。
	
	缺点： 依赖性太强， Mysql无法工作时会影响所有的web服务器， 影响系统速度。
	
* 利用cookie同步session

	缺点： 
	1. 用户禁用cookie或者cookie泄漏，存在安全隐患。
	2. 浏览器对单个cookie的数据量大小限制为4k左右，因此会存在数据量的限制问题。
	3. 影响带宽性能，降低页面的访问速度。 在高访问量的情况下， 用户每次请求时， 都要将客户端cookie中的session值发送到服务器，要占用较多的带宽， 进而影响访问速度， 服务器带宽成本增加。
	
* 利用memcache、redis同步session

	推荐使用redis内存数据库， 因为它比memcache支持更多的数据类型， 且支持内存数据备份到磁盘。     
	缺点：memcache 把内存分成很多种规格的存储块， 有块就有大小，这种方式也就决定了memcache不能完全利用内存空间， 会产生内存碎片， 如果存储块不足， 还会产生内存溢出。
	
* 使用硬件设备

	这个算是比较成熟的解决方案了，使用类似BIG-IP的负载设备来实现资源共享，那么就能够又稳定又合理的的共享Session了。       
   目前很多门户网站采用这种方式。
   缺点:很明显，贵，不过对于专业或者大型应用来讲，是比较合理并且值得的。
   
#### require和include都可包含文件， 二者的区别何在
1. 当包含的文件不存在时,
    include:代码继续往下执行,报一个warning,
    而require则是报fatal error, 停止执行.

2. include 一般是放在流程控制的处理部分中。PHP 程序网页在读到 include 的文件时，才将它读进来。
    PHP 程序在执行前，就会先读入 require 所指定引入的文件。

3. 使用require()语句包含的文件不能有返回值。试图在require()语句中返回值将会产生一个解析错误。

    *_once只包含一次,即便多次调用.
    而不加once,则调用一次,包含一次.

    关于效率:
    加once需要检测已加载的文件,效率稍低,
    如果能确认不会多次包含,不必加once
    
#### 打开php.ini中的safe_mode， 会影响哪些函数
开启之后，主要会对系统操作、文件、权限设置等方法产生影响，平常项目基本上也用不到这些方法。主要是用来应对webshell吧，减少被人植入webshell所带来的某些安全问题。    
     
* move_uploaded_file() 检查被操作的文件或目录是否与正在执行的脚本有相同的 UID（所有者）。
* chdir() 检查被操作的目录是否与正在执行的脚本有相同的 UID（所有者）。
* exec() 只能在 safe_mode_exec_dir 设置的目录下进行执行操作。基于某些原因，目前不能在可执行对象的路径中使用。
* system() 只能在 safe_mode_exec_dir 设置的目录下进行执行操作。基于某些原因，目前不能在可执行对象的路径中使用。
* fopen() 检查被操作的目录是否与正在执行的脚本有相同的 UID（所有者）。
* mkdir() 检查被操作的目录是否与正在执行的脚本有相同的 UID（所有者）。
* chmod() 检查被操作的文件或目录是否与正在执行的脚本有相同的 UID（所有者）。 另外，不能设置 SUID、SGID 和 sticky bits
* mail() 在安全模式下，第五个参数被屏蔽。
* set_time_limit() 在安全模式下不起作用。
* header() 在安全模式下，如果设置了 WWW-Authenticate，当前脚本的 uid 将被添加到该标头的 realm 部分。
* dbmopen() 检查被操作的文件或目录是否与正在执行的脚本有相同的 UID（所有者）。
* dbase_open() 检查被操作的文件或目录是否与正在执行的脚本有相同的 UID（所有者）。
* escapeshellcmd() 将被作用于此函数的参数上。
* filepro() 检查被操作的文件或目录是否与正在执行的脚本有相同的 UID（所有者）。

## Mysql
### mysql的索引机制，复合索引的使用规则
Mysql的索引包括4类： 主键索引（primary key）、 唯一索引 (unique)、 常规索引(index), 全文索引(fullindex)

`show index from table_name;`  -- 查看表中的索引     
`show status like 'Handler_read%'`  --查看索引的使用情况

复合索引， 一般遵循最左前缀原则， 如table_a 的 a b c 三列建复合索引     
`create index ind_table_a on table_a(a,b,c);`那么，只有在条件中用到a,或者a、b,或者a、b、c这样的情况下，才会用到刚建的复合索引。


### Mysq的表类型及MyISAM与InnoDB的区别
myisam查找性能更优，表锁，全文索引    
innodb 支持外键，事务， 行锁    

### 如何对数据库优化
### mysql的引擎
MyISAM   
InnoDB   
memory

## Nosql ／ Redis
### redis主要用来做什么
处理高并发，缓存处理
### redis有什么劣势
redis是基于内存的，每次重启都需要重新读入数据，并且不是关系型数据库
### memcached和redis的区别
### mysql索引实现的原理：
B+树， 哈希

## nginx 
### 如何优化nginx  -- 需完善
设置缓存， 缓冲， 使用代理    
使用varnish

### nginx epoll和select的区别 -- 需完善
select 是基于轮询的
epoll  是触发式的

## 设计模式


## 开发经验
### php写API接口，需要注意什么 -- 需完善
 空值处理， 安全问题， 数据格式统一。
 
### 安全方面需要注意什么 -- 需完善
XSS攻击和sql注入    
* 如何防范sql注入  
   1. 进行转义
   2. 使用pdo bind进行变量绑定

### 大并发应如何处理

### https和http的区别 -- 需完善
https经过ssl加密， 证书
 