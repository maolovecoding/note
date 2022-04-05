# 案例

## 一、对每一个POST请求都设置CSRF防护

备注：今天的资料中web-scrf(Protected2)是在上一天的web-scrf(Protected)的基础上将登录页面改成ajax登录的方式。

实际上，不仅仅转账需要CSRF防护，每一个post请求都需要做csrf的防护措施。

webA项目中的app.js：

```js
const router = express.Router();

router.all("/register",(res,req)=>{
    ....
})

function csrfProtect(req, res, next){
    let method = req.method
    if(method=="GET"){
        let csrf_token = getRandomString(48);
        res.cookie('csrf_token', csrf_token);
        next() //执行跳转到下一个函数执行，即app.use(beforeReq,router)中的下一个
    }else if(method=="POST"){
        // 判断响应头中的x-csrftoken值，和cookies中的csrf_token进行对比
        console.log(req.headers["x-csrftoken"]);
        console.log(req.cookies["csrf_token"]);
        
        if((req.headers["x-csrftoken"] === req.cookies["csrf_token"])){
            console.log("csrf验证通过！");
            next()
        }else{
            res.send("csrf验证不通过!！");
            return
        }
    }
}

app.use(csrfProtect,router)
```



# 项目

## 一、项目从初始化

1、新建项目文件夹News

2、初始化项目   yarn init -y  或者  npm init -y  

3、安装express，yarn add express 或者  npm install express 

4、其次在 News 目录中，创建一个名为 app.js 的文件

```js
const express = require("express");
const app = express();

app.get("/",(req, res)=>{
    
    res.send("hello News");
})

app.listen(3000, ()=>{
    console.log('Example app listening on port 3000!');
})
```

浏览器中访问<http://localhost:3000/> ， 看到hello News，则项目初始化成功；

## 二、静态资源和模板引擎的设置

#### 2.1、设置静态资源

app.js中添加下面代码设置静态资源的访问路径：

```js
app.use(express.static("public")); 
```

#### 2.2、设置模板引擎

项目采用的是art-templates模板引擎，使用之前需要安装 art-template  和 express-art-template 

```
yarn add art-template   或者   npm install art-template
yarn add express-art-template  或者  npm install express-art-template
```

把静态资源复制大项目目录下，调整到如下图：

![1585068309693](assets/1585068309693.png) 

我们模板引擎的目录为 /views

```js
const path = require('path');

// 1、修改模板引擎为html，导入express-art-template
app.engine('html', require('express-art-template'));
// 2、设置运行的模式为开发模式
app.set('view options', {
    debug: process.env.NODE_ENV !== 'development'
});
// 3、设置模板存放目录为views文件夹
app.set('views', path.join(__dirname, 'views'));
// 4、设置引擎后缀为html
app.set('view engine', 'html');


app.get("/",(req, res)=>{
    res.render("news/index");
})
```

发布后发现，样式图片没有出来，引入路径不对，应该改成如下图：

![1585068821035](assets/1585068821035.png)

（快捷键提示Ctrl+shift+L： 选中某字符串后，批量选择本页面中出现的所有该字符串，之后就可以批量修改了）

## 三、项目中设置post请求方式的处理

用过前面的学习我们知道，获取post请求体参数的时候使用body-parser模块；

添加以下设置代码：

```js
// 1、引入body-parser
const bodyParser = require('body-parser');

// 2、bodyParser功能添加到项目app中
app.use(bodyParser.urlencoded({ extended: false }));// parse application/x-www-form-urlencoded   针对普通页面提交功能
app.use(bodyParser.json());  // parse application/json    针对异步提交ajax
```

之后在路有接口中就可以**通过req.body获取请求体**参数了。

## 四、添加cookie和session的配置

先安装cookie-parser和cookie-session ：

```
yarn add cookie-parser
yarn add cookie-session
```

app.js中添加设置：

```js
const cookieParser = require('cookie-parser');
const cookieSession = require('cookie-session');

// cookie的注册
app.use(cookieParser());  
// session的注册
app.use(cookieSession({
    name:"my_session",
    keys:["%$#^&^%&TSFR#$TRGDRG$%GFDG%^$#%#^GFDGRDHG$#@^Y%"],
    maxAge:1000*60*60*24*2    //过期时间设置为2天
}));

// 后面就可以通过 req.session[属性名] = 值来 设置session了
```

## 五、抽取app.js中的配置信息

目前，app.js已经越写越多了， 我们要把这个文件中的配置信息全部抽取出来作为一个独立的配置文件

项目目录下新建配置文件config.js

```js
const express = require("express");
const path = require("path");
const bodyParser = require('body-parser');
const cookieParser = require('cookie-parser');
const cookieSession = require('cookie-session');

var appConfig = app => {
    // 静态资源找寻的文件夹
    app.use(express.static("public")); 

    // 模板引擎
    app.engine('html', require('express-art-template'));
    app.set('view options', {
        debug: process.env.NODE_ENV !== 'development'
    });
    app.set('views', path.join(__dirname, 'views'));
    app.set('view engine', 'html');

    // 设置获取请求体
    app.use(bodyParser.urlencoded({ extended: false }));
    app.use(bodyParser.json()); 

    // cookie的注册
    app.use(cookieParser());  
    // session的注册
    app.use(cookieSession({
        name:"my_session",
        keys:["%$#^&^%&TSFR#$TRGDRG$%GFDG%^$#%#^GFDGRDHG$#@^Y%"],
        maxAge:1000*60*60*24*2    //过期时间设置为2天
    }));
}

module.exports = appConfig;
```

