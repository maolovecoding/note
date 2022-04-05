## 一、图片验证码最后的补充

当用户点击注册按钮，我们会有/passport/register来处这个请求，其中有个业务步骤是也需要验证用户输入的验证码和我们在服务端生成的验证码是否一致。

所以在后端生成图片验证码的同时，需要保存图片验证码文本到session中。

```js
router.get("/passport/image_code/:float_random", (req,res)=>{
    (async function index(){
        let captchaObj = new Captcha(); 
        let captcha = captchaObj.getCode(); 

        // 保存文本到session中
        req.session["IMAGE_CODE"] = captcha.text
        console.log(req.session);  
        
       	//res.type(svg)
        res.setHeader('Content-Type', 'image/svg+xml');
        res.send(captcha.data) 
    })();
})
```



## 二、注册流程分析

业务流程：

1、获取参数，判空  （用户名、密码、图片验证码、agree）

2、对比验证码， 如果不一致要返回验证码错误，return

3、查询数据库，看看是否存在这个用户名

4、如果已经存在，返回用户名已经被注册 return

5、不存在，则往数据库中新增加一条记录

6、保持登录状态

7、返回响应成功

passport.js中：

```js
router.post("/passport/register", (req,res)=>{
    /*
    1、获取参数，判空  （用户名、密码、图片验证码、agree）
    2、对比验证码， 如果不一致要返回验证码错误，return
    3、查询数据库，看看是否存在这个用户名
    4、如果已经存在，返回用户名已经被注册 return
    5、不存在，则往数据库中新增加一条记录
    6、保持登录状态
    7、返回响应成功
    
    */
    (async function register(){
        
        // 1、获取参数，判空  （用户名、密码、图片验证码、agree）
        let {username, password, image_code, agree} = req.body
        console.log(username, password, image_code, agree);
        if(!username || !password || !image_code || !agree){
            res.json({errmsg:"缺少必传参数"});
            return
        }
        // 2、对比验证码， 如果不一致要返回验证码错误，return
        if(image_code.toLowerCase() !== req.session["IMAGE_CODE"].toLowerCase()){
            res.json({errmsg:"图片验证码错误"});
            return
        }
        // 3、查询数据库，看看是否存在这个用户名
        let result = await handleDB(res,"info_user","find", "info_user数据库查询出错",`username="${username}"`)   //username="${username}"这里注意加引号！！！
        console.log(result);  //数组， []
        // 4、如果已经存在，返回用户名已经被注册 return
        if(result.length>0){
            res.json({errmsg:"用户名已经被注册"});
            return
        }
        // 5、不存在，则往数据库中新增加一条记录
        let result2 = await handleDB(res,"info_user","insert", "info_user数据库新增出错",{
            username:username,
            nick_name:username,
            password_hash:password,
        })
        console.log(result2);  // 这个插入数据的结果有一个属性叫insertId，就是新增的用户id
        
        // 6、保持登录状态
        req.session["user_id"] = result2.insertId
        // 7、返回响应成功
        res.json({errmsg:"注册成功", errno:"0"});

    })();
})
```

## 三、登录业务流程

业务流程：

1、获取参数，判空  （用户名、密码） 

2、查询数据库，验证用户名是不是已经注册了

3、如果没有注册，则返回用户名未注册，登录失败 return

4、校验密码是否正确，如果不正确 就return

5、保持用户的登录状态

6、返回登录成功

passport.js中：

```js
router.post("/passport/login", (req,res)=>{
    /*
    1、获取参数,判空 
    2、查询数据库,验证手机号码是不是已经注册了
    3、如果没有注册，则返回用户名未注册，登录失败 return
    4、校验密码是否正确,如果不正确 就return
    5、保持用户的登录状态
    6、返回登录成功
    */
    (async function register(){
        
        // 1、获取参数，判空  （用户名、密码）
        let {username, password} = req.body
        if(!username || !password ){
            res.json({errmsg:"缺少必传参数"});
            return
        }
        // 2、查询数据库,验证手机号码是不是已经注册了
        let result = await handleDB(res,"info_user","find", "info_user数据库查询出错",`username="${username}"`);

        // 3、如果没有注册，则返回用户名未注册，登录失败 return
        if(result.length==0){
            res.json({errmsg:"用户名未注册，无法登录"});
            return
        }

        // 4、校验密码是否正确,如果不正确 就return
        if(password !== result[0].password_hash){
            res.send({errmsg:"用户名或者密码错误，登录失败"});
            return
        }
        // 5、保持登录状态
        req.session["user_id"] = result[0].id
        // 6、返回响应成功
        res.json({errmsg:"登录成功", errno:"0"});

    })();
})
```

