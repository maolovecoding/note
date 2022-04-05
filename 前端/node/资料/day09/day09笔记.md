## 一、pathinfo/pathname风格的参数的获取

pathinfo/pathname风格参数如下：    例如： /detail/1/economy           （detail/:id/:type)

```html
<h1>这是列表页</h1>
<hr>
<ul>
    <li><a href="/detail/1">第1篇新闻标题</a></li>
    <li><a href="/detail/2">第2篇新闻标题</a></li>
    <li><a href="/detail/3">第3篇新闻标题</a></li>
    <li><a href="/detail/4">第4篇新闻标题</a></li>
</ul>
```

接口中：

```js
app.get("/list", (req,res)=>{
    
    res.render("list")
})

app.get("/detail/:id", (req,res)=>{
    
    console.log(req.params);
    //let {id, type} = req.params;   // 解构获取多个
    //console.log(id, type);

    res.send(" detail ok! ");
    
})
```

## 二、模板过滤器和模板继承的使用

文档地址：http://aui.github.io/art-template/zh-cn/docs/

#### 2.1、过滤器的使用

app.js中：

```js
const template = require('art-template');
template.defaults.imports.过滤器名字 = function(value){    //value接收html中“|”前面的值
    return value * 1000
};
```

html中：

```html
<p>num1是：{{num1 | 过滤器名字}}</p>
```

#### 2.4、模板继承的使用

父模板parent.html中：

```html
<h1>头部</h1>
{{block 'content'}}
<div>父内容</div>
{{/block}}
<div>尾部</div>
```

子模板child.html中：

```html
{{extend './parent.html'}} 
<!-- 与父模板一样的内容通过extend关键字继承过来 -->

<!-- 与父模板不一样的内容通过block关键字来区分 -->
{{block 'content'}}
    <div>子内容</div>
{{/block}}
```

app.js中：

```js
const express = require('express');
const path = require('path');
const app = express();

app.engine('html', require('express-art-template'));
app.set('view options', {
    debug: process.env.NODE_ENV !== 'production'
});
app.set('views', path.join(__dirname, 'templates'));
app.set('view engine', 'html');


app.get("/", (req,res)=>{
    //注意：！！这里渲染子模板
    res.render("child");
})


app.listen(3000, () => {
    console.log('Example app listening on port 3000!')
});
```



## 三、状态保持技术cookie和session

- 因为 http 是一种无状态协议，浏览器请求服务器是无状态的。
- **无状态**：指一次用户请求时，浏览器、服务器无法知道之前这个用户做过什么，每次请求都是一次新的请求。
- **无状态原因**：浏览器与服务器是使用 socket 套接字进行通信的，服务器将请求结果返回给浏览器之后，会关闭当前的 socket 连接，而且服务器也会在处理页面完毕之后销毁页面对象。
- 有时需要保持下来用户浏览的状态，比如用户是否登录过，浏览过哪些商品等
- 实现状态保持主要有两种方式：
  - 在客户端存储信息使用`Cookie`
  - 在服务器端存储信息使用`Session`

> 无状态协议：
>
> 1. 协议对于事务处理没有记忆能力
> 2. 对同一个 url 请求没有上下文关系
> 3. 每次的请求都是独立的，它的执行情况和结果与前面的请求和之后的请求是无直接关系的，它不会受前面的请求应答情况直接影响，也不会直接影响后面的请求应答情况
> 4. 服务器中没有保存客户端的状态，客户端必须每次带上自己的状态去请求服务器
> 5. 人生若只如初见

**状态举例：**

- 有状态：
  - A：你今天中午吃的啥？
  - B：吃的大盘鸡。
  - A：味道怎么样呀？
  - B：还不错，挺好吃的。
- 无状态：
  - A：你今天中午吃的啥？
  - B：吃的大盘鸡。
  - A：味道怎么样呀？
  - B：？？？啊？啥？啥味道怎么样？
- 所以需要cookie这种东西：
  - A：你今天中午吃的啥？
  - B：吃的大盘鸡。
  - A：你今天中午吃的大盘鸡味道怎么样呀？
  - B：还不错，挺好吃的。

#### 3.1、cookie

