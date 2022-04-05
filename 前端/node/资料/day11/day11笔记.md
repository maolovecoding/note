## 一、在后台服务器程序获取数据库数据

项目目录下新建db文件夹，把今天材料中提供的db.js放入到这个文件夹中；

先安装mysql

db.js的代码如下（了解）：

```js
var mysql = require("mysql")
var pool = mysql.createPool({
    host:"localhost",
    user:"root",
    password:"",
    database:"qianduan_test"
})//数据库连接配置

function query(sql,callback){
    pool.getConnection(function(err,connection){
        connection.query(sql, function (err,rows) {
            callback(err,rows)
            connection.release()
        })
    })
}//对数据库进行增删改查操作的基础


exports.query = query
```

在项目中需要引入这个db.js之后，才能对数据库进行查询(掌握)：

```js
const express = require('express');
const app = express();

// 1、引入数据库
const db = require('./db/db.js');

app.get('/get_data', (req, res) => {
    // console.log(db.query);
    
    // 2、进行数据库查询
    db.query("select * from areas limit 10",(err, data)=>{
        console.log("查完了");
        
        if(err) {
            console.log(err);
        }
        res.send(data);
    });
});

app.listen(3000, () => {
    console.log('Example app listening on port 3000!')
});
```

## 二、ORM 介绍

ORM 全拼Object-Relation Mapping.

中文意为 对象-关系映射.

主要实现模型对象到关系数据库数据的映射.

比如：把数据库表中每条记录映射为一个模型对象

优点 :

​	1、只需要面向对象编程, 不需要面向数据库编写代码.

​	2、对数据库的操作都转化成对类属性和方法的操作.

​	3、不用编写各种数据库的sql语句.

​	4、实现了数据模型与数据库的解耦, 屏蔽了不同数据库操作上的差异.

​	5、不在关注用的是mysql、oracle...等.

​	6、通过简单的配置就可以轻松更换数据库, 而不需要修改代码.

缺点 :

​	1、相比较直接使用SQL语句操作数据库,有性能损失.

​	2、根据对象的操作转换成SQL语句,根据查询的结果转化成对象, 在映射过程中有性能损失.

​	3、有局限性，ORM中没有提供的查询功能需要写会sql语句	

## 三、ORM的基本使用（增删改查）

把nodejs-orm文件夹复制到db文件夹下，并修改其中index.js中的数据库连接设置

```js
// 数据库连接设置
let orm_config = {
    host: 'localhost',//数据库地址
    port:'3306',
    user: 'root',//用户名，没有可不填
    password: '',//密码，没有可不填
    database: 'qianduan_test'//数据库名称
}
```

#### 3.1、查询数据

查询所有学生的所有信息

```js
const db = require("./db/nodejs-orm/index.js");

app.get("/",(req, res)=>{
    let Student = db.model("students");
    Student.find((err,results)=>{
        res.send(results);
    });
})

```

查询指定字段(find、数组参数)

```js
let Student = db.model("students");
Student.find(["name", "age"],(err,results)=>{
    res.send(results);
});
```

按条件查询(find、字符串参数)

```js
let Student = db.model("students");   
Student.find("name='小月月'",(err,results)=>{  //这里的引号里面书写的是where后面的字符串
    res.send(results);
});
```

分页查询(limit、对象参数 where\number\count)

```js
let Student = db.model("students");  
//where查询条件，为可选项，number第几页； count表示每页条数
Student.limit({where:"age>18",number:1,count:5},(err,results)=>{
    res.send(results);
});
```



#### 3.2、增加数据

增加单条记录， insert、对象参数、属性就是列名

```js
let Student = db.model("students");
Student.insert({name:'赵云',age:20},(err,results)=>{

    res.send("增加成功");
});
```

同时增加多个数据， insert、数组参数、元素为对象、属性就是列名

```js
let Student = db.model("students");
let arr = [{name:'刘备'}, {name:"关羽"},{name:"张飞"}];

Student.insert(arr,(err,results)=>{
	res.send("增加成功");
});
```

#### 3.3、删除数据(物理删除)

按条件删除记录 delete、字符串参数

```js
let Student = db.model("students");
Student.delete('age=0',(err,results)=>{
     res.send("删除成功");
});
```

清空表里面所有内容   delete、无参数

```js
let Test1 = db.model("test1");

Test1.delete((err,results)=>{
        res.send("删除成功");
});
```

#### 3.4、修改数据

修改所有学生name为小明   update、对象参数、属性就是列名

```js
let Student = db.model("students");
Student.update({name:'小明'},(err,results)=>{
    console.log(results);
});
```

修改id为1的年龄为30     update、字符串参数1、对象参数2、属性就是列名

```js
let Student = db.model("students");
Student.update('id=1',{age:30},(err,results)=>{
    res.send("修改成功");
});
```

#### 3.5、自定义执行sql语句

上面的情况如果满足不了需求，直接使用自定义执行sql语句的方式    sql、字符串参数

```js
let Student = db.model("students");
Student.sql('select * from students',(err,results)=>{
    res.send("执行成功");
});
```

