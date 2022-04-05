# 你不知道的this

学会使用JavaScript只需要三天，但是想学好JavaScript需要三年。

**在js中，最让人头疼的存在莫过于，闭包，作用域，this指向问题，以及异步等等。**

可以说：前端人员很多人才开始都没有真的弄懂this。



## this的那些绑定规则

常见的绑定规则有如下四种：

1. 默认绑定
2. 隐式绑定
3. 显示绑定
4. new 绑定

接下来让我们好好说道说道。



### 默认绑定

什么是默认绑定？默认绑定不就是什么处理都不做的时候的绑定规则吗？很显然就是我们直接调用函数的时候，这时候就是默认绑定。**不需要多说，默认绑定的this就是window**



#### 全局调用全局函数

在全局定义的函数，直接调用，很明显绑定的是window

```js
function foo(){
    console.log(this);
}

// window
foo()
```

![image-20211114151249395](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114151249395.png)



#### 函数内调用全局函数

在全局定义的函数，如果在其他函数内调用，那么this是绑定的是那个对象？肯定还是window了，毕竟函数还是直接调用嘛。

```js
function foo(){
    console.log(this);
}
function bar(){
    foo();
}
// window
bar()
```

![image-20211114151623957](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114151623957.png)







### 隐式绑定

隐式绑定就是通过对象的形式调用函数。其实也可以说是调用方法，因为这些函数都是定义在对象上的。当然方法只是函数在不同位置的叫法而已，这里不需要纠结。

比如我们定义对象obj，并在上面定义方法（函数）foo。那么通过对象obj调用函数foo，绑定的this的值会是？？？？

```js
var obj = {
	name: "zs",
    foo: function(){
        console.log(this);
    }
}
// ?
obj.foo(); // obj
```

![image-20211114153207703](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114153207703.png)

当然隐式绑定的this的值是对象obj了。那么结果显而易见，**隐式绑定的优先级是大于默认绑定的。**

而隐式绑定调用的函数的this的指向可以理解为，谁调用，this就指向谁。之所以我们说是叫做隐式绑定，是因为我们没有在函数上直接绑定this的值是xxx对象。







### 显示绑定

显示绑定就是显示的告诉函数，我要绑定this的值是xxx对象。

#### 全局函数显示绑定this

定义的全局函数，如果在执行的时候，直接显示的绑定this的值，那么很显然，this的值肯定是我们手动绑定上去的喽！

```js
function foo(){
      console.log(this);
    }
foo(); // window
foo.call("123"); // '123'
foo.apply(123); // 123
foo.bind({name:"zzz"})(); // {name: "zzz"}
```

![image-20211114154108450](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114154108450.png)

结果可以证明： **显示绑定的this的优先级是大于默认绑定规则的。**

那么接下来我们应该比较一下显示绑定和隐式绑定的优先级。



#### 对象的方法(函数)显示绑定this

我们定义一个对象，并对该函数的方法进行显示的绑定，看其中的this是指向哪里的。

```js
 var obj = {
     name: "zs",
     foo: function () {
         console.log(this);
     }
 }
obj.foo.call("123"); // '123'
obj.foo.apply("123"); // '123'
obj.foo.bind("123")(); // '123'
```

![image-20211114155033119](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114155033119.png)

通过取出对象obj的方法foo，然后对其进行显示的this绑定，我们发现结果是：this的指向是我们显示绑定的对象。

**那么这样是否就证明了：显示绑定的优先级就一定大于隐式绑定？**其实不然，我们通过**对象.方法名**的形式，已经是直接取出了这个定义在方法中的函数的引用，接下来进行该函数的显示绑定，严格来说已经和对象obj没有关系了。

```js
var obj = {
      name: "zs",
      foo: function () {
        console.log(this);
      }
    }
    var fn = obj.foo;
    fn(); // window
```

![image-20211114155457586](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114155457586.png)

可以看见，取出了对象中定义的函数，然后在进行调用，可以对比函数直接定义在全局中，然后通过默认绑定规则的调用该函数，所以最终this也是绑定在全局对象window上的。

**那么，我们要如何证明显示绑定和隐式绑定的优先级的大小呢？**

当然是有办法的：

我们的目的就是进行显示的绑定this，且隐式的调用这个显示绑定的函数，这样最终的this指向，就可以证明出我们两种方式的优先级了。

我们实现定义好函数，然后定义对象obj，且将定义好的全局函数通过显示的绑定this的值，然后将这个显示绑定好this的函数赋值给obj对象的foo属性。最后通过obj.foo()调用该函数，就知道显示绑定和隐式绑定的优先级了。

```js
function foo(){
    console.log(this);
}
var obj = {
    name: "zs",
    foo: foo.bind("123")
}
obj.foo(); // "123"
```

![image-20211114155914501](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114155914501.png)



根据结果，我们可以知道，**显示绑定this的优先级肯定是大于隐式绑定的。**







### new 绑定

new绑定，其实就是通过构造函数创建对象。当我们new的时候，会创建一个空对象，且将空对象的引用赋值给构造函数的this，这样this就会指向这个空对象，且会在构造函数的最后，**如果没有返回值**，自动帮我们返回这个this。

写成伪代码大概就是：

