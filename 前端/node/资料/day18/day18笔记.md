## 一、点赞后端业务的处理

处理  点赞和取消点赞  的请求
    1、获取用户登录信息
    2、获取参数判空    评论id  action
    3、查询数据库，看评论是否存在
    4、添加点赞到info_comment_like数据表、 评论的like_count字段+1
    5、返回成功

```js
router.post("/news_detail/comment_like", (req,res)=>{
    /*

    处理  点赞和取消点赞  的请求
        1、获取用户登录信息
        2、获取参数判空    评论id  action
        3、查询数据库，看评论是否存在
        4、添加点赞到info_comment_like数据表、 评论的like_count字段+1
        5、返回成功

    */
    (async function(){
        // 1、获取用户登录信息
        let userResult = await common.getUser(req, res);
        if(!userResult[0]){
            res.json({errno:"4101",errmsg:"未登录"});
            return
        };
        // 2、接收参数,判空
        let {comment_id, action} = req.body;
        if(!comment_id || !action){
            res.json({errmsg:"参数错误1"});
            return
        }
        // 3、查询数据库，看评论是否存在
        let commentResult = await handleDB(res, "info_comment", "find", "数据库查询出错", `id=${comment_id}`); 
        if(!commentResult[0]){
            res.json({errmsg:"参数错误2"});
            return
        }

        // 4、添加点赞、 评论的like_count字段+1
        let like_count;
        if(action==="add"){
            await handleDB(res, "info_comment_like", "insert", "数据库添加出错", {
                user_id:userResult[0].id,
                comment_id:commentResult[0].id
            })
            like_count = commentResult[0].like_count?commentResult[0].like_count+1:1
        }else{
            await handleDB(res, "info_comment_like", "delete", "数据库删除出错", `user_id=${userResult[0].id} and comment_id=${commentResult[0].id}`)
            like_count = commentResult[0].like_count?commentResult[0].like_count-1:0
        }
        
        await handleDB(res, "info_comment", "update", "数据库更新出错", `id=${comment_id}`, {like_count});

        // 5、返回成功
        res.send({errno: "0"})

    })();
})
```

## 二、刷新页面展示点赞数量处理

/news_detail/:news_id  接口中最后：

```js
		// 把用户点赞过的评论数组传到前端页面
        let user_like_comments_ids = [];
        if(userInfo[0]){  //如果用户已经登录，查询用户点赞过的评论
            let user_like_comments = await handleDB(res, "info_comment_like", "find", "数据库查询出错", `user_id=${userInfo[0].id}`)
            
            user_like_comments.forEach(element=>{
                user_like_comments_ids.push(element.comment_id)
            })
        }

        let data = {
            ...
            user_like_comments_ids
        }
```

## 三、点赞的前端代码处理

detail.html页面中：

```html
<a href="javascript:;" class="{{ user_like_comments_ids.indexOf($value.id)>=0?'has_comment_up':''}} fr comment_up" data-commentid="{{ $value.id }}" data-newsid="{{ $value.news_id }}" data-likecount={{ $value.like_count?$value.like_count:0}}>
    {{ !$value.like_count?"赞": $value.like_count}}

</a>
```

前端detail.js代码中的.(源代码是news方法，改成html方法，否则不起作用)

```js
// 更新点赞数据,重新赋值回去
$this.attr('data-likecount', like_count)
if (like_count == 0) {
	$this.html("赞")
}else {
	$this.html(like_count)
}
```

## 四、详情页右侧作者信息展示

先执行  update info_news set user_id=2;  给所有新闻设置一个作者

在"/news_detail/:news_id"接口中：

```js
 // 查询作者信息：
        // 查询作者信息：
        let authorInfo = await handleDB(res, "info_user", "find", "数据库查询出错", `id=${newsResult[0].user_id}`)
        // 作者发布新闻数
        let authorNewsCount = await handleDB(res, "info_news", "sql", "数据库查询出错", 
            `select count(*) from info_news where user_id=${authorInfo[0].id}`
        )
        // 作者粉丝数
        let authorFansCount = await handleDB(res, "info_user_fans", "sql", "数据库查询出错", 
            `select count(*) from info_user_fans where followed_id=${authorInfo[0].id}`
        )

        // 传到模板中去的数据
        let data = {
            ...
            authorInfo:authorInfo[0],
            authorNewsCount:authorNewsCount[0]["count(*)"],
            authorFansCount:authorFansCount[0]["count(*)"]
        }
```