app.js中：

```js
// 引入配置文件
const config = require("./config");

// 创建应用程序对象
const app = express();

// 执行配置信息
config(app);
```

站在更高的角度，用面向对象的方式去写：

```js
class AppConfig{
    constructor(app){
        this.app = app
        this.app.use(express.static("public"));

        this.app.engine('html', require('express-art-template'));
        this.app.set('view options', {
            debug: process.env.NODE_ENV !== 'development'
        });
        this.app.set('views', path.join(__dirname, 'views'));
        this.app.set('view engine', 'html');

        this.app.use(bodyParser.urlencoded({ extended: false }));
        this.app.use(bodyParser.json()); 

        this.app.use(cookieParser());
        this.app.use(cookieSession({
            name:"my_session",
            keys: ["FGFDG#$%%YDFGSDHDRY$@#%$&^%*^%%%$^RFDGFJGRSA"],
            maxAge: 1000 * 60 *60 *24 * 2
        }));    
    }
}

//调用的时候：
let config = new Config(app)
```



## 六、抽取app.js中的路由接口函数

项目文件夹下新建routes文件夹，新建index.js:

```js
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
    //返回这个页面
    res.render('news/index');
});
module.exports = router
```

config.js中添加路由的设置

```js
const indexRouters = require('./routes/index');
var appConfig = app => {
    
    ...
    
 	// 路由的设置
    app.use(indexRouters);  //首页相关路由接口
}
```

## 七、项目数据表分析(见分析图)

## 八、添加数据库配置

#### 8.1、先创建数据库，并导入数据

把数据库导入数据有**两种方式**：

方式一：使用**带界面的Navicat**

![1589821499087](assets/1589821499087.png)



![1589821617590](assets/1589821617590.png)

**稍等5到10分钟，等待数据导入成功**

方式二：也可以直接在**cmd窗口**中通过连接数据库创建数据库后导入数据

先创建数据库

```shell
mysql -uroot -p

create database news charset=utf8;

use news;

source  G:\news.sql;    
```

**（注意：该目录不能出现中文！！！）**

**同样稍等5到10分钟，等待数据导入成功**

#### 8.2、项目中测试数据库

先安装mysql：

```
yarn add mysql 或者npm install mysql
```

把今天资料中提供的db文件夹放置到项目目录下，

测试数据库的使用，查询新闻分类表并相应回页面:

```js
const handleDb = require("../../db/handleDb");

router.get("/",(req, res)=>{
    (async function index(){
        let result = await handleDb(res,"info_category", "find", "info_category数据库查询出错");

        res.send(result);
    })();
})

```

## 九、初步获取图片验证码

先安装验证码获取的模块 ，在项目目录下：

```
yarn add svg-captcha
```

使用说明：

```js
let captchaObj = new Captcha(); // 创建对象
let captcha = captchaObj.getCode(); // 调用获取验证码的方法

captcha.text  //就是验证码文本
captcha.data  //就是验证码图片内容
//注意：返回给浏览器的时候，需要设置响应头
res.setHeader('Content-Type', 'image/svg+xml');

```

具体实现

项目目录下，新建utils文件夹，复制captcha文件夹到utils下。

routes下新建验证模块 passport.js：

```js
const express = require('express');
const Captcha = require('../utils/captcha');  // 引入captcha工具

const router = express.Router();

router.get('/password/image_code', (req, res) => {
    let captchaObj = new Captcha();  // 创建对象
    let captcha = captchaObj.getCode();  // 调用获取验证码的方法
    console.log("获取验证码的接口");
    console.log(captcha.text);   // 验证码文本

    //配合img标签的src属性请求来展示验证码图片的时候，需要设置响应头
    res.setHeader('Content-Type', 'image/svg+xml');  
    res.send(captcha.data);   // 响应验证码图片到浏览器
});

module.exports = router
```

config.js中注册，把passwordRouters注册到app上

```js
const passwordRouters = require('./routes/password');

var appConfig = app => {
    
    ...
    
 	// 路由的设置
    app.use(indexRouters);  //首页相关路由接口
    app.use(passwordRouters);  //验证相关路由接口(登录、注册、获取验证码等)
}
```

在浏览器中请求：<http://localhost:3000/password/image_code> 

## 十、注册流程分析（见注册流程分析图）

## 十一、完整验证码获取流程

```js
/*
    1、生成验证码
    2、将验证码文本存入session   （后面点击注册按钮的时候，注册接口中需要重新获取这个验证码文本和用户输入的验证码进行对比）
    3、发送验证码数据
*/

// 1、生成验证码
let captchaObj = new Captcha();  // 创建对象
let captcha = captchaObj.getCode();  // 调用获取验证码的方法
// 2、将验证码文本存入session   
req.session["IMG_CODE"] = captcha.text;
console.log(req.session)
// 3、发送验证码数据
res.setHeader('Content-Type', 'image/svg+xml');  //设置响应头
res.send(captcha.data);   // 响应验证码图片到浏览器
```

## 十二、解决点击获取验证码图片问题

/public/news/js/main.js中，加上一个随机数：

```js
// 1.设置图片url地址
image_url = '/passport/image_code/'+Math.random()

```

passport.js接口中：

```js
router.get("/passport/image_code/:float",  ...
```





