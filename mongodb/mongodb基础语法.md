## 安装

傻瓜式安装即可。



## 启动mongodb服务

```shell
mongod --config "配置文件路径" # 可以不要双引号
mongod --config
```



## 启动客户端

```shell
mongo
```





## 基础语法

![image-20220214165722546](https://gitee.com/maolovecoding/picture/raw/master/images/web/mongodb/image-20220214165722546.png)



### 数据库操作

1. db就是指当前正在使用的那个数据库（use manager 则下面使用db就代表manager数据库了）

![image-20220214165911854](https://gitee.com/maolovecoding/picture/raw/master/images/web/mongodb/image-20220214165911854.png)



### 集合操作

1. **db.createCollection(“users”)** 创建了一个集合users（理解为mysql中的表table）

![image-20220214170101463](https://gitee.com/maolovecoding/picture/raw/master/images/web/mongodb/image-20220214170101463.png)



### 创建文档

在集合上操作。也就是mysql中的操作表

1. 创建文档 也就是添加数据的操作。

   - 插入一条数据

     ```js
     db.users.insertOne({userId:1, userName:"张三", age:22})
     ```

   - 插入多个记录，最外层使用数组包裹

     ```js
     db.users.insertMany([
         {userId:1, userName:"张三", age:22},
         {userId:1, userName:"张三", age:22}
     ])
     ```

     

2. 查看集合中的数据

   - 查看数据 全部数据

     ```js
     db.users.find()
     ```

   - 查看指定符合条件的数据

     ```js
     db.users.find({userId:1}) # id = 1
     db.users.find({userId:{$gt:0}}) # id > 0
     ```

3. 删除数据和查看类似 均可指定条件

4. 更新数据

   - 前面的参数是查询条件，最后一个参数表示批量更新。不设置最后一个参数的值 ，只会更新符合要求的数据的第一条

![image-20220214170130814](https://gitee.com/maolovecoding/picture/raw/master/images/web/mongodb/image-20220214170130814.png)





### 条件操作

![image-20220214170241807](https://gitee.com/maolovecoding/picture/raw/master/images/web/mongodb/image-20220214170241807.png)