特点：
    1、cookie由服务器生成，保存在浏览器端的一小段文本信息
    2、cookie是以键和值得形式进行存储
    3、浏览器在访问一个网站的服务器时，会自动在请求头中把和本网站相关的所有cookie发送给服务器
    4、cookie是基于域名安全的
    5、cookie有过期时间，默认关闭浏览器之后过期

使用：

**先安装和引入：cookie-parser**     

**注册到app中：**

```js
const cookieParase = require('cookie-parser');
app.use(cookieParase());
```

**设置cookie：res.cookie('name', "node", {maxAge: 1000 * 60 * 60 * 2 }); **

**获取cookie：let name = req.cookies["name"]** 

完整代码如下：

```js
// 1、安装cookie-parser
// 2、引入cookie-parser并注册到app中
const cookieParase = require('cookie-parser');
app.use(cookieParase());

app.get("/setCookie",(req,res)=>{
    //设置cookie
    res.cookie('name', "node", {maxAge: 1000 * 60 * 60 * 2 });    // 过期时间：单位毫秒
    res.cookie('age', 11); 
    res.send("设置了cookie")
})

app.get("/getCookie",(req,res)=>{
    //获取cookie信息
    let name = req.cookies["name"];
    let age = req.cookies["age"];
    res.send(`获取cookie, ${name}, ${age}`);
})
```

#### 3.2、session

特点：
    1、session数据保存在服务器端
    2、session是以键和值的形式进行存储
    3、session依赖于cookie，每个session信息对应的客户端的标识保存在cookie中

使用：

**先安装和引入：cookie-session**     

```js
const cookieSession = require('cookie-session');
```

**注册到app中：**

```js
app.use(cookieSession({
    name:"my_session",  //name为session名，自己起一个字符串就行
    keys:["$^%%&^&%$RT%&TYGSGSFRR554785432$#@$$#%$@#%"],  // 内部加密需要的keys， keys为随机字符串
    maxAge: 1000 * 60 * 60 * 24   // 过期时间
}))
```

**设置session：req.session["name"] = "session_node"**

**获取session：let name = req.session["name"]**

完整代码如下：

```js
// 1、先安装：yarn add cookie-session     
//    如果报错，后面添加版本号，然后一直回车就好    yarn add cookie-session@2.0.0
// 2、设置到app中  
const cookieSession = require('cookie-session');
app.use(cookieSession({
    name:"my_session",  //name为session名，自己起一个字符串就行
    keys:["$^%%&^&%$RT%&TYGSGSFRR554785432$#@$$#%$@#%"],  // 内部加密需要的keys， keys为随机字符串
    maxAge: 1000 * 60 * 60 * 24   // 过期时间
}))

app.get("/setSession",(req,res)=>{
    //设置session
    req.session["name"] = "session_node"
    req.session["age"] = 11 
    
    res.send("设置了Session")
})

app.get("/getSession",(req,res)=>{
    //获取session信息
    let name = req.session["name"]
    let age = req.session["age"]
    res.send(`获取Session, ${name}, ${age}`);
})

```



## 四、数据库概述(了解)

#### 4.1、概念和作用

概念：数据库就是以一定格式进行组织的数据的集合。通俗来看数据库就是用户计算机上 一些具有特殊格式的数据文件的集合。 

对于普通用户来讲,在购买商品的时候看到的是这样的 

![1585258474139](assets/1585258474139.png)

对于后台程序来讲，看到的数据是这样的 

![1585258512326](assets/1585258512326.png)

再比如，用户在网站上能够看到微博及其相关的评论，这些数据同样也都来自于后台的数据库。

总之**数据库就是用来存储数据的**。

数据库有以下特点: 

- 持久化存储
- 读写速度极高
- 保证数据的有效性
- 对程序支持性非常好，容易扩展

#### 4.2、数据库管理系统

![1585258776340](assets/1585258776340.png)

数据库管理系统（英语：Database Management System，简称DBMS）是为管理数据库而设计的软件系统，包括三大部分构成:

1. 数据库文件集合. 主要是一系列的数据文件, 作用是存储数据.
2. 数据库服务端. 主要负责对数据文件以及文件中的数据进行管理.
3. 数据库客户端. 主要负责和服务端通信, 向服务端传输数据或者从服务端获取数据.