```js
function Person(name,age){
	this.name = name;
    this.age = age;
    // 不需要我们返回this
    return this;
}
```

new绑定需要的函数是构造函数，构造函数没有显示指定的返回值，且构造函数一般首字符是大写。

```js
function Person(name,age){
    this.name = name;
    this.age = age;
}
var person = new Person("zs",21);
console.log(person);
```

![image-20211114161130922](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114161130922.png)

new 绑定，其中的this的指向就是我们通过new关键字创建出来的空对象。然后通过构造函数给这个空对象添加各种属性。

![image-20211114161348641](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114161348641.png)













## 特殊的this绑定规则

上面的绑定规则只能说是我们经常遇到的，或者说我们会使用的绑定规则。**但是凡事总有例外**，总有一些特例对this的绑定让人感到苦恼，因为浏览器在解析它们的时候进行了特殊的处理，接下来介绍一下我知道的那些特例。



### 忽略显示绑定

我们上面看见了，显示绑定是大于隐式绑定，也是大于默认绑定规则的。这是毫无疑问的，前面已经证实过，那么接下来，我说的可能让你感到疑惑，为什么浏览器又会**忽略显示绑定呢？**

正如前面：

```js
function foo(){
    console.log(this);
}
// 默认绑定 直接调用 window
foo();

var obj = {name:"zs"};

// 显示绑定 三者都是 obj
foo.apply(obj);
foo.call(obj);
foo.bind(obj)();
```

**毋庸置疑：**这些结果如我们所预期一般。

但是，当我们进行显示绑定的时候，如果我们绑定的对象是**null**，或者是 **undefined**，那么我们函数执行时的this也是我们绑定的那个值吗？

```js
function foo(){
    console.log(this);
}
// 全是 window
foo.call(null);
foo.apply(null);
foo.call(undefined);
foo.apply(undefined);
```

![image-20211114145850490](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114145850490.png)

**结果说明了一切，**浏览器并不会把 **null或者undefined**绑定到this上。也就是说，在进行this绑定的时候，会查看绑定到this上的值，如果值不是null或者undefined才会正常的将对象绑定到this上，**否则，显示绑定的this依然会绑定为window对象**

![image-20211114150349876](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114150349876.png)

**如果既有显示绑定，又有隐式绑定的情况下，但是显示绑定的是null或者undefined，结果又会如何呢？**

我觉得即使不测试，应该也可以猜到结果。很显然嘛，**显示绑定是高于隐式绑定的**，那么结果肯定是以显示绑定为主啊！所以肯定还是 **window对象了**

```js
var obj = {
    name:"zs",
    foo(){
        console.log(this)
    }
}
// window
obj.foo.call(null);
obj.foo.apply(undefined);
obj.foo.bind(null)();
```

没啥毛病，结果就是window对象咯！

![image-20211114150806150](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114150806150.png)





### 间接函数引用

当我们**创建一个函数的间接引用**，这种情况使用默认绑定规则。

```JS
var obj1 = {
    name: "obj1",
    foo: function bar() {
        console.log(this);
    }
};
var obj2 = {
    name: "obj2"
};
(obj2.bar = obj1.foo)();
console.log((obj2.bar = obj1.foo));
var fn = (obj2.bar = obj1.foo);
fn();
```

![image-20211114173929711](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114173929711.png)

![image-20211114174003932](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20211114174003932.png)

可以发现：

1. 赋值语句 **`(obj2.bar = obj1.foo)`**的**结果是一个函数**（这个函数可能有名字，也可能没名字，取决于我们在对象中定义属性的时候，赋值的函数是否有名字）。其实就是取出了obj1的函数foo，**外面加上括号包裹就是将函数作为单独的执行体进行执行**。比如**`(obj1.foo)()`**的结果和上面的结果是不一致的，this还是隐式绑定为obj1。二者不同是因为前者括号里面的是赋值表达式，所以会取出函数obj1.foo然后通过后面的小括号进行直接调用（也叫间接函数引用），而后者还是隐式绑定；造成这种差异的方式主要还是在js引型解析的区别。

2. 得到的函数被直接调用，那么绑定规则是默认绑定。

**我写js代码习惯书写分号,在我们的赋值语句`(obj2.bar = obj1.foo)`**前面定义对象obj2的时候，如果没有分号结尾，是会报错的。

这种赋值语句会间接拿到事先定义的函数，并且作为这个语句的返回值。属于特殊情况，其实实际开发，我们不可能写这种代码。



### ES6的箭头函数 arrow function

箭头函数是ES6之后新增的编写函数的方法，写法比函数表达式更加简洁。

特点：

1. 箭头函数**不会绑定this**，arguments属性；
2. 箭头函数不能作为构造函数来使用（说白了就是不能搭配new使用）；



**箭头函数不使用上面的this的四种规则，通俗的说就是不绑定this**，而是根据外层作用域来决定this的指向。

```js
var foo = () => {console.log(this)}
// 均为 window
foo();
foo.call("123");
var obj = {
    name: "zs",
    foo: foo
};
obj.foo();
```





### 括号表达式

小括号中包含多项，这种只会取最后一项，且是取出最后一项的值。也就是影响this的指向，一般都是window/undefined的了。

```js
(1,2,3, oj.fn)() // window undefined
```





