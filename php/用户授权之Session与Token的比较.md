## 授权概述
做网站，做App端接口，都会涉及到用户登录问题， 网站登录传统的做法是采用Session来进行验证， 移动App通常采用Token进行验证， 下面简要分析下二者不同。

## Session与Cookie

Session的工作原理 
   
1. 生成全局唯一标识符(SessionID)
2. 开辟数据存储空间， 可以时服务器内存或持久化存储。
3. 将sessionID发送给客户。
4. 客户端发送每一条request的时候附带sessionID（这样就可以知道状态了）, 一般来说会有两种常用的方式： cookie或URL重写  
 - Cookie， 客户端访问的时候附带包括了sessionID的cookie， 这样服务器就根据cookie获取session
 - URL重写， 如果客户端禁用了cookie， 可以直接在URL后面上加上sessionID
 

## Token工作原理

Token和Session有第一定的类似，但是服务器不保存状态，而是生成一个Token保存在客户端，这个Token是加密并确保完整性和不变性的，也就是修改后无效的，所以是安全的，可以保存在客户端。

原理如下：
![image](http://www.intelligentunit.com/authorization-with-session-token-principle-and-compare/jwt-diagram.png)


## 二者比较

Session的状态是存储在服务器端，客户端只有session id；而Token的状态是存储在客户端