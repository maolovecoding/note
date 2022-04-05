## 一、为项目添加CSRF防护

在utils下新建common.js目录：

```js
function getRandomString(n){
    var str="";
    while(str.length<n){
      str+=Math.random().toString(36).substr(2);
    }
    return str.substr(str.length-n)
}
function csrfProtect(req, res, next){
    console.log("-------------------------------------csrfProtect")
    let method = req.method
    if(method=="GET"){
        let csrf_token = getRandomString(48);
        res.cookie('csrf_token', csrf_token);
        next() //执行跳转到下一个函数执行，即app.use(beforeReq,router)中的下一个
    }else if(method=="POST"){
        // 判断响应头中的x-csrftoken值，和cookies中的csrf_token进行对比
        console.log(req.headers["x-csrftoken"]);
        console.log(req.cookies["csrf_token"]);
        
        if((req.headers["x-csrftoken"] === req.cookies["csrf_token"]) && req.headers["x-csrftoken"]){
            console.log("csrf验证通过！");
            next()
        }else{
            res.json({errmsg:"csrf验证不通过!！"});
            return
        }
    }
}

module.exports = {
    csrfProtect,
}
```

在config.js中,添加csrf的防护效果：

```js
app.use(common.csrfProtect, indexRouter)
app.use("/passport",common.csrfProtect, passportRouter)
app.use(common.csrfProtect, detailRouter)
```

在/public/news/js/main.js中在post请求的ajax方法中添加:

```js
headers:{'X-CSRFToken':getCookie('csrf_token')},
```

## 二、项目中密码学

#### 2.1、扩展知识(了解)

base64编码

加密

单项散列函数

对称加密

非对称加密

#### 2.2、总结

1、为什么需要加密：

​	为了保证我们的数据能够在网络上进行安全的传输

2、常见加密算法：

- 不可逆算法：密码散列函数（英语：Cryptographic hash function），又译为加密散列函数、密码散列函数、加密散列函数，是散列函数的一种。它被认为是一种单向函数，也就是说极其难以由散列函数输出的结果，回推输入的数据是什么。这样的单向函数被称为“现代密码学的驮马”。这种散列函数的输入数据，通常被称为消息（message），而它的输出结果，经常被称为消息摘要（message digest）或摘要（digest）。
- 可逆算法：
  - 对称加密：将信息使用一个密钥进行加密，解密时使用同样的密钥，同样的算法进行解密。
  - 非对称加密：又称公开密钥加密，是加密和解密使用不同密钥的算法，广泛用于信息传输中。

3、项目中的密码学

​	项目中用户身份信息的密码字段都是使用密文进行存储（单项散列函数处理）

​	常见的单项散列函数处理方式有：   md5     和   sha256

#### 2.3、md5的使用

安装md5包：  yarn add md5

```js
const md5 = require("md5");
console.log(md5("hello"));      //5d41402abc4b2a76b9719d911017c592  
```

在线破解md5：<https://www.cmd5.com/> 

#### 2.4、项目中使用双重md5加盐来对密码进行存储前的加密

/routes/passport.js中：

```js
const Keys = require("../keys");
const md5 = require("md5");

....

//注册接口中，存储密码的时候：
let result2 = await handleDB(res,"info_user","insert", "info_user数据库新增出错",{
    username:username,
    nick_name:username,
    // password_hash:password,
    password_hash:md5(md5(password)+Keys.password_salt),
    last_login:(new Date()).toLocaleString(),
})
....
//登录接口中，验证密码的时候：
if(md5(md5(password)+Keys.password_salt) !== result2[0].password_hash){
    res.json({errmsg:"用户名或密码错误"});
    return
}

```

在项目文件夹下新建keys：

```js
module.exports = {
    session_keys:"%$#$^%*&^*%$REFDGWERDFG^%$^&*^$ERF$",
    password_salt:"DGFFDHRERDV%$$#&^&%^#%$RFDHG#$FGTHG^$%#FG"
}
```

## 三、RESTful风格接口

RESTful中REST，即Representational State Transfer的缩写 （译为"表现层状态转化"）

访问一个网站，就代表了客户端和服务器的一个互动过程。在这个过程中，势必涉及到数据和状态的变化。

互联网通信协议HTTP协议，是一个无状态协议。这意味着，所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"（State Transfer）。而这种转化是建立在表现层之上的，所以就是"表现层状态转化"。

客户端用到的手段，只能是HTTP协议。具体来说，就是HTTP协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。它们分别对应四种基本操作：**GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源。**

特点：

（1）每一个URI代表一种资源；

（2）客户端和服务器之间，传递这种资源的某种表现层；

（3）客户端通过四个HTTP动词，对服务器端资源进行操作，实现"表现层状态转化"。

