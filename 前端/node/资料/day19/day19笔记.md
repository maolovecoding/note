## 一、基本资料的修改(POST提交)

1、获取参数，判空

2、修改数据库中的数据（签名、昵称、性别）

3、返回操作成功

"/user/base_info" 接口中：

```js
		...
    	...
        }else if(req.method === "POST"){
            // 1、获取参数，判空
            let {signature, nick_name, gender} = req.body;
            if(!signature || !nick_name || !gender){
                res.send({errmsg:"参数错误1"})
                return
            }
            // 2、修改数据库中的数据
            await handleDB(res, "info_user", "update", "查询数据库出错", `id=${userInfo[0].id}`,{
                signature,
                nick_name,
                gender
            });
            // 3、返回操作成功
            res.send({errno:"0", errmsg:"操作成功"});
        }
```

user_base_info.html中，两个input标签价value值：

```html
<input class="gender" type="radio" name="gender" value="MAN" {{if gender=="MAN"}}checked{{/if}}> <b>男</b>&nbsp;&nbsp;&nbsp;&nbsp;
<input class="gender" type="radio" name="gender" value="WOMAN" {{if gender=="WOMAN"}}checked{{/if}}>  <b>女</b>
```

前端user_base_info.js中，源代码是news方法，改成html方法，否则不起作用

```js
// 更新父窗口内容
$('.user_center_name', parent.document).html(params['nick_name'])
$('#nick_name', parent.document).html(params['nick_name'])
```

## 二、修改密码完成

user.html中修改：

```html
<li><a href="/user/pass_info" target="main_frame">密码修改</a></li>
```

在前端user_pass_info.js中，释放ajax代码。

后端业务流程：

1、获取参数, 判空  (旧密码， 新密码*2)
2、校验两新密码是否一致
3、校验旧密码是否正确
4、修改数据库中的密码 
5、返回结果

```js
router.all("/user/pass_info",(req, res)=>{

    (async function(){
        // 获取用户登录信息，如果获取不到，就重定向到首页
        let userInfo = await common.getUser(req, res);   
        if(!userInfo[0]){
            res.redirect("/");  
        }
        
        if(req.method === "GET"){
          
          
            res.render("news/user_pass_info")
        }else if(req.method === "POST"){
            // 1、获取参数, 判空  (旧密码， 新密码*2)
            let {old_password, new_password, new_password2} = req.body;
            if(!old_password || !new_password || !new_password2){
                res.send({errmsg:"参数错误1"})
                return
            }
            // 2、校验旧密码是否正确
            if(md5(md5(old_password)+keys.password_salt) !== userInfo[0].password_hash){
                res.send({errmsg:"旧密码不正确，修改失败"})
                return
            }
            // 3、校验两新密码是否一致
            if(new_password !== new_password2){
                res.send({errmsg:"俩新密码不一致，修改失败"})
                return
            }

            // 4、修改密码 提交
            await handleDB(res, "info_user", "update", "查询数据库出错", `id=${userInfo[0].id}`,{
                password_hash:md5(md5(new_password)+keys.password_salt)
            });
            // 5、返回操作成功
            res.send({errno:"0", errmsg:"操作成功"});
        }
        

    })(); 
})
```

## 三、抽取获取用户信息函数

common.js中

```js
// 获取用户信息函数getUser2
async function getUser2(req, res){
    let userInfo = await getUser(req, res);   
    if(!userInfo[0]){
        res.redirect("/");  
    }
    return userInfo
}
module.exports = {
    ...
    getUser2
}
```

profile.js中：

```js
router.get("/profile",(req, res)=>{
    
    (async function(){
        
        // let userInfo = await common.getUser(req, res);   
        // if(!userInfo[0]){
        //     res.redirect("/");   
        // }
        // 把获取用户信息的代码改成调用：
        let userInfo = await common.getUser2(req, res);

        ....
    })(); 
})
```

