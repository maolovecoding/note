## 一、简介

Koa 是现在最流行的基于Node.js平台的web开发框架。

- koa 是由 Express 原班人马打造的
- 致力于成为一个更小、更富有表现力、更健壮的 Web 框架。
- koa 不在内核方法中绑定任何中间件，它仅仅提供了一个轻量优雅的函数库，使得编写 Web 应用变得得心应手。

官网：https://koajs.com/

中文社区：https://www.koajs.com.cn/

安装：

```
yarn add koa  或者  npm i koa
```



## 二、hello world代码

- Koa 应用程序是一个包含`一组中间件函数的对象`，它是按照类似堆栈的方式组织和执行的。

```js
const Koa = require('koa');
const app = new Koa();

app.use(async ctx => {
  ctx.body = 'Hello World';
});

app.listen(3000, ()=>{
    console.log("server is running at port 3000");
});
```

## 三、路由中间件

[koa-router](https://www.npmjs.com/package/koa-router)

```
yarn add koa-router
```

使用：

```js
const Router = require('koa-router');
let router = new Router();
router.get("/", function (ctx){
    ctx.body = 'Hello World';
});

app.use(router.routes());
```

完整代码：

```js
// 路由使用
const Koa = require('koa');
const Router = require('koa-router');
const app = new Koa();

let router = new Router();

router.get("/", function (ctx){

    ctx.body = 'Hello World222';
});

router.get('/detail/:id', function (ctx) {
    ctx.body = ctx.params;
})

router.get('/list', function (ctx) {
    ctx.body = "list";
})

app.use(router.routes());

app.listen(3000, ()=>{
    console.log("server is running at port 3000");
});
```



## 四、中间件的执行流程



![](assets/16eda68e1b84e16e)

![](assets/16efc9f00cea9b30)

```js
// 中间件
const Koa = require('koa');
const Router = require('koa-router');
const app = new Koa();

let router = new Router();

router.get("/", function (ctx, next){
    console.log("1111");
    next();
    console.log("2222");
    ctx.body = 'Hello World';
}, function(ctx, next){
    console.log("3333");
    next()
    console.log("4444");
    ctx.body = 'Hello World222';
}, function(ctx, next){
    console.log("5555");
});

app.use(router.routes());

app.listen(3000, ()=>{
    console.log("server is running at port 3000");
});
```



## 五、获取请求参数

```js
const Koa = require('koa');
const app = new Koa();
app.use(async (ctx) => {
    console.log(ctx.method); //获取请求方法
    console.log(ctx.url);    //获取请求URL
    console.log(ctx.query);  //获取解析的查询字符串对象
    console.log(ctx.querystring); //根据 ? 获取原始查询字符串
    console.log(ctx.headers);//获取请求头对象
    ctx.body = ctx.url;
});

app.listen(3000, () => {
    console.log('server is starting at port 3000');
});
```



## 六、模板引擎使用

安装   art-template 和  koa-art-template

文档地址： http://aui.github.io/art-template/koa/

使用：

新建views文件夹，在views中新建login.html

```js
const render = require('koa-art-template');

render(app, {
  root: path.join(__dirname, 'views'),
  extname: '.html',
  debug: process.env.NODE_ENV !== 'production'
});

router.get("/", function (ctx, next){
    ctx.render("login")
})
```

完整代码：

```js
// art-template使用
const Koa = require('koa');
const path = require('path');
const Router = require('koa-router');
const render = require('koa-art-template');
const app = new Koa();

let router = new Router();

render(app, {
  root: path.join(__dirname, 'views'),
  extname: '.html',
  debug: process.env.NODE_ENV !== 'production'
});

router.get("/", function (ctx, next){
    ctx.render("login")
});

app.use(router.routes());

app.listen(3000, ()=>{
    console.log("server is running at port 3000");
});
```

## 七、获取请求体（POST参数）

安装   koa-bodyparser

```
yarn add koa-bodyparser
```

使用：

```js
const bodyParser = require('koa-bodyparser');
const app = new Koa();
app.use(bodyParser());

router.post("/", function (ctx){
    let post_params = ctx.request.body;  //ctx.request.body;这里的request不能省略
    ctx.body = post_params
});
```

完整代码：

```js
// 获取post请求参数
const Koa = require('koa');
const path = require('path');
const Router = require('koa-router');
const render = require('koa-art-template');
const bodyParser = require('koa-bodyparser');
const app = new Koa();
app.use(bodyParser());
let router = new Router();

render(app, {
  root: path.join(__dirname, 'views'),
  extname: '.html',
  debug: process.env.NODE_ENV !== 'production'
});

router.get("/", function (ctx){
    ctx.render("login")
});
router.post("/", function (ctx){
    let post_params = ctx.request.body;
    ctx.body = post_params
});
app.use(router.routes());

app.listen(3000, ()=>{
    console.log("server is running at port 3000");
});
```

## 八、会话cookie

```js
// 设置和获取cookie
const Koa = require('koa');
const Router = require('koa-router');
const app = new Koa();
let router = new Router();

router.get("/", function (ctx){
    ctx.cookies.set('name', 'nodejs2020', {maxAge: 1000 * 3600});   // 设置cookie
    let cookie_name = ctx.cookies.get('name');   // 获取cookie
    ctx.body = cookie_name
});

app.use(router.routes());

app.listen(3000, ()=>{
    console.log("server is running at port 3000");
});
```

## 九、会话session

安装：

```
yarn add koa-session
```

使用：

```js
const session = require('koa-session');
app.keys = ['$%^&**%$#%$#&^*&)*&*&^$%#$%$&^&**'];
app.use(session({maxAge:1000 * 3600}, app));
router.get("/", function (ctx){
    ctx.session.age = 11;    // 设置session
    ctx.body = ctx.session.age   // 获取session
});
```

完整代码：

```js
// 设置和获取session
const Koa = require('koa');
const Router = require('koa-router');
const session = require('koa-session');
const app = new Koa();

let router = new Router();
app.keys = ['$%^&**%$#%$#&^*&)*&*&^$%#$%$&^&**'];
app.use(session({maxAge:1000 * 3600}, app));
router.get("/", function (ctx){
    ctx.session.age = 11;

    ctx.body = ctx.session.age
});

app.use(router.routes());

app.listen(3000, ()=>{
    console.log("server is running at port 3000");
});
```

## 十、静态资源中间件

安装

```
yarn add koa-static
```

使用：

```js
const static = require('koa-static');
const path = require('path')
app.use(static(path.join( __dirname,  'public')))
```

完整代码：

```js
// 静态资源中间件
const Koa = require('koa');
const Router = require('koa-router');
const static = require('koa-static')
const path = require('path')
const app = new Koa();
app.use(static(path.join( __dirname,  'public')))

//通过http://localhost:3000/02.png访问到public文件夹下的02.png图片
let router = new Router();

router.get("/", function (ctx){
    ctx.body = "hello"
});

app.use(router.routes());

app.listen(3000, ()=>{
    console.log("server is running at port 3000");
});
```

## 十一、操作数据库

```js
// 链接数据库
const Koa = require('koa');
const Router = require('koa-router');
const handleDB = require('./db/handleDB');
const app = new Koa();

let router = new Router();

router.get("/", async function (ctx){
     
    let ret = await handleDB(ctx.res, "info_category", "find", "出错")
    ctx.body = ret

});

app.use(router.routes());

app.listen(3000, ()=>{
    console.log("server is running at port 3000");
});
```