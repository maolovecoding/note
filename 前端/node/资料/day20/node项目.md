## 一、个人中心收藏列表

在utils/test.js接口中，添加测试数据的接口

```js
const handleDB = require("../db/handleDB");
router.get("/create_collections", (req, res)=>{
    (async function(){
        for(let i=200;i<256;i++){
            let a = await handleDB(res, "info_user_collection", "insert", "数据库插入出错", {
                user_id:9,
                news_id:i
            })
        }
    })();
})
```

profle.js接口中

```js
router.get("/user/collection",(req, res)=>{
    (async function(){
        let userInfo = await common.getUser2(req, res);
        // 接受参数，参数表示前端要获取第几页数据
        let {p=1} = req.query
        
        let current_page = p
        let total_page = 1
        // 获取总条数
        let counts = await handleDB(res, "info_user_collection", "sql", "出错1", 
            `select count(*) from info_user_collection where user_id=${userInfo[0].id}`
        )  // [{"counts(*)": 50}]

        // 每页10条， 要第current_page页   // 分页 limit (当前页数-1)*每页条数，每页条数
        let newsIdListResult = await handleDB(res, "info_user_collection", "sql", "出错2", 
            `select news_id from info_user_collection where user_id=${userInfo[0].id} limit ${(Number(current_page)-1)*10},10`
        ) 
		
        // 根据id查询到info_news里面对应的每一篇新闻
        let newsListResult = []
        for(var i=0; i<newsIdListResult.length; i++){
            let ret = await handleDB(res, "info_news", "sql", "出错3", 
                `select title, create_time from info_news where id=${newsIdListResult[i].news_id}`
            )
            newsListResult.push(ret[0])
        }
        console.log(newsListResult);
        // 总页数=总条数/每页条数
        total_page = Math.ceil(counts[0]["count(*)"]/10)
        let data = {
            current_page,
            total_page,
            newsListResult
        }
        res.render("news/user_collection", data)
    })(); 
})
```

views/use_collection.html中:

```html
 <script>
     $(function() {
         $("#pagination").pagination({
             currentPage: Number({{ current_page }}),
             totalPage: Number({{ total_page }}),
             callback: function(current) {
                 //这里的代码点击按钮的时候执行
                 // alert('ok!'+current);
                 location.href = "/user/collection?p=" + current
             }
         });
     });
</script>
```



## 二、项目总结

- 《经济新闻网》一款新闻展示的Web项目，主要为用户提供最新的金融资讯、数据
- 以抓取其他网站数据和用户发布作为新闻的主要来源
- 基于express 框架，以 前后端不分离 的形式实现具体业务逻辑
- 数据存储采用mysql，使用orm
- 封装自己的操作数据可的工具函数handleDB(兼容其他node框架)
- 用户图片数据使用对象存储（七牛云）
- 采用session实现保持用户登录状态机制
- 实现对CSRF请求伪造进行防护功能
- （提供jwt的获取接口）
- 采用art-template 模板引擎技术
- 界面局部刷新使用 ajax 请求接口
- 实现模块：注册、登录、首页新闻数据展示模块，滑动到底部加载更多、点击排行、基页模板的抽取与模板继承、详情页数据展示、用户收藏新闻、用户评论模块、回复评论模块、新闻作者数据展示、用户关注模块、个人中心模块(修改基本资料、密码、用户头像)等。