## 四、头像上传接口初步使用

安装：yarn add multer

使用：

1、引入和使用：

```js
const multer = require('multer');
const upload = multer({ dest: '设置上传图片的保存路径' })	
```

2、接受图片提交的接口：

```js
router.post("接口路径", upload.single('avatar'), (req, res)=>{  // avatar为file表单的name属性
    
  	req.file;   // 提交之后通过req.file获取本次提交的信息对象
    
    
  	/*
    { fieldname: 'avatar',
      originalname: 'avatar.jpg',
      encoding: '7bit',
      mimetype: 'image/jpeg',
      destination: 'public/upload/avatar',
      filename: 'ad84a9e186d0e9deaa774c237076cd8b',
      path: 'public\\upload\\avatar\\ad84a9e186d0e9deaa774c237076cd8b',
      size: 84657 }

    */
})
```

完整代码：

```js
const multer = require('multer');
const upload = multer({ dest: 'public/news/upload/avatar' })	

router.get("/user/pic_info",(req, res)=>{
    (async function(){

        let userInfo = await common.getUser2(req, res);
        res.render("news/user_pic_info")
    })(); 
})
router.post("/user/pic_info", upload.single('avatar'), (req, res)=>{
    console.log("请求了/user/pic_info");
    var file = req.file;
    console.log(file);
    res.send(file);
})
```

## 五、利用七牛云实现图片上传功能

七牛云官网 ：https://www.qiniu.com/

安装七牛  yarn add qiniu 

把qn.js放到utils文件夹下：

```js
const upload_file = require("../utils/qn");

router.post("/user/pic_info", upload.single('avatar'), (req, res)=>{
    (async function(){
        var file = req.file;
        //upload_file(上传后的名字，上传的图片路径)   //上传的图片相对路径, 从项目文件夹出发
        var qnObj = await upload_file('01.jpg', './01.jpg');
    })();
}) 
```

## 六、完整上传图片的接口处理

1、接收req.file对象

2、上传至七牛云

3、把头像链接保存到数据库中

4、传递avatar_url前端回调用于界面展示

```js
router.post("/user/pic_info", upload.single('avatar'), (req, res)=>{
    (async function(){
        let userInfo = await common.getUser2(req, res);

        // 1、接收req.file对象
        var file = req.file;
        /*
        console.log(file);
        { fieldname: 'avatar',
        originalname: 'avatar.jpg',
        encoding: '7bit',
        mimetype: 'image/jpeg',
        destination: 'public/upload/avatar',
        filename: 'ad84a9e186d0e9deaa774c237076cd8b',
        path: 'public\\upload\\avatar\\ad84a9e186d0e9deaa774c237076cd8b',
        size: 84657 }
        */
        // 2、上传至七牛云
        try{
            var qnObj = await upload_file(file.originalname, "./public/news/upload/avatar/"+file.filename);
        }catch(err){
            console.log(err);
            res.send({errno:"0", errmsg:"七牛云上传出错"});
            return 
        }
        /*
        console.log(qnObj);
        qnObj对象：
        { hash: 'FvIbAK91uDfn0a-mXMiMWyW0w5HP',
        key: 'image/activity/03.png' }
        */
        // 3、把qnObj.key保存在数据库中
        let ret = await handleDB(res, "info_user", "update", "数据库更新出错", `id=${userInfo[0].id}`,{
            avatar_url:qnObj.key
        })
        console.log(ret);  
        
        // 4、传递avatar_url前端回调用于界面展示
        let data={avatar_url:constant.QINIU_URI+qnObj.key}
        
        res.send({errno:"0", errmsg:"设置成功", data});
    })();
})
module.exports = router
```



## 七、跨域介绍

跨域，是指**浏览器不能执行其他网站的脚本**。它是**由浏览器的同源策略造成**的，是浏览器对JavaScript实施的安全限制。

同源策略限制了一下行为：

