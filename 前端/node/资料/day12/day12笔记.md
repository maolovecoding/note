## 一、完善获取数据库数据的写法

**async+await版本**

```js
app.get("/",(req, res)=>{

    (async function(){
        let Student = db.model("students");  //获取学生表模型
        let results = await new Promise((resolve,reject)=>{
            Student.find('id>3',(err,data)=>{
                if(err)reject(err);
                resolve(data);
            });    
        })

        res.send(results);
    })();
})
```

**带捕获异常的版本**

```js
(async function(){
    let Student = db.model("students");  //获取学生表模型
    let results 
    try{
        results = await new Promise((resolve,reject)=>{
            Student.find('id>3',(err,data)=>{
                if(err)reject(err);
                resolve(data);
            });    
        })
    }catch(err){
        console.log(err);
        res.send({errmsg:"数据库查询出错"})
        return
    }
    res.send(results);
})();
```

## 二、封装handleDB

在db文件夹中新建handleDB.js文件：

```js
const db = require("./nodejs-orm")

function handleDB(res,tableName,methodName,errorMsg, n1, n2){
    //数据库操作
    let Model = db.model(tableName);  // 表映射为模型, 参数是要操作的这个表的表名
    let results;  //results就收查询到的数据
    try{
        results = new Promise((resolve, reject)=>{
            // Model.find("id>=15",(err,data)=>{   //直接调用不封装
            if(!n1){
                Model[methodName]((err,data)=>{    //封装的时候使用这种格式 
                    if(err)reject(err);   // 失败的时候调用reject()
                    resolve(data);    //成功的时候调用resolve() 
                });
                return
            }
            //能够给执行到这里说明n1已经传进来了！
            if(!n2){
                Model[methodName](n1,(err,data)=>{    //封装的时候使用这种格式 
                    if(err)reject(err);   // 失败的时候调用reject()
                    resolve(data);    //成功的时候调用resolve() 
                });
                return
            }
            //能够给执行到这里说明n1和n2已经传进来了！
            Model[methodName](n1,n2,(err,data)=>{    //封装的时候使用这种格式 
                if(err)reject(err);   // 失败的时候调用reject()
                resolve(data);    //成功的时候调用resolve() 
            });
                
        }) 
    }catch(err){
        console.log(err); // 给后台看到的
        res.send({errMsg:errorMsg}); //给前端送过去的
        return
    }

    return results
}

module.exports = handleDB
```

在项目中：

```js
const db = require("./db/handleDB");


app.get("/", (req,res)=>{
	(async function(){
        //获取参数， 判空

        //数据库操作
        // let results = await Promise对象
        let results = await handleDB(res,"students", "find", "students数据库插入数据出错！");
        //后面我们在项目中操作数据库都可以使用上面这种做法

        //查询到的结果返回页面中去
        res.send(results);

    })();
    
})
```

## 三、CSRF跨站请求伪造 

CSRF全拼为Cross Site Request Forgery，译为跨站请求伪造。

CSRF指攻击者盗用了你的身份，以你的名义发送恶意请求。

包括：以你名义发送邮件，发消息，盗取你的账号，甚至于购买商品，虚拟货币转账......

造成的问题：个人隐私泄露以及财产安全。

CSRF请求伪造的示意图：![CSRF攻击过程](assets/CSRF%E6%94%BB%E5%87%BB%E8%BF%87%E7%A8%8B.png)

## 四、CSRF防护

**防护思路：**

**1、请求转账页面的时候，服务器响应转账页面，在cookie中设置一个csrf_token值(随机48位字符串)。**

**2、客户端在进行post请求的时候，在请求头中带上自定义的属性'X-CSRFToken' ，值为cookie中的csrf_token值。(要注意的是，此时的post请求，浏览器还会自发带着cookie中csrf_token到服务器。)**

**3、服务器在接收到post请求的时候，首先验证响应头中的x-csrftoken值，和cookies中的csrf_token是不是一致，如果不一致，需要return，直接结束处理，不进行后续工作。**



完整步骤：

先安装cookie-parser   

生成n为随机字符串：

```js
function getRandomString(n){
    var str="";
    while(str.length<n){
      str+=Math.random().toString(36).substr(2);
    }
    return str.substr(str.length-n)
}
getRandomString(48);  // 调用生成csrf_token
```

get请求转账页面的时候，在cookie中设置一个csrf_token值(随机48位字符串)：

**！！记得安装cookie-parser模块**

```js
if(req.method=="GET"){
    // 渲染转账页面的时候，同时在cookie中设置csrf_token
    //设置cookie和session
    let csrf_token = getRandomString(48);
    res.cookie('csrf_token', csrf_token); 

    res.render('temp_transfer');
}
```

接下来，在前端页面中，post请求时候带上自定义的属性'X-CSRFToken' ，值为cookie中的csrf_token值：

```js
$.ajax({
    url:'/transfer',
    type:'post',
    data:JSON.stringify(params),
    contentType:'application/json',
    headers:{'X-CSRFToken':getCookie('csrf_token')},
    success: function (resp) {
         ....
    }
})

....

function getCookie(name) {   //获取cookie的函数
    var r = document.cookie.match("\\b" + name + "=([^;]*)\\b");
    return r ? r[1] : undefined;
}
        
```

最后回到服务器端，处理post请求的时候，判断响应头中的x-csrftoken值，和cookies中的csrf_token是不是一致，不一致就是CSRF验证不通过，直接return：

```js
...
else if(req.method=="POST"){
    // 判断响应头中的x-csrftoken值，和cookies中的csrf_token进行对比
    console.log(req.headers["x-csrftoken"]);
    console.log(req.cookies["csrf_token"]);

    if((req.headers["x-csrftoken"] === req.cookies["csrf_token"])){
        console.log("csrf验证通过！");
    }else{
        res.send("csrf验证不通过！");
        return
    }

    // 以下可以开始正常处理post请求
    ...

}
```

## 五、对每一个POST请求都设置CSRF防护

备注：资料中web-scrf(Protected2)是在之前的web-scrf(Protected)的基础上将登录页面改成ajax登录的方式。

实际上，不仅仅转账需要CSRF防护，每一个post请求都需要做csrf的防护措施。

webA项目中的app.js：

```js
const router = express.Router();

router.all('/', (req, res) => {
    if(req.method=="GET"){
        res.render('temp_login')
    }
    
    ...
});
router.all('/transfer', (req, res) => {
    
   ...
   
    else if(req.method=="POST"){
        
        let {to_account, money} = req.body;
        console.log(to_account, money);
        
        //执行转账功能： ....此处省略
        console.log("假装执行转账操作，将当前登录用户的钱转账到指定账户");
        console.log(`已经完成转账${money}元到账户${to_account}`);
        
        res.json({to_account,money});

    }
});
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