**他们之间的关系:**

1. 数据库客户端通过 SQL 语句告诉服务端, 客户端想要做什么.
2. 服务端和数据文件一般都在同一台机器上, 直接可以读写数据文件.

**由此可知:** 我们学习数据库，重点在于如何编写 **SQL 语句**.

#### 4.3、数据库分类

一般情况下，可以将数据库分为**关系型数据库**和**非关系型数据库**。

- 数据库排行榜: <https://db-engines.com/en/ranking>

#### 4.4、关系型数据库中核心元素

- 数据行(记录)
- 数据列(字段)
- 数据表(数据行的集合)
- 数据库(数据表的集合)

 ![1585259316904](assets/1585259316904.png)

#### 4.5、SQL

SQL 语句的作用是实现数据库客户端和服务端之间的通信. 其表现形式为: 带有一定格式的字符串.

1970年E.F.Codd的《A Relational Modelof Data forLarge Shared Data Banks》的论文开始讲起。该论文奠定了关系模型的理论基础，Codd的同事DonChamberlin对Codd的论文和关系运算进行转换，发明了简单易用的SQL语言，并且在之后的发展中成为所有关系型数据库的标准。

SQL(Structured Query Language)是结构化查询语言，是一种用来操作RDBMS的数据库语言。当前几乎所有关系型数据库都支持使用SQL语言进行操作,也就是说可以通过 SQL 操作 oracle,sql server,mysql,sqlite 等等所有的关系型的数据库。

- SQL语句主要分为：
  - **DQL：数据查询语言，用于对数据进行查询，如select**
  - **DML：数据操作语言，对数据进行增加、修改、删除，如insert、udpate、delete**
  - TPL：事务处理语言，对事务进行处理，包括begin transaction、commit、rollback
  - DCL：数据控制语言，进行授权与权限回收，如grant、revoke
  - DDL：数据定义语言，进行数据库、表的管理等，如create、drop
- **对于web程序员来讲，重点是数据的crud（增删改查），必须熟练编写DQL、DML，能够编写DDL完成数据库、表的操作，其它语言如TPL、DCL、CCL了解即可.**

**注意:** 不区分大小写

#### 4.6、安装数据库(见附录)

## 五、数据类型和约束——保证数据的完整性，有效性

**常用数据类型如下:**

- 整数：int，bit
- 小数：decimal
- 字符串：varchar,char, text
- 日期时间: date, time, datetime
- 枚举类型(enum)

**特别说明的类型如下：**

- decimal表示浮点数，如 decimal(5, 2) 表示共存5位数，小数占 2 位.
- char表示固定长度的字符串，如char(3)，如果填充'ab'时会补一个空格为`'ab '`.
- varchar表示可变长度的字符串，如varchar(3)，填充'ab'时就会存储'ab'
- 对于图片、音频、视频等文件，不存储在数据库中，而是上传到某个服务器上，然后在表中存储这个文件的保存路径.
- 字符串 text 表示存储大文本，当字符大于 4000 时推荐使用, 比如技术博客.

**数据约束：**

约束本质上是对数据在数据类型限定的基础上添加的额外的要求.

常见的约束如下:

1. 主键 primary key: 物理上存储的顺序. MySQL 建议所有表的主键字段都叫 id, 类型为 int unsigned.
2. 非空 not null: 此字段不允许填写空值.
3. 惟一 unique: 此字段的值不允许重复.
4. 默认 default: 当不填写字段对应的值会使用默认值，如果填写时以填写为准.
5. 外键 foreign key: 对关系字段进行约束, 当为关系字段填写值时, 会到关联的表中查询此值是否存在, 如果存在则填写成功, 如果不存在则填写失败并抛出异常.（仅做了解）

## 六、连接到数据库

连接到数据库之前先启动mysql服务：

net start mysql

通过以下命令在cmd窗口中链接到数据库：(先按照附录配置好环境变量)

mysql -uroot -p    (直接回车即可)

![1585260960239](assets/1585260960239.png)

看见以上界面说明已经登录mysql了，就可以在这里使用sql语句操作数据库了