- Cookie无法读取
- DOM 和 JS 对象无法获取
- Ajax请求发送不出去

同源是指，域名、协议、端口均为相同

**为什么需要用到跨域？**

1、自身业务是出现很多端(前后端分离开发)   2、和第三方合作  3、面试经常问

**如何处理跨域带来的ajax问题？**(解决跨域方案)

1、jsonp  

2、设置代理服务器  （正向代理，  反向代理）

3、后端设置响应头Access-Control-Allow-Origin

```
res.setHeader("Access-Control-Allow-Origin", "*")
```

跨域代码演示：

后端代码：

```js
// 入口文件
const express = require("express");

const app = express();

app.get("/get_data",(req, res)=>{
    
    res.send({name:"node", age:"11"})
})

app.listen(3001, ()=>{
    console.log(`服务器已经启动，端口为：3001`);
})
```

html代码（右键Open in Live Server）：

```html
<!--引入JQ代码-->
<script src="http://libs.baidu.com/jquery/2.0.0/jquery.min.js"></script>
```

```html
<p><span id="sp1"></span>的年龄是<span id="sp2"></span></p>
<script>
    $.ajax({
        url:'http://localhost:3001/get_data',
        type:"GET",
        success: function(data){
            $("#sp1").html(data.name)
            $("#sp2").html(data.age)
        }
    });
</script>
```



## 八、JSONP介绍

处理使用ajax代码发起请求外，页面某些标签也会自动发起请求。我们可以利用script标签的src属性，来发起请求。

jsonp 就是前端利用 script 在页面不刷新的情况下和服务器进行交互一种技术。拿 json 格式的数据去填充一个函数，英语：json with paddding a function 简称：jsonp

#### 8.1、使用jsonp原理来解决跨域

前端代码：

```html
<p><span id="sp1"></span>的年龄是<span id="sp2"></span></p>
<script>
    function callback(data){

    	console.log("执行了callback");
        $("#sp1").html(data.name)
        $("#sp2").html(data.age)
    }    
</script>
<!-- jsonp 原理，不会出现跨域-->
<script src="http://localhost:3001/get_data"></script>
```

后端代码：

```js
// 入口文件
const express = require("express");

const app = express();

app.get("/get_data",(req, res)=>{
    
    // 按照jsonp原理来响应:
    res.send('callback({name:"node", age:"11"})')
})

app.listen(3001, ()=>{
    console.log(`服务器已经启动，端口为：3001`);
})
```

#### 8.2、express封装好的jsonp方法

后端代码：

```js
// 入口文件
const express = require("express");

const app = express();

app.get("/get_data",(req, res)=>{

    // express封装好的方法jsonp
    res.jsonp({name:"node", age:"11"})
})

app.listen(3001, ()=>{
    console.log(`服务器已经启动，端口为：3001`);
})
```

前端代码：

```html
<script src="http://localhost:3001/get_data?callback=callback"></script>
<!--这里第一个callback是固定，=号后面的callback是我们回调函数的名字 -->
```

## 九、后端设置响应头

#### 9.1、直接设置响应头Access-Control-Allow-Origin

设置响应头：res.setHeader("Access-Control-Allow-Origin", "*")

后端代码：

```js
const express = require("express");

const app = express();

app.get("/get_data",(req, res)=>{

    res.setHeader("Access-Control-Allow-Origin", "*")
    res.send({name:"node", age:"11"})
})

app.listen(3001, ()=>{
    console.log(`服务器已经启动，端口为：3001`);
})
```

前端代码无需任何设置。

```html
<p><span id="sp1"></span>的年龄是<span id="sp2"></span></p>
<script>
    $.ajax({
        url:'http://localhost:3001/get_data',
        type:"GET",
        success: function(data){
            $("#sp1").html(data.name)
            $("#sp2").html(data.age)
        }
    });
</script>
```

#### 9.2、使用cors模块

```js
const cors = require("cors");

app.use(cors())
```

