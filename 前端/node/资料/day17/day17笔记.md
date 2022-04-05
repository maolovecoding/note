## 一、用户是否收藏这篇新闻的展示

在/news_detail/:new_id 接口中：

```js
		// 用户是否收藏了新闻？
        let isCollected = false;
        // if(用户已经登录){
        //     if(用户收藏了这篇新闻)isCollected=true;
        // }
        if(userInfo[0]){
            let collectionResult = await handleDB(res, "info_user_collection", "find", "数据库查询出错", `news_id=${news_id} and user_id=${userInfo[0].id}`);
            console.log(collectionResult);
            if(collectionResult[0])isCollected=true;
        }

	...
    	let data = {
            ...
            isCollected:isCollected    //把这个值传到前端，作为展示按钮的依据
        }
```

在detail.html页面中：

```html
    <a href="javascript:;" class="collection block-center" data-newid={{newsData.id}} style="display:{{isCollected?'none':'block'}}">收藏</a>
    <a href="javascript:;" class="collected block-center" data-newid={{newsData.id}} style="display:{{isCollected?'block':'none'}}"><span class="out">已收藏</span><span class="over">取消收藏</span></a>
```

## 二、收藏和取消收藏逻辑的实现

需求：前端点击按钮后端执行收藏或者取消收藏的处理
需要传参数？
        1、对哪一篇新闻进行收藏要告诉服务器， 传新闻id
        2、还要告诉服务器执行的是收藏还是取消收藏

注意：
    	用户 与 收藏新闻  多对多关系(一个用户可以收藏多条新闻，一条新闻可以被多用户收藏)
    	数据表info_user_collection 记录了用户 与 收藏新闻 的关系

步骤分析：

​	1、获取登录用户信息（用户没有登录，不允许收藏，直接return）

​	2、接收参数,判空

​	3、查询新闻，并判断是否存在，不存在就return

​	4、收藏或者取消收藏

​	5、返回成功的响应

```js
router.post("/news_detail/news_collect", (req,res)=>{
    /*
        1、获取登录用户信息（用户没有登录，不允许收藏，直接return）
        2、接收参数,判空
        3、查询新闻，并判断是否存在
        4、收藏或者取消收藏
        5、返回成功的响应
    */
    (async function news_collect(){
        // 1、获取登录用户信息（用户没有登录，不允许收藏，直接return）
        let userResult = await funcs.getUser(req, res);
        if(!userResult[0]){
            res.json({errno:"4041",errmsg:"未登录"});
            return
        };
        // 2、接收参数,判空
        let {news_id, action} = req.body;
        console.log(news_id, action)
        if(!news_id || !action){
            res.json({errmsg:"参数错误1"});
            return
        }
        // 3、查询新闻，并判断是否存在
        let newsResult = await handleDB(res, "info_news", "find", "info_news数据库查询出错", `id=${news_id}`); 
        if(!newsResult[0]){
            res.json({errmsg:"参数错误2"});
            return
        }
        // 4、收藏或者取消收藏
        if(action=="collect"){
            //进行收藏
            await handleDB(res, "info_user_collection", "insert", "数据库添加数据出错", {news_id,user_id:userResult[0].id }); 
        }else{
            //进行取消收藏
            await handleDB(res, "info_user_collection", "delete", "数据库删除数据出错", `news_id=${news_id} and user_id=${userResult[0].id}`); 
        }
        // 5、返回成功的响应
        res.json({errno:"0",errmsg:"操作成功"});
    })();
})
```

## 三、评论功能分析

评论的数据都保存在info_comment表中。

评论分为两种：

1、一种是评论新闻   这类评论的parent_id字段为空

2、另一种是回复别人的评论  这类评论的parent_id字段不为空，就是回复的那条评论的id

## 四、评论新闻后端逻辑实现

明确：
	评论新闻和回复别人的评论是一样的，处理的请求写在一个接口函数内

参数分析：
	评论新闻需要传的参数：新闻id和评论内容   即 news_id、comment

​	回复别人的评论需要传的参数： 新闻id和评论内容、还有回复的那条评论的id  即 news_id、comment、parent_id

步骤分析：
    1、获取登录用户信息
    2、接收参数，判空
    3、查询新闻，并判断是否存在
    4、往数据库插入数据   (如果有parent_id这个参数，也要添加这个属性)
    5、返回成功的响应（传数据给前端的回调函数中）

