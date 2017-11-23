## maven介绍

## maven的安装和配置

* 从官网下载最新的Maven包：[官网](http://maven.apache.org/download.html), 当前最新版本：3.5.2
* 解压缩到本地并放置到/usr/local/apache-maven-3.5.2目录： `mv ~/apache-maven-3.5.2 /usr/local/apache-maven-3.5.2`
* 修改.bash_profile文件，添加apache-maven-3.5.2/bin到path变量

	```
	export MAVEN_ROOT=/Users/syswin_/apache-maven-3.5.2
   export GOROOT=/usr/local/go
   export GOPATH=$HOME/go
   export PATH=$HOME/bin:$MAVEN_ROOT/bin:$GOROOT/bin:$GOPATH/bin:$PATH
   
   ## 保存后，执行source .bash_profile
	```
* 检查maven是否安装成功， 在命令行中执行： 
	`mvn -version`

## 常用命令
* 创建Maven的普通java项目

	```java
	mvn archetype:create -DgroupId=packageName -DartifactId=projectName
	```
* 创建Maven的web项目
	
	```java
	mvn archetype:create -DgroupId=packageName -DartifactId=webappName -DarchetypeArtifactId=maven-archetype-webapp
	```

* 编译源代码：
	
	```java
	mvn compile
	```

* 编译测试代码

	```java
	mvn test-compile
	```
	
* 运行测试

	```java
	mvn test
	```
* 产生site：
	
	```java
	mvn site
	```
	
* 打包
	
	```java
	mvn package
	```
	
* 在本地Repository中安装jar

	```java
	mvn install
	```
	
* 清除产生的项目

	```java
	mvn clean
	```
	
* 生成eclipse项目

	```java
	mvn eclipse:eclipse
	```
	
* 生成idea项目

	```java
	mvn idea:idea
	```
	
* 组合使用goal命令，如只打包不测试

	```java
	mvn -Dtest package
	```
	
* 只打jar包

	```java
	mvn jar:jar
	```

* 只测试而不编译，也不测试编译

	```java
	mvn test -skipping compile -skipping test-compile
	```
	
* 清除eclipse的一些系统设置

	```java
	mvn eclipse:clean
	```

