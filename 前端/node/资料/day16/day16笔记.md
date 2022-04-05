关于时间格式的操作，只需要一个格式转化函数即可，把它当成我们的工具函数放在common.js中:

```js
function dateFormat(value){
    var d = new Date(value);  //2018-01-16T21:19:19.000Z
    var times=d.getFullYear() + '-' + (d.getMonth() + 1) + '-' + d.getDate() + ' ' + d.getHours() + ':' + d.getMinutes() + ':' + d.getSeconds(); 
    return times
}

module.exports = {
   ...
    dateFormat
}
```



## 一、详情页的文章的详情的填充

在/views/detail.html中

```html
		<h3>{{newsData.title}}</h3>
        <div class="detail_about clearfix">
            <span class="time_souce fl">{{newsData.create_time | dateFormat}} 来源: {{newsData.source}}</span>
            <span class="comment fr">{{newsData.clicks?newsData.clicks:0}}</span>
        </div>

        <p class="summary">
            摘要：{{newsData.digest }}
        </p>
        {{#newsData.content }}   <!--这个井号是为了把字符串格式化成html-->


...
	{{ if !user_info}}
    <div class="comment_form_logout login_btn">
        登录发表你的评论
    </div>
    {{ else }}
    <form action="" class="comment_form">
        <div class="person_pic">
                <img src={{user_info.avatar_url?user_info.avatar_url:"/news/images/person01.png"}} />
        </div>
        <textarea placeholder="请发表您的评论" class="comment_input"></textarea>
        <input type="submit" name="" value="评 论" class="comment_sub">
    </form>
    {{ /if }}

    <div class="comment_count">
        {{newsData.comments_count?newsData.comments_count:0}}条评论
    </div>
```

日期的处理，在filters.js中添加过滤器：直接复制一下函数作为过滤器即可

```js
template.defaults.imports.dateFormat= function(value){
    var d = new Date(value);  //2018-01-16T21:19:19.000Z
    var times=d.getFullYear() + '-' + (d.getMonth() + 1) + '-' + d.getDate() + ' ' + d.getHours() + ':' + d.getMinutes() + ':' + d.getSeconds(); 
    
    return times
}
```

在/routes/detail.js中引入：

```js
require("../utils/filters");
```



## 二、新闻详情点击量加1

每次访问/news_detail/:new_id 接口的时候，如果查询到新闻数据，需要对这篇新闻点击量字段加1

```js
//设置访问新闻详情的时候，点击量+1
let news_click = result4[0].clicks+1
await handleDB(res, "info_news", "update", "info_news数据库更新出错", `id=${new_id}`,{clicks:news_click}); 
```

## 三、获取用户登录信息的功能的抽取

我们把这个获取用户登录信息的功能抽取到common.js中

/utils/common.js中

```js
const handleDB = require("../db/handleDb.js");

...
async function getUser(req, res){
    let user_id = req.session["user_id"];
    let result1=[];
    if(user_id){
        result1 = await handleDB(res, "info_user", "find", "user_info数据库查询出错", `id=${user_id}`); 
    }
    return result1
}
module.exports = {
    csrfProtect,
    getUser
}
```

原先书写的获取用户登录信息的地方（两处：/routes/index.js和/routes/detail.js）：

```js
const common = require("../utils/common");
...
let userResult = await common.getUser(req, res);
```

## 四、用户是否收藏这篇新闻的展示

在/news_detail/:new_id 接口中：

```js
		// 用户是否收藏了新闻？
        let isCollected = false;
        // if(用户已经登录){
        //     if(用户收藏了这篇新闻)isCollected=true;
        // }
        if(userResult.length>0){
            let collectionResult = await handleDB(res, "info_user_collection", "find", "数据库查询出错", `news_id=${new_id} and user_id=${userResult[0].id}`);
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

