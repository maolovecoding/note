## Json Web Token(JWT)

JSON Web Token（JWT）是一个非常轻巧的规范。这个规范允许我们使用JWT在两个组织之间传递安全可靠的信息。

> 官方定义:JSON Web Token (JWT) is a compact URL-safe means of representing claims to be transferred between two parties

JWT是一个有着简单的统一表达形式的字符串：

![jwt2](assets/jwt2.png)

##### 头部（Header）

头部用于描述关于该JWT的最基本的信息，例如其类型以及签名所用的算法等。 JSON内容要经Base64 编码生成字符串成为Header。

##### 载荷（PayLoad）

payload的五个字段都是由JWT的标准所定义的。

1. iss: 该JWT的签发者
2. sub: 该JWT所面向的用户
3. aud: 接收该JWT的一方
4. **exp(expires): 什么时候过期，这里是一个Unix时间戳**
5. iat(issued at): 在什么时候签发的

后面的信息可以按需补充。 JSON内容要经Base64 编码生成字符串成为PayLoad。

##### 签名（signature）

这个部分header与payload通过header中声明的加密方式，使用密钥secret进行加密，生成签名。 JWS的主要目的是保证了数据在传输过程中不被修改，验证数据的完整性。但由于仅采用Base64对消息内容编码，因此不保证数据的不可泄露性。所以不适合用于传输敏感数据。