```js
router.post("/news_detail/news_comment", (req,res)=>{
    /*
        评论新闻需要传的参数：新闻id和评论内容   即 news_id、comment
	    回复别人的评论需要传的参数： 新闻id和评论内容、还有回复的那条评论的id  即 news_id、comment、parent_id

        1、获取登录用户信息
        2、接收参数，判空
        3、查询新闻，并判断是否存在
        4、往数据库插入数据   (如果有parent_id这个参数，也要添加这个属性)
        5、返回成功的响应（并把这个评论模型对象返回给前端，前端需要展示评论相关数据）

    */
    (async function news_collect(){ 
        // 1、获取登录用户信息
        let userResult = await funcs.getUser(req, res);
        if(userResult.length==0){
            res.json({errno:"4041",errmsg:"未登录"});
            return
        };
        // 2、接收参数，判空
        let {news_id,comment,parent_id=null} = req.body;
        console.log(news_id, comment)
        if(!news_id || !comment){
            res.json({errmsg:"参数错误1"});
            return
        }
        // 3、查询新闻，并判断是否存在
        let newsResult = await handleDB(res, "info_news", "find", "info_news数据库查询出错", `id=${news_id}`); 
        if(newsResult.length==0){
            res.json({errmsg:"参数错误2"});
            return
        }
        // 4、往数据库插入数据   (如果有parent_id这个参数，也要添加这个属性)
        let commentObj = {
            user_id:userResult[0].id,
            news_id,
            content:comment,
        }
        if(parent_id)commentObj["parent_id"]=parent_id;
       	let insertResult = await handleDB(res, "info_comment", "insert", "数据库插入出错", commentObj)
        
        // 5、返回成功的响应（并把这个评论模型对象返回给前端，前端需要展示评论相关数据）
        let newsResult = await handleDB(res, "info_news", "find", "info_news数据库查询出错", `id=${news_id}`)
        res.json({errno:"0",errmsg:"操作成功"} )
    })();
})
```

完成以上功能已经能够正常插入数据库，但是前端还需要做数据渲染，所以需要把插入的这条数据给前端传过去

```js
let commentObj = {
    ...
    create_time:new Date().toLocaleString()
}
...
// 5、返回成功的响应（并把这个评论模型对象返回给前端，前端需要展示评论相关数据）
let data = {
    user:{
        nick_name: userResult[0].nick_name,
        avatar_url: userResult[0].avatar_url?userResult[0].avatar_url:"/news/images/worm.jpg"
    },
    content:comment,
    news_id,
    id:insertResult.insertId,
    create_time:commentObj.create_time
}
res.send({errno:"0", errmsg:"评论成功", data})
```

但是目前刷新页面，还不能展示这个评论。

## 五、刷新之后评论功能的展示

在渲染这个详情页面的时候，要查询跟这篇新闻相关的评论，按时间排序查询出来

在/news_detail/:new_id 接口中：

```js
//-------查询评论--------
let commentResult = await handleDB(res, "info_comment", "find", "数据库查询出错", `news_id=${new_id} order by create_time`);

let data = {
    user_info:userResult[0]?{
        nick_name:userResult[0].nick_name,
        avatar_url:userResult[0].avatar_url?userResult[0].avatar_url:"/news/images/person01.png"
    }:"",
    newsClick:result3,
    newsData:result4[0],
    isCollected:isCollected,
    commentList:commentResult
}

res.render("news/detail", data);
```

前端detail.html中：(复制粘贴到detail.html中即可)

```html
		{{ each commentList }}
            <div class="comment_list">
                <div class="person_pic fl">
                    <img src="{{ user_info.avatar_url }}" alt="用户图标">
                </div>
                <div class="user_name fl">{{ user_info.nick_name }}</div>
                <div class="comment_text fl">{{ $value.content }}</div>
                {{ if $value.parent_id }}
                    <div class="reply_text_con fl">
                        <div class="user_name2">{{ $value.parent.user.nick_name }}</div>
                        <div class="reply_text">
                            {{ $value.parent.content }}
                        </div>
                    </div>
                {{ /if }}
                <div class="comment_time fl">{{ $value.create_time | dateFormat }}</div>
                <a href="javascript:;" class="comment_up fr" data-commentid="{{ $value.id }}" data-newsid="{{ $value.news_id }}">赞</a>
                <a href="javascript:;" class="comment_reply fr">回复</a>
                <form class="reply_form fl" data-commentid="{{ $value.id }}" data-newsid="{{ $value.news_id }}">
                    <textarea class="reply_input"></textarea>
                    <input type="button" value="回复" class="reply_sub fr">
                    <input type="reset" name="" value="取消" class="reply_cancel fr">
                </form>
            </div>
        {{ /each }}
```