## 四、首页登录状态的展示

来到首页应该先验证用户是否登录：

1、看看session中是否能获取到一个user_id

2、如果能拿到user_id，查询数据库，看这个id是否有效

index.js中：

```js
router.get("/", (req,res)=>{
    (async function index(){
        // 获取session["user_id"]，看用户是否登录
        let user_id = req.session["user_id"];
        // 如果获取到user_id，则查询数据库，查询这个id的用户信息
        let result1=[];
        if(user_id){
            result1 = await handleDB(res, "info_user", "find", "user_info数据库查询出错", `id=${user_id}`); 
        }
        
        let data = {
            user_info:result1[0]?{
                nick_name:result1[0].nick_name,
                avatar_url:result1[0].avatar_url
            }:null
        }
        res.render("news/index", data);
    })();
})
```

views/index.html下：

```html
{{ if !user_info }}
    <div class="user_btns fr">
        <a href="javascript:;" class="login_btn">登录</a> / <a href="javascript:;" class="register_btn">注册</a>
    </div>
    {{ else }}
	<!-- 用户登录后显示下面，隐藏上面 -->
    <div class="user_login fr">
        <img src={{user_info.avatar_url?user_info.avatar_url:"/news/images/person01.png"}} class="lgin_pic">
        <a href="#">{{user_info.nick_name}}</a>
        <a href="#" onclick="logout()">退出</a>
    </div>
{{ /if }}
```

## 五、退出登录接口

passport.js中

```js
router.post('/passport/logout', (req, res) => {

    delete req.session["user_id"] 
    res.json({errno:"0", errmsg:"退出登录成功"})   
})
```

## 六、给用户设置最后一次登录时间

给用户设置最后一次登录时间

(new Date()).toLocaleString() 方法是js用来获取当前时间字符串的方法

```js
//注册接口中, 新增一条用户记录的时候：
		// 5、不存在，则往数据库中新增加一条记录
        let result2 = await handleDB(res,"info_user","insert", "info_user数据库新增出错",{
            username:username,
            nick_name:username,
            password_hash:password,
            last_login:(new Date()).toLocaleString(),
        })

//登录接口中, 用户登录之前：
		// 5、保持登录状态
        handleDB(res,"info_user","update", "info_user数据库查询出错",`id=${result[0].id}`,{last_login:(new Date()).toLocaleString()});
        req.session["user_id"] = result2[0].id
```

## 七、首页头部分类

服务端查询数据库，info_category数据表，传递给模板循环渲染

routes/index.js中

```js
let Category = db.model('info_category');		

		//-------------------以上是登录功能
        // 头部分类
        let result2 = await handleDB(res, "info_category", "find", "info_category数据库查询出错", ["name"]); 

		let data = {
            userInfo: result1[0]?{
                nick_name:result1[0].nick_name,
                avatar_url:result1[0].avatar_url,
            }:null,
            category:result2  // 传到html页面中
        }
```

views/index.html中：

```html
<ul class="menu fl">
    {{ each category}}
    	<li data-cid={{$index+1}} class={{$index==0?"active":""}}><a href="javascript:;">{{$value.name}}</a></li>
    {{ /each }}
</ul>
```

## 八、右侧点击排行

routes/index.js中

```js
	// 右侧点击排行
    let result3 = await handleDB(res, "info_news", "sql", "info_category数据库查询出错", "select title from info_news order by clicks desc limit 6"); 
    console.log(result3);

	let data = {
           ...
            newsClick:result3
    }
```

views/index.html中:

```html
<ul class="rank_list">
    {{each newsClick}}
    <li><span class="first">{{$index+1}}</span><a href="#">{{$value.title}}</a></li>
    {{/each}}
</ul>
```

## 九、右侧点击排行小图片样式处理

以上写法导致左侧小图标样式都是一样的，需要处理成原样式。

使用过滤器，根据下标进行过滤

views/index.html中：

```html
{{ each click_titles}}
<li><span class={{ $index | classNameFilter }}>{{$index+1}}</span><a href="#">{{$value.title}}</a></li>
{{ /each }}
```

接下来书写classNameFilter过滤器

在utils文件夹中新建filters.js:

```js
const template = require('art-template');
template.defaults.imports.classNameFilter = function(value){    //value接收html中“|”前面的值
    if(value===0){
        return "first"
    }else if(value===1){
        return "second"
    }else if(value===2){
        return "third"
    }else{
        return ""
    }
};
```

**routes/index.html中引入过滤器文件**

```js
require('../utils/filters');
```