在views/detail.html中：

```html
...
<div class="author_card">
    <a href="#" class="author_pic"><img src="{{ authorInfo.avatar?authorInfo.avatar:'/news/images/user_pic.png'}}" alt="author_pic"></a>
    <a href="#" class="author_name">{{authorInfo.nick_name}}</a>
    <div class="author_resume">{{authorInfo.signature}}</div>
    <div class="writings"><span>总篇数</span><b>{{authorNewsCount}}</b></div>
    <div class="follows"><span>粉丝</span><b>{{authorFansCount}}</b></div>
    
    ...
</div>  
```



## 五、关注按钮的展示

在"/news_detail/:news_id"接口中：

```js
	// 登录的用户是不是已经关注这个作者
    let isFollow = false;
    if(userInfo[0]){  // 如果已经登录
        // 查询数据库
        let followResult = await handleDB(res, "info_user_fans", "find", "查询数据库出错", 
                                          `follower_id=${userInfo[0].id} and followed_id=${authorInfo[0].id}`
                                         )  // [{}]    []
        if(followResult[0]){   //关注了这个作者
            isFollow = true;
        }
    }

	let data = {
        ...
        isFollow
    }
```

在views/detail.html中：

```html
<a href="javascript:;" class="focus fr"  style="display: {{isFollow? 'none': 'block'}}">关注</a>
<a href="javascript:;" class="focused fr" style="display: {{isFollow? 'block': 'none'}}"><span class="out">已关注</span><span class="over">取消关注</span></a>
```



## 六、关注的后端逻辑

需要传的参数： author_id  action的值是follow就执行关注或者   unfollow 就取消关注的标志

业务逻辑：
1、获取登录信息
2、获取参数, 判空
3、查询数据库，判断作者是否存在
4、判断action的值是  follow 还是  unfollow
5、返回操作成功

```js
router.post("/news_detail/followed_user", (req,res)=>{
    (async function(){
    //    1、获取登录用户信息，没有获取到，就return
        let userInfo =  await common.getUser(req,res);  // [{...}]     [] 

        if(!userInfo[0]){ //如果没有登录， 就return
            res.send({errno:"4101", errmsg:"用户未登录"})
            return
        }
    //    2、获取参数，判空
        let {user_id, action} = req.body;
        if(!user_id || !action){
            res.send({errmsg:"参数错误1"})
            return
        }
    //    3、查询数据库，判断作者是否存在
        let followedResult = await handleDB(res, "info_user", "find", "数据库查询出错", `id=${user_id}`);
        if(!followedResult[0]){
            res.send({errmsg:"参数错误2"})
            return
        }
    //    4、根据action的值 关注 或者  取消关注的功能
        if(action ==="follow"){ 
            //执行关注操作
            await handleDB(res, "info_user_fans", "insert", "数据库添加失败", {
                follower_id:userInfo[0].id,
                followed_id:followedResult[0].id
            })
        }else{
            //执行取消关注操作, 取消关注相当于删除这条记录
            await handleDB(res, "info_user_fans", "delete", "数据库添加失败",
                `follower_id=${userInfo[0].id} and followed_id=${followedResult[0].id}`
            )
        }
    //    5、返回操作成功
        res.send({errno:"0", errmsg:"操作成功"});

    })();
})
```

在views/detail.html中：  两个按钮补上data-userid="{{authorInfo.id}}"

```html
<a href="javascript:;" class="focus fr"  style="display: {{isFollow? 'none': 'block'}}" data-userid="{{authorInfo.id}}">关注</a>
    <a href="javascript:;" class="focused fr" data-userid="{{authorInfo.id}}" style="display: {{isFollow? 'block': 'none'}}"><span class="out">已关注</span><span class="over">取消关注</span></a>
```