**添加新闻评论条数的增加**

/news_detail/news_comment  接口中：

```js

        let count = newsResult[0].comments_count?newsResult[0].comments_count+1:1
        await handleDB(res, "info_news", "update", "数据库修改出错", `id=${news_id}`, {comments_count:count});
        // 5、返回成功的响应（传数据给前端的回调函数中）
```

## 六、评论功能的展示(最终完成)

到目前为止，退出功能实际上有bug，退出之后，detail.html里面的头像和名称没办法正常展示

因为detail.html页面中的评论代码写的是：

```html
<div class="person_pic fl">
    <img src="{{ user_info.avatar_url }}" alt="用户图标">
</div>
<div class="user_name fl">{{ user_info.nick_name }}</div>
```

这里其实应该是 ：每一条评论的user_id所对应的用户头像url和用户昵称，即：

```html
<div class="person_pic fl">
    <img src="{{ $value.commenter.avatar_url }}" alt="用户图标">
</div>
<div class="user_name fl">{{ $value.commenter.nick_name }}</div>
```

所以，在/news_detail/:news_id 接口中，应该在我们返回的数组里面添加这个评论者的头像链接和昵称补充：

```js
		// 查询新闻对应的评论
        let commentsResult = await handleDB(res, "info_comment", "find", "数据库查询出错", `news_id=${news_id}`);

		// 给每一条评论添加评论者信息
        for(var i=0; i<commentsResult.length; i++){
            let commenterInfo = await handleDB(res, "info_user", "find", "数据库查询出错", `id=${commentsResult[i].user_id}`)

            commentsResult[i].commenter={
                nick_name:commenterInfo[0].nick_name,
                avatar_url:commenterInfo[0].avatar_url?commenterInfo[0].avatar_url:"/news/images/worm.jpg"
            }
        };
```

## 七、回复功能的后端逻辑

/news_detail/news_comment接口第4步中，

如果有接收到parent_id，则查询这条评论parent_id对应评论中对应的user_id对应的用户头昵称 

```js
if(parent_id){
    commentObj["parent_id"]=parent_id;   //parent_id是父评论id
    
    // 查询父评论和父评论者信息
    var parentComment = await handleDB(res, "info_comment", "find", "数据库查询出错", `id=${parent_id}`);
    var parentUserInfo = await handleDB(res, "info_user", "find", "数据库查询出错", `id=${parentComment[0].user_id}`);

};
...
let data= {
    ...
    parent:parent_id?{
        user:{
            nick_name:parentUserInfo[0].nick_name
        },
        content:parentComment[0].content
    }:null
}
```

## 八、详情页中包含回复的展示

在/news_detail/:new_id 接口中,

如果有父评论id，也需要返回父评论id所对应的这个用户昵称和父评论内容，给detail.html做展示。我们可以直接把这两个信息添加在评论结果的列表中。

```js
//-------查询评论--------
var commentResult = await handleDB(res, "info_comment", "find", "数据库查询出错", `news_id=${new_id} order by create_time desc`);

// 遍历每一个评论对象的时候，要看看是否有parent_id， 有的话也要把父评论者的昵称和福评论内容传到页面去
for(let i = 0; i<commentResult.length; i++){
    
    ... ...
    
    if(commentsResult[i].parent_id){
        var parentComment = await handleDB(res, "info_comment", "find", "数据库查询出错", `id=${commentsResult[i].parent_id}`);
        var parentUserInfo = await handleDB(res, "info_user", "find", "数据库查询出错", `id=${parentComment[0].user_id}`);

        commentsResult[i].parent = {
            user:{
                nick_name:parentUserInfo[0].nick_name
            },
            content:parentComment[0].content
        }

    }
}
```
