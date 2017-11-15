## JWT介绍
> JSON Web Token（JWT）是为了在网络应用环境间传递声明而执行的一种基于 JSON 的开放标准（RFC 7519）。来自 JWT RFC 7519 标准化的摘要说明：JSON Web Token 是一种紧凑的，URL 安全的方式，表示要在双方之间传输的声明。JWT 一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该 Token 也可直接被用于认证，也可被加密。

## JWT 认证流程
![image](https://cdn.auth0.com/content/jwt/jwt-diagram.png)

1. 客户端调用登录接口 （或者获取token接口）, 传入用户名或密码（or code）
2. 服务端请求身份认证中心，确认用户名密码正确。
3. 服务端创建JWT， 返回给客户端
4. 客户端拿到JWT，进行存储（可以存储在缓存中，Cookie中，或localStore中）， 在后续请求中， 在HTTP请求头中加入JWT。 （官方使用： Authorization: Bearer <token>）
5. 服务端校验JWT， 校验通过后， 返回相关资源和数据。


## JWT 结构
JWT 是由三段信息构成， 第一段为头部 `Header`, 第二段为载荷`Payload`, 第三段为签名`Signature`。 每一段内容都是一个JSON对象， 将每一段JSON对象采用BASE64编码， 将编码后的内容用.链接 一起就构成了JWT字符串。 如下：
   
```
header.payload.signature
```

1. **头部（Header）**   
 头部用于描述该JWT的最基本信息， 例如其类型以及所用的算法等。 这也可以被表示成一个json对象。
 
```code
{
    "typ": "JWT",
    "alg": "HS256"
}
```

2. **载荷（payload）**   
 载荷就是存放有效信息的地方。有效信息包含三个部分：  
 - 标准中注册等声明
 - 公共的声明
 - 私有等声明 
 
标准中注册的声明（建议但不强制使用）：    

- iss:  JWT签发者
- sub:  JWT所面向的用户	
- aud:  接受JWT的一方
- exp:  JWT的过期时间， 这个过期时间必须要大于签发时间
- nbf:  定义在什么时间之前， 该JWT都是不可用的
- iat:  JWT的签发时间
- jti:  JWT的唯一身份标识， 主要用来作为一次性token， 从而回避重放攻击。   


公共的声明：

公共的声明可以添加任何的信息，一般添加用户的相关信息或其他业务需要的必要信息. 但不建议添加敏感信息，因为该部分在客户端可解密。

私有的声明：  

私有声明是提供者和消费者所共同定义的声明，一般不建议存放敏感信息，因为 base64 是对称解密的，意味着该部分信息可以归类为明文信息。

示例如下：
  
```code
{
    "iss": "Online Jwt Builder",
    "iat":  1416797419,
    "exp":  1448333419,
    "aud": "www.toon.mobi",
    "sub": "devops@syswin.com",
    "awe": { 
        feedId": "s_1497518172740704",
        "title": "刘智存 风险总监",
        "subtitle": "小聂科技/风险部",
        "avatarId": "http://static1.systoon.com/share/img/staff_180x180.png",
        "titlePinYin": "liu zhi cun feng xian zong jian",
        "userId": "336059",
        "cardNo": "s325076",
        "comId": "25120",
    }
}
```

3. **签名 (signature)**  
创建签名需要使用 Base64 编码后的 header 和 payload 以及一个秘钥。将 base64 加密后的 header 和 base64 加密后的 payload 使用. 连接组成的字符串，通过 header 中声明的加密方式进行加盐 secret 组合加密，然后就构成了 jwt 的第三部分。


比如：HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)

## JWT 优点：
1. 跨语言， JSON的格式保证了跨语言的支撑
2. 基于Token， 无状态 
3. 占用字节小， 便于传输

## 关于Token的注销：
Token的注销，由于Token不存储在服务端， 由客户端存储， 当用户注销时， Token的有效时间还没有到， 还是有效的。 所以如何在用户注销登录时让Token注销是一个关注的点。 一般有如下几种方式： 
  
1. Token存储在Cookie或localStore中，这样客户端注销时， 自然可以清空掉。
2. 注销时， 将Token存放到分布式缓存中， 每次校验Token时，检查下该Token是否已经注销，（麻烦，不建议使用）
3. 多采用短期令牌， 比如令牌有效期20分钟，这样可以一定程度上降低注销后Token的可用性风险。


## PHP 类库
采用官网（https://jwt.io/#libraries）推荐的php类库（Firebase）    
 
 ![image](http://oetghrmbm.bkt.clouddn.com/firebase.png)
 
```code
composer require firebase/php-jwt
```

## apache配置
```code
RewriteEngine On
RewriteCond %{HTTP:Authorization} ^(.*)
RewriteRule .* - [e=HTTP_AUTHORIZATION:%1]
```