前端detail.js代码中的.(源代码是news方法，改成html方法，否则不起作用)

```js
// 取消关注成功
var count = parseInt($(".follows b").html());
count--;
$(".follows b").html(count + "")
```

## 七、个人中心页面展示

routes下新建profile.js文件

```js
const express = require("express");

const handleDB = require("../db/handleDb");
const router = express.Router();


router.get("/profile",(req, res)=>{
    
    res.render("news/user")
})



module.exports = router
```

config.js中引入profileRouter 并注册到app下:

```js
const profileRouter = require("./routes/profile.js");

...

this.app.use(common.csrfProtect, profileRouter);
```

访问http://localhost:8080/profile可以看见个人中心页面。

## 八、设置登录才可以访问个人中心页面

但是由于该页面需要登录才可以访问，所以接口中添加验证登录的代码：

未登录自动重定向到首页。

```js
const common = require("../utils/common");

router.get("/profile",(req, res)=>{
    (async function(){
        let userResult = await common.getUser(req, res);   
        if(!userResult[0]){
            res.redirect("/");   
        }

        res.render("news/user")    
    })(); 
})
```

base.html中，设置点击跳转到个人中心：

```html
{{if !user_info}}
<div class="user_btns fr">
    <a href="javascript:;" class="login_btn">登录</a> / <a href="javascript:;" class="register_btn">注册</a>
</div>
{{else}}
<div class="user_login fr">
    <img src="/news/images/person01.png" class="lgin_pic">
    <a href="/profile">{{ user_info.nick_name }}</a>   <!-- !!!点击跳转个人中心 -->
    <a href="#" onclick="logout()">退出</a>
</div>
{{/if}}
```

## 九、个人中心页面模板的抽取

views/user.html中:

```html
{{ extend './base.html'}}
{{block 'titleBlock'}}用户中心{{/block}}

{{ block 'contentBlock'}}
<div class="conter_con">
	...
</div>

{{/block}}

```

解决右上角显示的登录状态，"/profile"接口中补充：

```js
		let data = {
            user_info:userInfo[0]?{
                nick_name: userInfo[0].nick_name,
                avatar_url: userInfo[0].avatar_url
            }:false
        }
        res.render("news/user", data)   
```

## 十、子页面的展示

views/user.html中，修改回正确的路径：

```html
<ul class="option_list">
	<li class="active"><a href="/user/base_info" target="main_frame">基本资料</a></li>
	...
</ul>
<iframe src="/user/base_info" frameborder="0" name="main_frame" class="main_frame" id="main_frame"></iframe>
```

profile.js中， 把get 和post请求都写在这个接口中：

```js
router.all("/user/base_info",(req, res)=>{
    (async function(){
        // 获取用户登录信息，如果获取不到，就重定向到首页
        let userInfo = await common.getUser(req, res);   
        if(!userInfo[0]){
            res.redirect("/");  
        }
        
        if(req.method === "GET"){
            
            res.render("news/user_base_info")
        }else if(req.method === "POST"){

        }
   
    })(); 
})
```

## 十一、基本资料的数据的展示

"/user/base_info" 接口中：

```js
if(req.method === "GET"){
    // 查询数据库，把登录用户的数据添加到

    let data = {
        signature:userInfo[0].signature,
        nick_name:userInfo[0].nick_name,
        gender:userInfo[0].gender?userInfo[0].gender:"MAN",
    }
    res.render("news/user_base_info",data)
}
```

user_base_info.html中：

```html
...
<input id="signature" type="text" name="signature" class="input_txt" value="{{signature}}">
...
<input id="nick_name" type="text" name="" class="input_txt"  value="{{nick_name}}">
...

<input class="gender" type="radio" name="gender" {{if gender=="MAN"}}checked{{/if}}><b>男</b>

<input class="gender" type="radio" name="gender" {{if gender=="WOMAN"}}checked{{/if}}>  <b>女</b>
```

## 十二、基本资料的修改(POST提交)

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

