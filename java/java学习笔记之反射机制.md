# javaWeb 学习笔记之EL表达式


## 概述
EL, 即Expression Language。 在JavaWeb中属于JSP技术。 

JSP中的表达式:

1. Java脚本表达式： `<%=Java表达式%>`
2. EL表达式： `${EL表达式}`

JavaWeb中最好不要在Jsp中写Java代码， 所以用EL表达式来替代Java表达式。

## 语法及用法

#### 获取某个对象的之， 并将其输出到页面上
* 点运算符：

	`${stu.name}`, 即调用域中`stu`对应的对象`name`的读属性`getName()`. 但，若stu在4个域中没有绑定相应的对象， 那么就在网页上输出空字符串（即什么都不输出）。
	
	`${stu.name}`相当于：
	
	```java
	Student stu = pageContext.getAttribute("stu");
	if (stu != null) out.println(stu.getName());
	```
	
* []运算符

	`${stu['name']}`或者`${stu["name"]}` . 与点运算符作用一样， 但当索引或者名称不符合Java名称规范时， （如，获取数组中的某个值）， 只能用[] 运算符。

* 获取集合中的元素

	List按照索引来获取元素:`${obj.list[1]}`，Map按照指定的key来获取元素:`${obj.map.key}`或者`${obj.map['key']}`。
	
#### 逻辑和数学运算

* empty运算符

	判断一个对象是否为`null`或者`””`，是则返回`true`，否则返回`false`。也可以判断一个数组和集合，当数组或集合对象不是null，但对象中没有原素也返回`true`。

* 数学运算符

	`+-*/`, 如`${i+1}`

* 三元运算符

	`${表达式 ？ T表达式 : F表达式}`， 如 `${a>1 ? a+1 : a-1}`
	
* 不支持字符串连接

	如`${str + str}` , 这是错误的。
	
#### 11个内置对象
EL内置对象， 即在EL表达式中可以直接调用的对象。 总结起来，就是有一个JSP对象， 十个Map对象。

1. pageContext:与JSP内置对象pageContext完全一样。
2. pageScope:当前页面的域对象的Map。
3. requestScope:请求域对象的Map。
4. sessionScope:会话域对象的Map。
5. applicationScope:应用域对象的Map。
6. param:请求参数的Map，key为请求参数名，value为请求参数的值。
7. paramValues:请求参数(可重名)的Map<String,String[])，key为请求参数名，value为请求参数的值数组。
8. header:请求消息头Map，key为请求头名，value为对应的值。
9. headerValues:请求消息头(可重名)的Map<String,String[]>，key为请求名，value为请求头值数组。
10. cookie:封装cookie的Map<String,Cookie>，key为cookie的name属性，value为cookie对象本身。
11. initParam:初始化参数的Map，在web.xml中的context-param元素中定义的初始化参数。

## 自定义EL函数

#### 第一步： 定义一个静态方法的类

```java
package com.fuqiang.util;

public class StringUtil {
	public static String toUppercase(String str) {
		return "字符串";
	}
}
```

#### 第二步： 配置tld
在Web-INF目录下（也可以在其他目录下， 该目录下建立安全性更高）, 建立一个扩展名为tld的xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<taglib xmlns="http://java.sun.com/xml/ns/j2ee"  
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
        xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd"  
        version="2.0">  
        <tlib-version>1.0</tlib-version>  
        <short-name>myfn</short-name>  
        <uri>http://www.yrom.net/jsp/string/fn</uri>  
        <function>  
              <name>toUppercase</name>  
              <function-class>com.fuqiang.StringUtil</function-class>  
              <function-signature>java.lang.String toUpperCase( java.lang.String )</function-signature>  
        </function>  
   </taglib>
```

#### 第三步： 配置web.xml
如果tld文件是在Web-INF 目录或类路径下，此步可以跳过。

```java
<jsp-config>
<taglib>
	<taglib-uri>http://www.yrom.net/jsp/string/fn</taglib-uri>
	<taglib-location>/WEB-INF/myfn.tld</taglib-location>
</taglib>  
</jsp-config>
```

#### 第四步： 在JSP中使用
> 注意： 应先用taglib指令引入该uri

```
<%@ taglib uri="/该tld所在位置" prefix="myfn" %>  
...  
<body>  
<%  
   pageContext.setAttribute("a","aaa");  
%>  
   ${myfn:toUppercase(a)}  
</body>  
...
```