一般这种接口返回出来是json数据

RESTful接口举例：

​	GET          /users              获取用户列表

​	GET          /users/1              获取id为1的用户

​	GET          /users/2              获取id为2的用户

​	PUT          /users/3              更新id为3的用户

​	POST        /users/4              插入id为4的用户

​	DELETE     /users/5              删除id为5的用户

​	GET    /token     登录

RESTful针对动态资源，路径为资源名词， 路径不包含后缀名(动态资源不写.html)

## 四、Json Web Token(JWT)的介绍

JSON Web Token（JWT）是一个非常轻巧的规范。这个规范允许我们使用JWT在两个组织之间传递安全可靠的信息。

> 官方定义:JSON Web Token (JWT) is a compact URL-safe means of representing claims to be transferred between two parties

![jwt](assets/jwt.png)

#### 4.1、jsonwebtoken的使用

安装jsonwebtoken

```js
yarn add jsonwebtoken
```

生成token值

```js
const jwt = require('jsonwebtoken');
const token = jwt.sign({id:1,username:"zhangsan"},salt,{expiresIn: 60 * 60 * 2})   //expiresIn为过期时间，单位是秒
```

验证token

```js
const jwt = require('jsonwebtoken');

const token = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwidXNlcm5hbWUiOiJ6aGFuZ3NhbiIsImlhdCI6MTU4NjEyNTUwMywiZXhwIjoxNTg2MTMyNzAzfQ.Uwx-EzPq2c9oDJxfs0nCrWLAcTS89HxPBqTUbx91gwY"
try{
    var userData = jwt.verify(token, salt);   //获取token中的数据(用户信息)
}catch(e){
    console.log("token已经过期")
}

console.log(userData)
```

#### 4.2、在项目中颁发token

```js
router.get("/token",(req,res)=>{       //就是所谓的登录操作
    
    const token = jwt.sign({id:1,username:"zhangsan"},Keys.jwt_salt,{expiresIn: 60 * 60 * 2})   // expiresIn

    // resful风格的接口返回的数据格式如下
    res.json({
        errmsg:"success", 
        errno:0,
        reason:"登录请求",
        result:{
            token,
        }
    });

})
```

前端请求到之后，最规范的操作是在请求的时候带上请求头 Authorization属性。



## 五、首页新闻数据列表数据展示

**首先明确(需求)：**

​	1、最新，指的是所有新闻中最新的数据，而不是一个叫“最新”的分类，所有的新闻的category_id字段不会为1 (所属的分类id不会为1， 1是“最新”)

​        2、先实现点击导航的分类，展示对应分类的新闻，并且没有刷新页面，即点击之后前端向后台服务器发起ajax请求，只是获取新闻数据，用GET方式请求

**前端传哪些参数给服务器？**
    1、点击导航的分类，要展示对应分类的新闻，应该告诉服务器此时点击的分类id（即代码中的cid）

​	2、一个分类对应多条新闻，不会在点击之后把该分类的所有新闻一次性展示完，比如，只展示最新的n条，这个n要告诉服务器(即代码中的per_page)

