# JavaScript数据类型检测

对于确定一个js变量的类型，我们大概都知道几种方式。对于弱语言来说，检测其正确的类型是一件不是特别容易的事情。那么我们有几种方式来检查一个js变量的类型？

**从大的方向来说，js的数据类型分为基本数据类型和引用数据类型。**对于js的基本类型和引用类型是那些，这里就不在赘述。此外，在ES6新增的 *symbol*基本类型，ES7新增*bigint*大数类型，也是基本类型。



一般获取一个js类型的方式，大概应该是四种(我目前只知道这四种)

1. typeof  **
2. instanceof
3. toString -> Object.prototype.toString.call(变量) **
4. constructor 

注： ** 表示最常见的检测类型的方案



## typeof底层原理

我们检测一个变量的类型，最常用的应该就是typeof运算符了。

```js
typeof "你好"
//'string'
typeof 12
//'number'
typeof 12.3
//'number'
typeof 12n
//'bigint'
typeof Symbol()
//'symbol'
typeof null
//'object'
typeof undefined
//'undefined'
typeof []
//'object'
typeof {}
//'object'
typeof function(){}
//'function'
typeof new Object
//'object'
typeof new Boolean
//'object'
typeof new Set
//'object'
```





![image-20220214214229556](https://gitee.com/maolovecoding/picture/raw/master/images/web/js/image-20220214214229556.png)

结果很直观。对于基本数据类型，什么字符串啊，数字啊，都能直接判断出来其对应的类型，对于函数类型也可以直接判断，但是像内置对象(标准，非标准)等，其结果都是得到一个模糊的 **‘object’**。很明显是不利于我们判断其更加准确的类型。而且，对于一个null，也就是空，其结果居然也是 **‘object’**，其实是让人有些费解的。



首先不考虑那么多，要明白对象 typeof 变量，该求值得到的 结果一定是一个字符串，毋庸置疑。一个简单的面试题：

```js
typeof typeof typeof "123" // ???
```



typeof在检查基本类型时还是很好用的，但是到了对象不是很好用了。

那么typeof底层是如何判断出其具体的类型？

**其底层是按照二进制的方式来检查其类型的。[效率高]**

我们知道计算机的底层都是一串串的二进制代码。所以以二进制的形式进行判断，其效率肯定会高一些。不要反驳：哪怕这种方式只是效率高那么一点点，也是效率提升了。

以64位二进制存储变量去具体值在堆（原始类型可以在栈中）中的地址：以下是以某些数字开头的变量存储的变量的类型

- **二进制开头数字   类型**

- 000 对象
- 1 整数
- 010 浮点数
- 100 字符串
- 110 布尔
- 000000…. null
- -2^30 undefined
- ……

**可以看见对象的地址其二进制是以000开头的，而null变量的地址全是0，所以在判断到以000开头以后，就直接认为null也是个对象。**

因而导致我们使用typeof判断null的类型的时候，得到的类型并不是 ‘null’，而是 ‘object’。

**也要记住，函数也是对象，是一种特殊的对象**

![image-20220216204819473](https://gitee.com/maolovecoding/picture/raw/master/images/web/js/image-20220216204819473.png)



如何判断一个变量的类型是否是对象？

```js
function isObject(val){
    if(val === null) return false;
    return typeof val === 'object' || typeof val === 'function';
}
```



**typeof 还有个特点：就是如果后面跟着的变量是未定义的，那么其得到的结果是 ‘undefined’，而不是报错**

![image-20220216205257363](https://gitee.com/maolovecoding/picture/raw/master/images/web/js/image-20220216205257363.png)



**方式二 整理好再说吧。。。**

