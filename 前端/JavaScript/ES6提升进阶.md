# ES6学习

## ES6必备知识

### const/let



### 解构赋值

- 按照一定模式，从数组和对象中提取值，对变量进行赋值
- 数组结构
- 对象结构
- 字符串结构
- 应用



### 数组的遍历

#### ES5

- for循环
- forEach 增强for，forEach循环是无法跳出循环的，不可思议break和continue
- map 函数的返回值组成新数组
- filter 进行数组元素的过滤
- some 数组是否包含符合条件的元素
- every 数组元素是否都符合某个条件
- reduce 接收一个函数作为累加器reduce(callback,init)
- for in 遍历，**不推荐使用有一些小问题**

```js
let arr = [1,2,3]
    Array.prototype.foo = ()=>{
      console.log("foo");
    }
    for(const a in arr){
      console.log(a);
    }
// 会发现遍历的最后，会输出 foo 也就是 foo函数也会被调用一次
```



#### ES6方法

- find 找到指定元素
- findIndex 找到指定的元素的下标
- for of 更适合循环数组，遍历的需要是可迭代对象
- keys 获取数组元素下标的迭代器
- values() 获取数组元素的迭代器
- entries() 获取一个有数组下标和元素的迭代器[index,value]。

keys，values，enteies这三种遍历方式都是一般搭配for of 使用的。

```js
// find
    let arr2 = [2,3,4]
    // const res = arr2.find((value)=>{
    //   return value === 3;
    // })
    // console.log(res);

    // for(const a of arr2){
    //   console.log(a);
    // }
    for(const b of arr2.keys()){
      console.log(b);
    }
    for(const b of arr2.values()){
      console.log(b);
    }
    for(const [index,value] of arr2.entries()){
      console.log(index,value);
    }
```



### 数组的扩展

- 类数组/伪数组
- Array.from() 将伪数组换为数组
- Array.of()
- copyWithin() 
- fill() 填充数组元素
- includes()，可以检测数组是否包含NaN元素。

```js
 // ES5转伪数组
    // 所谓的伪数组，其实需要一个length属性
    let arr = Array.prototype.slice.call({ length: 0 })
    arr.push(1);
    console.log(arr instanceof Array);
    console.log(arr);

    function foo(a) {
      console.log(arguments);
      console.log(arguments instanceof Array);
    }
    foo()
    // ES6 转伪数组
    let a = Array.from({
      // 有几个元素 length的长度应该就是几 
      // 不然转数组以后会出现undefined的情况
      length: 1
    });
    console.log(a);
    console.log(a instanceof Array);

    // Array.of() 将
    console.log(new Array(1, 2, 3));
    console.log(new Array(3));  //数组的长度是3
    console.log(Array.of(3)); // [3]

    // copyWithin(从哪个位置开始替换元素，从哪个位置开始读取元素，从哪个位置停止读取元素)
    // 从索引2开始替换元素，替换为 从索引4开始到数组结束的元素
    console.log([1, 2, 3, 4, 5, 6, 7].copyWithin(2, 4));

    // fill
    const arr3 = new Array(10)
    // 填充元素从0索引开始5索引结束
    arr3.fill(10, 0, 5);
    console.log(arr3);

    const arr4 = [1,2,NaN,true]
    console.log(arr4.includes(NaN));// true
```