​	3、因为后台服务器通过limit分页查询查出数据返回给前端，涉及到第几页数据（并且后面下拉加载更多也需要告诉服务器要拿第几页的新闻），所以传当前页数((即代码中的page)       

**思路分析(步骤)：**
    1、获取参数，前端发送通过GET参数发送

​	2、分页查询新闻表中的新闻(根据分类id，即cid；降序；使用分页查询；查询到的是python的模型对象列表)

​	3、返回新闻数据

```js
router.get("/news_list", (req,res)=>{
    /*处理首页新闻数据列表的请求
    
    1、获取参数判空
    2、查询数据库  info_news   分类id  分页查询  perPage  currentPage
    3、返回数据给前端
    */
    (async function news_list(){
        // 1、获取参数判空
        // let {page, cid, per_page} = req.query; 
        let {page=1, cid=1, per_page=5} = req.query;  // 没传就给默认值
        

        
        // 2、查询数据库  info_news   分类id  分页查询  perPage  currentPage
        let wh = cid != 1?`category_id=${cid} order by create_time desc`:`1 order by create_time desc`;
        let result3 = await handleDB(res, "info_news", "limit", "info_news数据库查询出错", {where:wh,number:page,count:per_page}); 
       
        // 3、返回数据给前端
        res.json({newsList:result3})    //这里返回的数据，可以参考/public/news/js/index.js中回调函数中使用的属性

    })();
})
```

## 六、首页滚动到底部加载更多新闻数据

滚动到底部的功能，前端还需要两个参数totalPage(总页数) 和 currentPage (当前页数)

所以在后端/news_list 接口函数中需要加上这两个值的返回

```js
router.get("/news_list", (req,res)=>{
    /*处理首页新闻数据列表的请求
    
    1、获取参数判空
    2、查询数据库  info_news   分类id  分页查询  perPage  currentPage
    3、返回数据给前端
    */
    (async function news_list(){
        // 1、获取参数判空
        // let {page, cid, per_page} = req.query; 
        let {page=1, cid=1, per_page=5} = req.query;  // 没传就给默认值
        

        console.log(page, cid, per_page);
        
        // 2、查询数据库  info_news   分类id  分页查询  perPage  currentPage
        let wh = cid != 1?`category_id=${cid} order by create_time desc`:`1 order by create_time desc`;
        let result3 = await handleDB(res, "info_news", "limit", "info_news数据库查询出错", {where:wh,number:page,count:5}); 
       

        // ！！！！！总页数的计算
        let result4 = await handleDB(res, "info_news", "sql", "info_news数据库查询出错", "select count(*) from info_news where "+wh); 
        let total = result4[0]['count(*)'];   // 记录总条数
        let totalPage = Math.ceil(total/per_page)   // 总页数 = 向上取整(记录总条数/每页条数)
        console.log(totalPage);
        

        // 3、返回数据给前端
        // res.json({newsList:result3})
        res.json({newsList:result3, totalPage, currentPage:parseInt(page)})
        
    })();
})
```

## 七、创建详情页接口和详情页的渲染

routes下新建detail.js:

```js
const express = require("express");
const handleDB = require("../db/handleDb");
const router = express.Router();

router.get("/news_detail/:new_id", (req,res)=>{

    res.render("news/detail");
})

module.exports = router
```

config.js中注册该路由

```js
const detailRouter = require("./routes/detail");

let appConfig = app => {
	// 各个路由接口模块的注册
    app.use(indexRouter)
    app.use("/passport",passportRouter)
    
    app.use(detailRouter)
}
```

右侧点击排行中标题的点击跳转问题，在/views/detail.html：

```html
<a href="/news_detail/{{$value.id}}">{{$value.title}}</a>
```

这样就可以在首页点击新闻之后跳转到详情页。

如果详情页出现了样式路径问题，记得把“../css/reset.css ”修改成“/news/css/reset.css ”

## 八、base.html模板的抽取

在/views/news/中，拷贝index.html为base.html，base.html就是基类模板

拿base.html和detail.html进行对比，对于base.html中和detail.html中的不同之处：

#### 8.1、base.html中，不同之处去掉，替换成

```html
{{block 'titleBlock'}}{{/block}}
{{block 'scriptsBlock'}}{{/block}}
{{block 'menuBlock'}}{{/block}}
{{block 'contentLeftBlock'}}{{/block}}
{{block 'authorCardBlock'}}{{/block}}
```

#### 8.2、detail.html中，继承这个基类模板

```html
	{{extend "./base.html"}}
并且只需要些这5个不同之处的代码，例如：
	{{block 'titleBlock'}}文章详情页{{/block}}
	{{block 'scriptsBlock'}}<script type="text/javascript" src="/news/js/main.js"></script>
{{/block}}
	{{block 'menuBlock'}}{{/block}}
	{{block 'contentLeftBlock'}}  ... {{/block}}
	{{block 'authorCardBlock'}}  ... {{/block}}
```

注意：此时刷新详情页，右侧点击排行没有数据，页面右上角登录注册也没有数据，需要在detail.js的接口中获取这些数据，传到detail.html上，（这部分代码已经在index.js接口中写了，直接复制到detail.js的接口上即可）

```js
router.get("/news_detail/:new_id", (req,res)=>{
    (async function news_detail(){
        let user_id = req.session["user_id"];
        let result1=[];
        if(user_id){
            result1 = await handleDB(res, "info_user", "find", "user_info数据库查询出错", `id=${user_id}`); 
        }
        let result3 = await handleDB(res, "info_news", "limit", "info_category数据库查询出错", {where:"1 order by clicks desc",number:1,count:5}); 

        
        let data = {
            user_info:result1[0]?{
                nick_name:result1[0].nick_name,
                avatar_url:result1[0].avatar_url
            }:"",
            newsClick:result3
        }

        res.render("news/detail", data);
    })();
})
```

#### 8.3、index.html中，同样继承基类模板

```html
	{{extend "./base.html"}}
    并且只需要些这5个不同之处的代码，例如：
	{{block 'titleBlock'}}首页-News{{/block}}
	{{block 'scriptsBlock'}}<script type="text/javascript" src="/news/js/index.js"></script>{{/block}}
	{{block 'menuBlock'}}  ...  {{/block}}
	{{block 'contentLeftBlock'}}  ...  {{/block}}
	{{block 'authorCardBlock'}}  ...  {{/block}}
```
