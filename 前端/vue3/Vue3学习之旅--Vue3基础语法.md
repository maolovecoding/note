# Vue3学习之旅--爱上Vue3--Vue3基础语法(一)

**续上文：**

[vue3学习之旅--邂逅vue3-了解认识Vue3](https://blog.csdn.net/weixin_45747310/article/details/118683938)

[vue3学习之旅--邂逅vue3-了解认识Vue3(二)](https://blog.csdn.net/weixin_45747310/article/details/118691744)

### methods方法绑定this

**问题回顾：**

> 问题一：为什么不能使用箭头函数（官方文档有给出解释）？
>
> 问题二：不使用箭头函数的情况下，this到底指向的是什么？（可以作为一道面试题）

![image-20210714211627563](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210714211627563.png)

#### 不能使用箭头函数的原因？

**我们在methods中要使用data返回对象中的数据：**

那么这个this是必须有值的，并且应该可以通过this获取到data返回对象中的数据。

那么我们这个this能不能是**window**呢？

不可以是window，因为window中我们无法获取到data返回对象中的数据；但是如果我们使用箭头函数，那么这个this就会是window了；

为什么是window呢？

这里涉及到箭头函数使用this的查找规则，它会在自己的上层作用于中来查找this；

最终刚好找到的是script作用于中的this，所以就是window；

**this到底是如何查找和绑定的呢？**

且听下回分解。



#### this到底指向什么？

事实上Vue的源码当中就是对methods中的所有函数进行了遍历，并且通过bind绑定了this：

![image-20210714215745360](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210714215745360.png)

![image-20210714215847860](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210714215847860.png)

![image-20210714215927151](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210714215927151.png)

当然学过vue2的小伙伴可能觉得有点熟系。看不懂也没关系，以后慢慢我们可以看懂的。此处可以略过。

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <div id="app"></div>
  <script src="../vue3/vue3.js"></script>

  <!-- template 模板写法二  -->
  <!-- 直接在 template标签内书写模板 -->
  <template id="mao">
    <div>
      <h2>{{message}}</h2>
      <h2>{{counter}}</h2>
      <button @click='increment'>+</button>
      <button @click='decrement'>-</button>
      <!-- 验证箭头函数的指向 -->
      <!-- 发现打印出来的对象，的确是window对象 -->
      <button @click='increment'>验证箭头函数的指向</button>
    </div>
  </template>

  <script>
    Vue.createApp({
      template: '#mao',
      // 数据
      data() {
        return {
          message: 'hello Vue3!',
          counter: 0
        }
      },
      // 方法  
      // 问题：为什么我们定义在methods里面的方法，不能使用箭头函数？
      // 使用箭头函数，我们发现这个简单的计数器并不会动
      methods: {
        // 使用箭头函数的问题：

        increment: () => {
          // this === window ? 很明显是不能这样做的
          // window对象里面哪里来的counter属性，就算有，和我的vue实例又有什么关系呢？
          // 写出箭头函数的时候，这个this就指向了window
          console.log(this); // 打印的对象的确是window
          console.log(this === window); // true
          // 那么问题来了：为什么使用箭头函数，this就指向window了
          // 很明显：不难想象，肯定和vue的this绑定规则有关
          // 我们知道：箭头函数是不会绑定this的，箭头函数在哪个作用域中，this就指向那个作用域的对象
          // 那么你可能觉得，我箭头函数当前不就在 methods的作用域里面吗？
          // 其实不然：methods只是在定义对象  methods是什么，是对象啊
          // 什么是作用域： () 肯定是在括号里面啦
          // 所以箭头函数最终找到的this就到了最顶层的window
          this.counter++;
        },
        decrement: () => {
          this.counter--;
          console.log(this);
        }
      }
    }).mount('#app');

    window.name = 'win'
    const fn = function () {
      console.log(this.name);
    }
    fn() // 'win' 实际上执行的是 window.fn()  隐式绑定

    const obj = { name: '毛', fn: fn }
    obj.fn() // '毛'
  </script>
</body>

</html>
```



### 补充：vscode代码片段

我们在前面练习Vue的过程中，有些代码片段是需要经常写的，我们再VSCode中我们可以生成一个代码片段，方 便我们快速生成。

VSCode中的代码片段有固定的格式，所以我们一般会借助于一个在线工具来完成。

具体的步骤如下：

1. 复制自己需要生成代码片段的代码
2. https://snippet-generator.app/在该网站中生成代码片段；
3. 在VSCode中配置代码片段

##### 需要生成的代码片段

```html
  <div id="app"></div>
  <template id="my-app">
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    const app = Vue.createApp({
      template:'#my-app',
      data(){
        return {
          message:'hello Vue3!'
        }
      }
    }).mount('#app')
  </script>
```

##### vscode--> 设置 --> 用户代码片段

![image-20210714221840644](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210714221840644.png)



##### 选择我们需要生成的文件类型

![image-20210714222153482](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210714222153482.png)

**将需要生成的代码片段拷贝到上面的那个网站**

![image-20210714222233650](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210714222233650.png)

**在html.json 中进行配置**

![image-20210714222323614](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210714222323614.png)

**保存，就ok**

接下来直接使用prefix的属性值，敲出来 前缀然后回车，自动生成模板骨架。



## 模板语法

**React的开发模式：**

1. React使用的jsx，所以对应的代码都是编写的类似于js的一种语法； 
2. 之后通过Babel将jsx编译成 React.createElement 函数调用

**Vue也支持jsx的开发模式（后续有时间也会讲到）**：

1. 但是大多数情况下，使用基于HTML的模板语法； 
2. 在模板中，允许开发者以声明式的方式将DOM和底层组件实例的数据绑定在一起； 
3. 在底层的实现中，Vue将模板编译成虚拟DOM渲染函数，这个我会在后续给大家讲到；

所以，对于学习Vue来说，学习**模板语法**是非常重要的。



### Mustache双大括号语法

如果我们希望把数据显示到模板（template）中，使用最多的语法是 “Mustache”语法 (双大括号) 的文本插值。

1. 并且我们前端提到过，data返回的对象是有添加到Vue的响应式系统中； 
2. 当data中的数据发生改变时，对应的内容也会发生更新。 
3. 当然，Mustache中不仅仅可以是data中的属性，也可以是一个JavaScript的表达式。

```html
<div id="app"></div>
  <template id="my-app">
    <!-- 插值表达式的基本使用 -->
    <!-- 直接获取一个实例数据 -->
    <h2>{{message}}</h2>
    <!-- 也可以使用表达式 -->
    <h2>{{age * 10}}</h2>
    <h2>{{age / 10}}</h2>
    <!-- 也可以使用数据的方法 比如字符串的切割-->
    <!-- 字符串的切割 然后反转 -->
    <h2>{{message.split('').reverse().join('')}}</h2>
    <!-- 调用函数 -->
    <h2>{{getReverseMessage()}}</h2>
    <!-- 还可以使用计算属性，暂时不讲 -->

    <!-- 使用三元运算符 -->
    <h2>{{isShow? '哈哈' : '呵呵'}}</h2>
    <!-- 同一个标签也可以使用多个插值表达式 -->
    <h2>{{name}} {{message}}</h2>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!',
          name: '毛毛',
          age: 22,
          isShow:true
        }
      },
      methods: {
        getReverseMessage() {
          return this.message.split('').reverse().join('');
        }
      },
    }).mount('#app')
  </script>
```

![image-20210714225551729](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210714225551729.png)

**另外这种用法是错误的：**

```html
    <!-- 以下为错误用法 -->
    <!-- 1. 在插值表达式中定义变量，这是赋值语句，不是表达式 -->
    <h2>{{const a = '10'}}</h2>
    <!-- 使用if语句等 -->
    <h2>{{if(isShow) {return '哈哈哈'}}}</h2>
```



## Vue指令

### v-once指令

**v-once用于指定元素或者组件只渲染一次：**

1. 当数据发生变化时，元素或者组件以及其所有的子元素将视**为静态内容并且跳过；**

2. 该指令可以用于**性能优化；**

```html
    <!-- v-once指令：该指令修饰的元素或者组件只会渲染一次-->
    <h2 v-once>{{counter}}</h2>
    <h2>{{counter}}</h2>
    <button @click='increment'>+</button><br>
```

如果是子节点，也是只会渲染一次：

```html
    <!-- 如果是子节点，一样也只渲染一次 -->
    <div v-once>
      <h2>{{counter}}</h2>
      <button @click='increment'>+</button><br>
    </div>
```

```html
<div id="app"></div>
  <template id="my-app">
    <!-- v-once指令：该指令修饰的元素或者组件只会渲染一次-->
    <h2 v-once>{{counter}}</h2>
    <h2>{{counter}}</h2>
    <button @click='increment'>+</button><br>
    <!-- 如果是子节点，一样也只渲染一次 -->
    <div v-once>
      <h2>{{counter}}</h2>
      <button @click='increment'>+</button><br>
    </div>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          counter: 100
        }
      },
      methods: {
        increment() {
          this.counter++;
        }
      },
    }).mount('#app')
  </script>
```



### v-text 指令

用于更新元素的 textContent：

也就是用于更新元素的文本内容。

```html
    <template id="my-app">
      <!-- v-text指令 -->
      <h2 v-text='message'></h2>
      <!-- 等价于 -->
      <h3>{{message}}</h3>
      <!-- 但是插值表达式更加灵活 -->
    </template>
```



### v-html指令

默认情况下，如果我们展示的**内容本身是 html** 的，那么vue并不会对其进行特殊的解析。

如果我们希望这个内容被Vue可以解析出来，那么可以使用 v-html 来展示；

```html
<div id="app"></div>
  <template id="my-app">
    <div>{{message}}</div>
    <!-- v-html -->
    <div v-html='message'></div>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: '<div style="color:red">毛毛</div>'
        }
      }
    }).mount('#app')
  </script>
```

![image-20210714231448442](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210714231448442.png)

### v-pre 指令

1. v-pre用于跳过元素和它的子元素的编译过程，显示原始的Mustache标签
2. 跳过不需要编译的节点，加快编译的速度；

```html
    <!-- 如果我们希望有些内容不希望被解析，可以使用这个指令 -->
    <h2 v-pre>{{message}}</h2>
```

### v-cloak 指令

这个指令保持在元素上直到关联组件实例结束编译。和 CSS 规则如 [v-cloak] { display: none } 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到组件实 例准备完毕。

```html
  <style>
    [v-cloak]{
      display: none;
    }
  </style>

    <!-- v-cloak指令，隐藏未解析完的Mustache 标签（插值表达式），直到解析完毕 -->
    <h2 v-cloak>{{message}}</h2>
```

**这个h2标签在vue解析完全之前，不会显示。**



**以下的指令都为常用的指令，是本次学习的重点！**



### v-bind的绑定属性

#### 1. 基本使用

前面讲的一系列指令，主要是将值插入到模板内容中。

但是，除了内容需要动态来决定外，某些**属性**我们也希望动态来绑定。

比如：

1. 动态绑定a元素的href属性；
2. 动态绑定img元素的src属性；

**绑定属性我们使用v-bind：**

缩写：:

预期：any (with argument) | Object (without argument)

参数：attrOrProp (optional)

修饰符：.camel - 将 kebab-case attribute 名转换为 camelCase。

用法：动态地绑定一个或多个 attribute，或一个组件 prop 到表达式。

##### 2. 绑定基本属性

v-bind用于绑定一个或多个属性值，或者向另一个组件传递props值（这个学到组件时再介绍）；

在开发中，有哪些属性需要动态进行绑定呢？

还是有很多的，比如图片的链接src、网站的链接href、动态绑定一些类、样式等等

v-bind有一个对应的语法糖（:)，也就是简写方式。

在开发中，我们通常会使用语法糖的形式，因为这样更加简洁。

![image-20210715113152269](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715113152269.png)

```html
<div id="app"></div>
    <!-- 在vue2.x template模板中只能有一个根元素 -->
    <!-- 但是在vue3中 是允许template中有多个根元素 -->
    <template id="my-app">
      <!-- 使用v-bind 动态绑定实例里面的属性 -->
      <img v-bind:src="imgUrl" alt="">
      <a v-bind:href="url2">百度</a><br>
      <!-- v-bind 提供了一个语法糖的写法，在需要绑定的标签属性前面加上 : -->
      <a :href="url">我的博客</a>
    </template>
    <script src="../vue3/vue3.js"></script>
    <script>
      Vue.createApp({
        template:'#my-app',
        data(){
          return {
            message:'hello Vue3!',
            imgUrl: 'https://gitee.com/maolovecoding/picture/raw/master/images/image-20210714225551729.png',
            url: 'https://blog.csdn.net/weixin_45747310/article/details/118691744',
            url2: 'https://blog.csdn.net/weixin_45747310/article/details/118683938'
          }
        }
      }).mount('#app')
    </script>
```



#### 3. 绑定class属性

**介绍**

在开发中，有时候我们的元素class也是动态的，比如：

1. 当数据为某个状态时，字体显示<span style="color:red">红色</span>.
2. 当数据另一个状态时，字体显示黑色.

**绑定class的两种方式：**

1. 对象语法
2. 数组语法

##### 对象语法

我们可以传给 :class (v-bind:class 的简写) 一个对象，以动态地切换 class。

```html
<style>
    .active {
      color: red;
    }

    .bgColor {
      background-color: aquamarine;
    }

    .default {
      font-size: 40px;
    }
  </style>


<div id="app"></div>
  <template id="my-app">
    <!-- v-bind指令动态绑定class属性 -->
    <!-- 对象语法： :class={'active':boolean} -->
    <!-- boolean 布尔值可以直接指定，也可以使用vue实例的属性来指定 -->
    <!-- 如果布尔值为true，则绑定这个class属性，false则不绑定 -->
    <!-- 引号可以加，也可以不加 -->
    <h2 :class="{'bgColor':true,active:isActive}">哈哈哈哈，{{message}}</h2>
    <button @click='toggle'>文字颜色切换</button>
    <!-- 默认的class和动态的class结合 -->
    <h3 class='default' :class='{active:isActive}'>你好！</h3>
    <!-- 将对象放到一个单独的属性中 -->
    <h2 :class='classNameObj'>vue3 good good </h2>
    <!-- 将返回的对象放到一个方法中，调用方法拿到返回值也可以 -->
    <!-- 计算属性当然也是可以的 -->
    <h2 :class='getClass()'>通过方法拿到的对象</h2>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!',
          isActive: true,
          // 一般在定义属性存放数据的时候，不会嵌套的引用其他属性的值
          // 比如  classNameObj = {active:isActive} 这种写法是不被允许的
          classNameObj: {
            active: true,
            bgColor: true
          }
        }
      },
      methods: {
        toggle() {
          this.isActive = !this.isActive;
        },
        getClass() {
          return {
            active: true,
            bgColor: true
          };
        }
      },
    }).mount('#app')
  </script>
```

![image-20210715113532678](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715113532678.png)

![image-20210715113544817](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715113544817.png)



##### 数组语法

我们可以把一个数组传给 :class，以应用一个 class 列表

```html
<style>
    .active {
      color: red;
    }

    .bgColor {
      background-color: aquamarine;
    }

    .default {
      font-size: 40px;
    }
  </style>
</head>

<body>
  <div id="app"></div>
  <template id="my-app">
    <!-- 数组语法 -->
    <!-- 数组里面的元素，都会合并在一起，绑在标签的class属性上，权重一样的时候，后面覆盖区前面的 -->
    <h2 :class="['default','bgColor']">呵呵额和</h2>
    <!-- 数组里面的元素可以使用三元运算符 -->
    <h3 :class="['bgColor',isActive?'active':'']">{{message}}</h3>
    <!-- 三目运算符很明显，可读性不是很好，所以我们可以在数组红嵌套对象写法 -->
    <h2 :class="['bgColor',{'active':isActive}]">{{message}}</h2>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!',
          isActive: true,
          // 一般在定义属性存放数据的时候，不会嵌套的引用其他属性的值
          // 比如  classNameObj = {active:isActive} 这种写法是不被允许的
          classNameObj: {
            active: true,
            bgColor: true
          }
        }
      },
      methods: {
        toggle() {
          this.isActive = !this.isActive;
        },
        getClass() {
          return {
            active: true,
            bgColor: true
          };
        }
      },
    }).mount('#app')
  </script>
```

![image-20210715113823162](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715113823162.png)



#### 4. 绑定style

**介绍**：我们可以利用v-bind:style来绑定一些CSS内联样式。

因为：某些样式我们需要根据数据动态来决定。比如文字的颜色，大小，行间距，行高等。

CSS property 名可以用**驼峰式** (camelCase) 或**短横线分隔** (kebab-case，记得用引号括起来) 来命名

绑定class有两种方式：

1. 对象语法
2. 数组语法



##### 对象语法

1. 不动态绑定时

    ```html
    <!-- 没有动态绑定时： -->
    <h2 style="color: aquamarine;">{{message}}</h2>
    ```

2. 动态绑定style

   ```html
   <div id="app"></div>
     <template id="my-app">
       <!-- 没有动态绑定时： -->
       <h2 style="color: aquamarine;">{{message}}</h2>
       <!-- 动态绑定style，对象语法 -->
       <!-- 1. 对象形式书写时，key可以不需要加引号，但是如果值是我们写的一个定值（常量），那么值必须加单引号 -->
       <h2 :style="{color: '#ccc'}">1111111111111111111111</h2>
       <!-- 如果值不加单引号，则值会被vue看成书写，会去从实例中找 -->
       <h2 :style="{color: myOrange}">222222222222222</h2>
       <!-- 此外，如果键名是多个单词的组合，不加单引号则遵循驼峰命名， -->
       <h3 :style="{backgroundColor: '#aadacc'}">3333333333333333</h3>
       <!-- 否则使用 - 连接时，则必须使用引号 -->
       <h3 :style="{'background-color': '#dddacc'}">4444444444444444</h3>
       <!-- 也可以进行值的拼接,将动态绑定的属性值与一个常量进行拼接 -->
       <h2 :style="{fontSize:myFontSize + 'px'}">555555555555555555</h2>
       <!-- 如果属性名和定义在实例中的属性值同名，则可以吃采取简写模式 -->
       <h2 :style="{backgroundColor,fontSize}">666666666666</h2>
       <!-- 直接绑定对象当然也肯定是允许的啦 -->
       <h2 :style="myStyle">7777777777777</h2>
       <!-- 绑定函数或者计算属性返回的对象 -->
       <h2 :style="getStyle()">8888888888888</h2>
     </template>
     <script src="../vue3/vue3.js"></script>
     <script>
       Vue.createApp({
         template: '#my-app',
         data() {
           return {
             message: 'hello Vue3!',
             myOrange: 'orange',
             myFontSize: 30,
             backgroundColor: '#aedcfa',
             fontSize: '40px',
             myStyle: {
               color: '#eae',
               fontSize: '30px',
               backgroundColor: '#ccc'
             }
           }
         },
         methods: {
           getStyle(){
             return {
               color: '#eaa',
               fontSize: '40px',
               backgroundColor: '#bdc'
             }
           }
         },
       }).mount('#app')
     </script>
   ```

   ![image-20210715114606815](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715114606815.png)



##### 数组语法

style 的数组语法可以将多个样式对象应用到同一个元素上

```html
<div id="app"></div>
  <template id="my-app">
    <!-- 数组语法的使用：一般我们会在数组中放多个对象，然后多个对象会都绑定到style上，自动合并 -->
    <h2 :style="[style1,style2]">111111111111111111111</h2>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!',
          style1: { color: 'red', fontSize: '30px' },
          style2: { backgroundColor: '#ccc' }
        }
      }
    }).mount('#app')
  </script>
```

![image-20210715114636438](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715114636438.png)

#### 5. 动态绑定属性

**在某些情况下，我们属性的名称可能也不是固定的：**

1. 前端我们无论绑定src、href、class、style，属性名称都是固定的；
2. 如果属性名称不是固定的，我们可以使用 :[属性名]=“值” 的格式来定义；
3. 这种绑定的方式，我们称之为动态绑定属性；

```html
<style>
    .active {
      color: aquamarine;
    }
  </style>

  <div id="app"></div>
  <template id="my-app">
    <!-- 属性名动态绑定到name，name具体是什么值，绑定的就是什么属性，
      然后在通过value绑定这个动态的属性应该具有的值 -->
    <div :[name]="value">111111111111111</div>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!',
          name: 'class',
          value: 'active'
        }
      }
    }).mount('#app')
  </script>
```

![image-20210715114706276](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715114706276.png)



#### 6. 绑定一个对象

如果我们希望将一个对象的所有属性，绑定到元素上的所有属性，应该怎么做呢？

非常简单，我们可以直接使用 **v-bind 绑定一个 对象**；

例如：info对象会被拆解成div的各个属性

```html
<div id="app"></div>
  <template id="my-app">
    <!-- 当我们将动态的将一个对象上的所有属性都绑定给一个标签元素时 -->
    <!-- 我们可以直接用 v-bind='对象' -->
    <div v-bind="info">{{message}}</div>
    <!-- 也可以使用语法糖，但是貌似看起来有点怪，不是很建议使用 -->
    <div :="info">{{message}}</div>
    <!-- 这种绑定方式会将绑定的对象的属性全都解构到这个元素上，成为这个元素的属性 -->
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!',
          info: {
            name: '哈哈',
            style: 'color:red',
            age: 22,
            data: '你好！'
          }
        }
      }
    }).mount('#app')
  </script>
```

![image-20210715114815454](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715114815454.png)



### v-on指令 绑定事件

前面我们绑定了**元素的内容和属性**，在前端开发中另外一个非常重要的特性就是**交互。**

在前端开发中，我们需要经常和用户进行各种各样的交互：

这个时候，我们就必须监听用户发生的事件，比如**点击、拖拽、键盘事件**等等

在Vue中如何监听事件呢？使用**v-on指令**。

接下来我们来看一下v-on的用法：

v-on的使用：

缩写：@

预期：Function | Inline Statement | Object

参数：event

(事件)修饰符：

1. .stop - 调用 event.stopPropagation()。
2. .prevent - 调用 event.preventDefault()
3. .capture - 添加事件侦听器时使用 capture 模式
4. .self - 只当事件是从侦听器绑定的元素本身触发时才触发回调
5. .{keyAlias} - 仅当事件是从特定键触发时才触发回调
6. .once - 只触发一次回调。
7. .left - 只当点击鼠标左键时触发
8. .right - 只当点击鼠标右键时触发。
9. .middle - 只当点击鼠标中键时触发
10. .passive - { passive: true } 模式添加侦听器

用法：绑定事件监听



#### 基本使用

我们可以使用v-on来监听一下点击的事件：

```html
    <!-- v-on指令，绑定监听的事件 v-on:监听的事件 = "methods里面定义的函数" -->
    <button v-on:click="btnClick1">按钮1</button><br>
```

![image-20210715115310402](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715115310402.png)

v-on:click可以写成@click，是它的语法糖写法：

```html
<button @click="btnClick1">按钮1</button><br>
```

当然，我们也可以绑定其他的事件：

```html
<!-- 监听鼠标移动的方法 -->
<div v-on:mousemove="btn2Move">鼠标在移动事件</div>
```

![image-20210715115512649](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715115512649.png)

如果我们希望一个元素绑定多个事件，这个时候可以传入一个对象：

```html
    <button @="{'click':btnClick1,'mousemove':btn2Move}" @click="btnClick2">绑定多个事件，两个点击事件都会生效</button>

```

![image-20210715115611372](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715115611372.png)

**基本使用总览：**大家多多尝试

```html
<style>
    .dd{
      width: 200px;
      height: 200px;
      background-color: aquamarine;
    }
  </style>

<div id="app"></div>
  <template id="my-app">
    <!-- v-on指令，绑定监听的事件 v-on:监听的事件 = "methods里面定义的函数" -->
    <button v-on:click="btnClick1">按钮1</button><br>
    <!-- 监听鼠标移动的方法 -->
    <div class="dd" v-on:mousemove="btn2Move">鼠标在移动事件</div>
    <!-- 语法糖的写法 -->
    <button @click="btnClick2">按钮2</button><br>
    <!-- 绑定一个表达式 -->
    <button @click="counter++">点击数字加一</button> <span style="color: blueviolet;">{{counter}}</span>
    <!-- 同时绑定多个事件的时候，我们一般采用对象的形式 -->
    <!-- 同一个事件不能在对象里面多次绑定，不然后面的会覆盖前面的 -->
    <!-- 因为在对象中，同名属性会被后面定义的覆盖 -->
    <button v-on="{'click':btnClick1,'click':btnClick2}">只有第二个点击事件生效</button><br>
    <!-- 将同一个事件分开绑定，会同时生效 -->
    <button @="{'click':btnClick1,'mousemove':btn2Move}" @click="btnClick2">绑定多个事件，两个点击事件都会生效</button>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!',
          counter: 0
        }
      },
      methods: {
        btnClick1() {
          console.log('btn1 发生了点击');
        },
        btn2Move(){
          console.log('鼠标移动');
        },
        btnClick2() {
          console.log('btn2 发生了点击');
        },
      },
    }).mount('#app')
  </script>
```

![image-20210715115941888](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715115941888.png)

 

#### v-on参数传递

当通过methods中定义方法，以供@click调用时，需要注意参数问题：

1. 如果该方法不需要额外参数，那么方法后的()可以不添加

   ```html
   <!-- 绑定事件，直接绑定事件处理函数名，不带括号，也会默认传递一个触发的事件参数 -->
   <button @click="btnClick">按钮</button>
   ```

   ![image-20210715120320150](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715120320150.png)

2. 如果方法本身中有一个参数，那么会默认将原生事件event参数传递进去

   ```html
   <button @click="btnClick">按钮</button>
   ```

3. 如果需要同时传入某个参数，同时需要event时，可以通过$event传入事件。

   ```html
   <!-- vue中，使用 $event 来获取事件参数 -->
   <button @click="btnClick2($event,'你好！')">按钮</button>
   ```

   ![image-20210715120339775](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715120339775.png)

4. 如果事件处理函数带括号了，不传递参数，默认是接收不到 event事件的

   ```html
   <!-- 注意，绑定函数时如果带括号，却不传递参数，那么事件参数也是拿不到的-->
   <button @click="btnClick3()">按钮</button>
   ```

   ![image-20210715120438940](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715120438940.png)

**总览：**

```html
<div id="app"></div>
  <template id="my-app">
    <!-- 绑定事件，直接绑定事件处理函数名，不带括号，也会默认传递一个触发的事件参数 -->
    <button @click="btnClick">按钮</button>
    <!-- 点击按钮的时候，还需传递其他参数时，需要书写的时候带上函数的括号，并传递参数 -->
    <!-- vue中，使用 $event 来获取事件参数 -->
    <button @click="btnClick2($event,'你好！')">按钮</button>
    <!-- 注意，绑定函数时如果带括号，却不传递参数，那么事件参数也是拿不到的-->
    <button @click="btnClick3()">按钮</button>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!'
        }
      },
      methods: {
        btnClick(event) {
          console.log(event);
        },
        btnClick2(event,data) {
          console.log(event);
          console.log(data);
        },
        btnClick3(event) {
          console.log(event);
        }
      },
    }).mount('#app')
  </script>
```

#### v-on 事件修饰符

v-on支持修饰符，修饰符相当于对事件进行了一些特殊的处理：

1. .stop - 调用 event.stopPropagation()。
2. .prevent - 调用 event.preventDefault()
3. .capture - 添加事件侦听器时使用 capture 模式
4. .self - 只当事件是从侦听器绑定的元素本身触发时才触发回调
5. .{keyAlias} - 仅当事件是从特定键触发时才触发回调
6. .once - 只触发一次回调。
7. .left - 只当点击鼠标左键时触发
8. .right - 只当点击鼠标右键时触发。
9. .middle - 只当点击鼠标中键时触发
10. .passive - { passive: true } 模式添加侦听器

例如：.stop的使用如下：

```html
<div id="app"></div>
  <template id="my-app">
    <div @click="divClick">
      <button @click="btnClick1">按钮1 不阻止冒泡</button>
      <button @click="btnClick2">按钮2 使用js提供的方法阻止冒泡</button>
      <button @click.stop="btnClick3">按钮3 使用事件修饰符阻止冒泡</button>
    </div>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!'
        }
      },
      methods: {
        btnClick1() {
          console.log('btn 111111111')
        },
        btnClick2(event) {
          // 阻止冒泡
          event.stopPropagation()
          console.log('btn 222222222')
        },
        btnClick3() {
          console.log('btn 33333333')
        },
        divClick(){
          console.log('div 44444444');
        }
      }
    }).mount('#app')
  </script>
```

![](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715120931467.png)

![image-20210715121004503](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715121004503.png)

![image-20210715121026449](https://gitee.com/maolovecoding/picture/raw/master/images/image-20210715121026449.png)

事件修饰符的其他使用可以自行练习。emmm，具体的细节我会再出一个博客专门讲解修饰符以及相关案例。



今日分享结束！冲冲冲







# Vue3学习之旅--爱上Vue3--Vue3基础语法(二)--模板语法

**续上文：**

[Vue3学习之旅–爱上Vue3–Vue3基础语法(一)--以及vscode基本使用和快速生成代码片段](https://blog.csdn.net/weixin_45747310/article/details/118756641?spm=1001.2014.3001.5501)

## 条件渲染

在某些情况下，我们需要根据当前的条件决定某些元素或组件是否渲染，这个时候我们就需要进行条件判断了。

Vue提供了下面的指令来进行条件判断：

1. v-if
2. v-else
3. v-else-if
4. v-show

下面我们开始逐个的学习。

### v-if、v-else、v-else-if

v-if、v-else、v-else-if用于根据条件来渲染某一块的内容：

1. 这些内容只有在条件为true时，才会被渲染出来；

2. 这三个指令与JavaScript的条件语句if、else、else if类似；

   ```html
   <template id="my-app">
       <h2 v-if="score > 90">优秀</h2>
       <!-- 第一个条件不满足则判断v-else-if里面的条件是否满足 -->
       <h2 v-else-if="score > 80">良好</h2>
       <!-- 如果上面的条件都不满足，则执行渲染v-else所在的标签 -->
       <h2 v-else>不及格</h2>
     </template>
   ```

**v-if的渲染原理**:

1. v-if是惰性的

2. 当条件为false时，其判断的内容完全不会被渲染或者会被销毁掉

3. 当条件为true时，才会真正渲染条件块中的内容

   ```html
   <template id="my-app">
         <!-- v-if 的使用 如果条件为真（true） 则渲染这个标签，否则不会渲染 -->
         <h2 v-if="isShow">{{message}}</h2>
         <button @click="isShow = !isShow">切换是否显示</button>
       </template>
   ```

### template元素

因为v-if是一个指令，所以必须将其添加到一个元素上：

但是如果我们希望切换的是多个元素呢?

此时我们**渲染div**，但是我们并不希望**div**这种元素被渲染；

这个时候，我们可以选择使用template；

template元素可以当做不可见的包裹元素，并且在v-if上使用，但是最终template不会被渲染出来：

有点类似于小程序中的block.

```html
<template id="my-app">
<div v-if="isShow">
    <!-- 当我们想根据条件选择性的渲染
        下面两块数据其中之一的时候，
        我们可以在那些标签的外面，加上一个div，然后将判断条件写在div上，
        此时也能达到我们的要求，但是，却在渲染数据的时候多了一个不必要的div  -->
        <h2>{{message}}</h2>
        <h2>{{message}}</h2>
        <h2>{{message}}</h2>
      </div>
      <div v-else>
        <h2>哈哈</h2>
        <h2>哈哈</h2>
        <h2>哈哈</h2>
      </div>
</template>
```

### v-show

v-show和v-if的用法看起来是一致的，也是根据一个条件决定是否显示元素或者组件：

```html
<template id="my-app">
	<h1 v-show="isShow
                ">
        {{message}}
    </h1>
</template>
```

### v-show和v-if的区别

**首先，在用法上的区别:**

1. v-show是不支持template
2. v-show不可以和v-else一起使用

**其次，本质的区别:**

1. v-show元素无论是否需要显示到浏览器上，它的DOM实际都是有渲染的，只是通过CSS的display属性来进行 切换
2. v-if当条件为false时，其对应的元素压根不会被渲染到DOM中；

**开发中如何进行选择呢?**

1. 如果我们的元素需要在显示和隐藏之间频繁的切换，那么使用v-show
2. 如果不会频繁的发生切换，那么使用v-if

```html
<template id="my-app">
      <!-- 当我们想根据条件选择性的渲染
        下面两块数据其中之一的时候，
        我们可以在那些标签的外面，加上一个div，然后将判断条件写在div上，
        此时也能达到我们的要求，但是，却在渲染数据的时候多了一个不必要的div  -->
      <div v-if="isShow">
        <h2>{{message}}</h2>
        <h2>{{message}}</h2>
        <h2>{{message}}</h2>
      </div>
      <div v-else>
        <h2>哈哈</h2>
        <h2>哈哈</h2>
        <h2>哈哈</h2>
      </div>
      <!-- 所以解决上面多出一个div的问题，我们可以使用 template标签进行包裹 -->
      <!-- 使用template标签的好处，就是渲染的时候，template标签在渲染到浏览器的时候，不会渲染到页面 -->
      <template v-if="!isShow">
        <h2>{{message}}</h2>
        <h2>{{message}}</h2>
        <h2>{{message}}</h2>
      </template>
      <template v-else>
        <h2>哈哈</h2>
        <h2>哈哈</h2>
        <h2>哈哈</h2>
      </template>
    </template>
```

```html
<template id="my-app">
    <!-- v-show标签的使用和v-if一样，都是根据条件决定是否渲染所在的标签 -->
    <!-- 但是二者也是有区别的：
      1. 条件不成立的时候：
        v-if 所在的标签直接被移除，
        v-show 所在的标签只是被隐藏了 display:none 
          实际上标签还是存在的，而且已经被浏览器解析了
      2. 在用法上：
        v-show 不支持 template 标签
        v-show 不可以和 v-else 一起使用
      3. 本质区别：
        v-show所在的标签，无论条件是否成立，都被浏览器解析和渲染，只是通过css来进行了切换显示隐藏
        v-if 所在的标签，条件不成立时 标签压根不喝被渲染到 DOM 上

      二者使用如何选择：（好的选择可以提高性能）
        频繁切换显示和隐藏： 使用 v-show
        不频繁 使用 v-if
    -->
    <h2 v-show="isShow">{{message}} v-show标签</h2>
    <h2 v-if="isShow">{{message}} v-if标签</h2>
  </template>
```

## 列表渲染

在真实开发中，我们往往会从服务器拿到一组数据，并且需要对其进行渲染。

这个时候我们可以使用v-for来完成；v-for类似于JavaScript的for循环，可以用于遍历一组数据

### v-for基本使用

v-for的基本格式是 **"item in 数组"**：

1. 数组通常是来自**data**或者**prop**，也可以是其他方式
2. item是我们给每项元素起的一个别名，这个别名可以自定来定义

我们知道，在遍历一个数组的时候会经常需要拿到数组的索引：

1. 如果我们需要索引，可以使用格式： **"(item, index) in 数组**"
2. 注意上面的顺序：数组元素项item是在前面的，索引项index是在后面的；

```html
<ul>
      <!-- v-for="元素 in 数组" -->
      <li v-for="movie in movies">{{movie}}</li>
    </ul>
    <ul>
      <!-- 如果想拿到元素的索引, v-for="(item,index) in arr" -->
      <li v-for="(movie,index) in movies">{{movie}} --- {{index}}</li>
    </ul>
```

### v-for支持的类型

v-for也支持遍历对象，并且支持有一二三个参数:

1. 一个参数： "value in object";
2. 二个参数： "(value, key) in object";
3. 三个参数： "(value, key, index) in object"

```html
<!-- v-for 遍历的当然也可以是一个对象 -->
    <ol>
      <!-- 遍历对象时，拿到的就是对象的属性值，属性名,也可以拿到索引(其实就是取到属性的次序) -->
      <!-- 只有一个参数时，拿到的就是对象的属性值，两个参数 拿到的就是属性值和属性名，索引是第三个的参数 -->
      <li v-for="(value,key,index) in info">{{value}} --- {{key}} --- {{index}}</li>
    </ol>
```



v-for同时也支持数字的遍历：

每一个item都是一个数字.

```html
<!-- 遍历数字 -->
    <h2>遍历数字</h2>
    <ul>
      <li v-for="(item ,index) in 10">{{item}} ---> {{index}}</li>
    </ul>
```

```html
<div id="app"></div>
  <template id="my-app">
    <!-- v-for的基本使用 -->
    <ul>
      <!-- v-for="元素 in 数组" -->
      <li v-for="movie in movies">{{movie}}</li>
    </ul>
    <ul>
      <!-- 如果想拿到元素的索引, v-for="(item,index) in arr" -->
      <li v-for="(movie,index) in movies">{{movie}} --- {{index}}</li>
    </ul>
    <!-- v-for 遍历的当然也可以是一个对象 -->
    <ol>
      <!-- 遍历对象时，拿到的就是对象的属性值，属性名,也可以拿到索引(其实就是取到属性的次序) -->
      <!-- 只有一个参数时，拿到的就是对象的属性值，两个参数 拿到的就是属性值和属性名，索引是第三个的参数 -->
      <li v-for="(value,key,index) in info">{{value}} --- {{key}} --- {{index}}</li>
    </ol>

    <!-- 遍历数字 -->
    <h2>遍历数字</h2>
    <ul>
      <li v-for="(item ,index) in 10">{{item}} ---> {{index}}</li>
    </ul>
  </template>
  <script src="../../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!',
          movies: ["海贼王", "火影忍者", "名侦探柯南", "喜洋洋"],
          info: {
            name: '张三',
            age: 22,
            gender: '男'
          }
        }
      }
    }).mount('#app')
  </script>
```



### template元素

类似于v-if，你可以使用 template 元素来循环渲染一段包含多个元素的内容：

我们使用template来对多个元素进行包裹，而不是使用div来完成

```html
<div id="app"></div>
  <template id="my-app">
    <!-- v-for 和 template标签的结合使用 -->
    <!-- 展示 info对象里面的每个属性和值，并且使用 分割线分割 -->
    <ul>
      <!-- 为什么不使用 div？ 首先，使用div是会浪费性能，会多渲染标签到页面上，
        其次，在html5开发规范上，不建议在ul里面使用div，不要在ul或者li里面使用其他的标签 
      -->
      <template v-for="(value,key) in info">
        <li>{{key}}</li>
        <li>{{value}}</li>
        <li>华丽的分割线</li>
      </template>
    </ul>

  </template>
  <script src="../../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!',
          info: {
            name: '毛毛',
            age: 21,
            gender: '男'
          }
        }
      }
    }).mount('#app')
  </script>
```

### 数组更新检测

Vue 将被侦听的数组的变更方法进行了包裹，所以它们也将会触发视图更新。这些被包裹过的方法包括:

1. push()
2. pop()
3. shift()
4. unshift()
5. splice()
6. sort()
7. reverse()

**替换数组的方法**

上面的方法会直接修改原来的数组，但是某些方法不会替换原来的数组，而是会生成新的数组，比如 filter()、 concat() 和 slice().

```html
<div id="app"></div>
  <template id="my-app">
    <button @click="addArr">push</button>
    <button @click="delArr">pop</button>
    <button @click="delArrFirst">shift</button>
    <button @click="addArrFirst">unshift</button>
    <button @click="spliceArr">splice</button>
    <button @click="sortArr">sort</button>
    <button @click="reverseArr">reverse</button>
    <ul>
      <li v-for="(value,index) in arr">{{value}} --- > {{index}}</li>
    </ul>
  </template>
  <script src="../../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!',
          arr: ["张三", "李四", "王五", "赵六"]
        }
      },
      methods: {
        /* 
          Vue 将侦听的数组变更方法进行了包裹，所以他们也将会触发视图的更新。
          这些包裹过的方法有：
            1. push()
            2. pop()
            3. shift() 移除数组第一个元素
            4. unshift() 在数组头插入元素
            5. splice() 数组的切片
            6. sort() 排序
            7. reverse() 反转

          // 而向 concat，slice，filter 不会改变原数组，
              而是生成新数组，所以不是响应式的，只要将生成的数组重新赋值给原数组，就能进行视图的更新了
              
        */
        addArr() {
          this.arr.push("小七")
        },
        delArr() {
          this.arr.pop()
        },
        delArrFirst() {
          this.arr.shift()
        },
        addArrFirst() {
          this.arr.unshift("小鸡鸡")
        },
        spliceArr() {
          this.arr.splice(1, 1)
        },
        // 按照元素的长度进行排序
        sortArr() {
          this.arr.sort((first, second) => {
            return first.length - second.length
          })
        },
        reverseArr() {
          this.arr.reverse()
        }
      },
    }).mount('#app')
  </script>
```

### v-for中的key是什么作用？

在使用v-for进行列表渲染时，我们通常会给元素或者组件绑定一个**key属性。**

这个key属性有什么作用呢？我们先来看一下**官方的解释：**

1. key属性主要用在**Vue的虚拟DOM算法**，在新旧nodes对比时辨识**VNodes；**
2. 如果不使用key，Vue会使用一种最大限度减少动态元素并且尽可能的尝试就地**修改/复用相同类型元素的算法**
3. 而使用key时，它会基于key的变化重新排列元素顺序，并且会移除/销毁key不存在的元素.

官方的解释对于初学者来说并不好理解，比如下面的问题:

1. 什么是新旧nodes，什么是VNode?
2. 没有key的时候，如何尝试修改和复用的?
3. 有key的时候，如何基于key重新排列的?

#### 认识VNode

我们先来解释一下VNode的概念：

因为目前我们还没有比较完整的学习组件的概念，所以目前我们先理解HTML元素创建出来的VNode；

VNode的全称是Virtual Node，也就是虚拟节点；

事实上，无论是组件还是元素，它们最终在Vue中表示出来的都是一个个VNode；

VNode的本质是一个JavaScript的对象；

![image-20210720090447245](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720090447245.png)

![image-20210720090504233](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720090504233.png)

![image-20210720090517688](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720090517688.png)

#### 虚拟DOM

如果我们不只是一个简单的div，而是有一大堆的元素，那么它们应该会形成一个VNode Tree：

#### 插入F的案例

我们先来看一个案例：这个案例是当我点击按钮时会在中间插入一个f；

![image-20210720090704551](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720090704551.png)

我们可以确定的是，这次更新对于ul和button是不需要进行更新，需 要更新的是我们li的列表.

在Vue中，对于相同父元素的子元素节点并不会重新渲染整个列 表；

因为对于列表中 a、b、c、d它们都是没有变化的；在操作真实DOM的时候，我们只需要在中间插入一个f的li即可；

那么Vue中对于列表的更新究竟是如何操作的呢？

1. Vue事实上会对于有key和没有key会调用两个不同的方法；
2. 有key，那么就使用 patchKeyedChildren方法；
3. 没有key，那么就使用 patchUnkeyedChildren方法；

#### Vue源码对于key的判断

![image-20210720091015467](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720091015467.png)

#### 没有key的操作(源码)

![image-20210720091150425](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720091150425.png)

#### 没有key的过程如下

我们会发现上面的diff算法效率并不高:

c和d来说它们事实上并不需要有任何的改动；

但是因为我们的c被f所使用了，所有后续所有的内容都要一次进行改动，并且最后进行新增；

![image-20210720091648154](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720091648154.png)

#### 有key执行操作（源码）

![image-20210720091351614](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720091351614.png)

#### 有key的diff算法如下

第一步的操作是从头开始进行遍历、比较：

1. a和b是一致的会继续进行比较；
2. c和f因为key不一致，所以就会break跳出循环；

![image-20210720091445993](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720091445993.png)

第二步的操作是从尾部开始进行遍历、比较：

![image-20210720091502417](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720091502417.png)

第三步是如果旧节点遍历完毕，但是依然有新的节点，那么就新增节点：

![image-20210720091520509](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720091520509.png)

第四步是如果新的节点遍历完毕，但是依然有旧的节点，那么就移除旧节点:

![image-20210720091537722](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720091537722.png)

第五步是最特色的情况，中间还有很多未知的或者乱序的节点:

![image-20210720091554169](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue3/image-20210720091554169.png)

所以我们可以发现，Vue在进行diff算法的时候，会尽量利用我们的key来进行优化操作：

在没有key的时候我们的效率是非常低效的；

在进行插入或者重置顺序的时候，保持相同的key可以让diff算法更加的高效；





# Vue3的Options-API

**续上文：**

[vue3学习之旅--邂逅vue3-了解认识Vue3](https://blog.csdn.net/weixin_45747310/article/details/118683938)

[vue3学习之旅--邂逅vue3-了解认识Vue3(二)](https://blog.csdn.net/weixin_45747310/article/details/118691744)

[Vue3学习之旅–爱上Vue3–Vue3基础语法(一)--以及vscode基本使用和快速生成代码片段](https://blog.csdn.net/weixin_45747310/article/details/118756641?spm=1001.2014.3001.5501)

[Vue3学习之旅--爱上Vue3--Vue3基础语法(二)--模板语法--条件渲染--列表渲染--为什么Vue的v-for需要绑定key属性](https://blog.csdn.net/weixin_45747310/article/details/118926892)



## 复杂data的处理方式

我们知道，在模板中可以直接通过**插值语法**显示一些data中的数据。但是在某些情况，我们可能需要对数据进行一些转化后再显示，或者需要将多个数据结合起来进行显示；

1. 比如我们需要对多个data数据进行运算、三元运算符来决定结果、数据进行某种转化后显示
2. 在模板中使用表达式，可以非常方便的实现，但是设计它们的初衷是用于简单的运算
3. 在模板中放入太多的逻辑会让模板过重和难以维护
4. 并且如果多个地方都使用到，那么会有大量重复的代码

**我们有没有什么方法可以将逻辑抽离出去呢？**

可以，其中一种方式就是将逻辑抽取到一个method中，放到**methods的options**中，但是，这种做法有一个直观的弊端，就是所有的data使用过程都会变成了一个方法的调用；另外一种方式就是使用**计算属性computed**；

### 认识计算属性computed

**什么是计算属性呢？**

**官方并没有给出直接的概念解释**；而是说：对于任何包含响应式数据的复杂逻辑，你都应该使用**计算属性**；

**计算属性**将被混入到组件实例中。所有 getter 和 setter 的 this 上下文自动地绑定为组件实例

#### 计算属性的用法

选项：computed

类型：{ [key: string]: Function | { get: Function, set: Function } }



**那接下来我们通过案例来理解一下这个计算属性**

#### 案例实现思路

**我们来看三个案例：**

案例一：我们有两个变量：firstName和lastName，希望它们拼接之后在界面上显示；

案例二：我们有一个分数：score，当score大于60的时候，在界面上显示及格；当score小于60的时候，在界面上显示不及格。

案例三：我们有一个变量message，记录一段文字：比如Hello World。某些情况下我们是直接显示这段文字；某些情况下我们需要对这段文字进行反转；

**我们可以有三种实现思路**：

1. 思路一：在模板语法中直接使用表达式
2. 思路二：使用method对逻辑进行抽取；
3. 思路三：使用计算属性computed；

##### 实现思路一：模板语法

**思路一的实现：模板语法**

```html
<template id="my-app">
    <!-- 使用插值语法完成字符串的拼接等案例 -->
    <h2>{{firstName + lastName}}</h2>
    <h2>{{score >= 60 ? '及格' : '不及格'}}</h2>
    <h3>{{message.split('').reverse().join('')}}</h3>
  </template>
```

缺点一：模板中存在大量的复杂逻辑，不便于维护（模板中表达式的初衷是用于简单的计算）；

缺点二：当有多次一样的逻辑时，存在重复的代码；

缺点三：多次使用的时候，很多运算也需要多次执行，没有缓存；

##### 实现思路二：method实现

**思路二的实现：method实现**

```html
<div id="app"></div>
  <template id="my-app">
    <h2>{{getFullName()}}</h2>
    <h2>{{getResult()}}</h2>
    <h2>{{getReverseMessage()}}</h2>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          firstName: '毛',
          lastName: '俊',
          score: 80,
          message: 'hello Vue3!'
        }
      },
      methods: {
        getFullName(){
          return this.firstName + this.lastName
        },
        getResult(){
          return this.score>=60? '及格' : '不及格'
        },
        getReverseMessage(){
          return this.message.split('').reverse().join('')
        }
      },
    }).mount('#app')
  </script>
```

**缺点一**：我们事实上先显示的是一个结果，但是都变成了一种方法的调用；

**缺点二**：多次使用方法的时候，没有缓存，也需要多次计算

##### 思路三的实现：computed实现

**思路三的实现：computed实现**

```html
<div id="app"></div>
  <template id="my-app">
    <h2>{{fullName}}</h2>
    <h2>{{result}}</h2>
    <h2>{{reverseMessage}}</h2>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          firstName: '毛',
          lastName: '俊',
          score: 80,
          message: 'hello Vue3!'
        }
      },
      // 计算属性  计算属性在多次使用时，如果数据没有发生改变，是会重复使用上次的数据的
      computed: {
        // 定义一个计算属性叫做 fullName
        // 虽然计算属性看起来和函数很相似，但是本质上计算属性其实就是一个属性
        fullName() {
          return this.firstName + this.lastName
        },
        result() {
          return this.score >= 60 ? '及格' : '不及格'
        },
        reverseMessage() {
          return this.message.split('').reverse().join('')
        }
      }
    }).mount('#app')
  </script>
```

注意：计算属性看起来像是一个函数，但是我们在使用的时候不需要加()，这个后面讲setter和getter时会讲到；我们会发现无论是直观上，还是效果上计算属性都是更好的选择;并且计算属性是有**缓存的**；



#### 计算属性 vs methods

在上面的实现思路中，我们会发现计算属性和methods的实现看起来是差别是不大的，而且我们多次提到计算属 性**有缓存的**

接下来我们来看一下同一个计算多次使用，计算属性和methods的差异：

```html
<div id="app"></div>
  <template id="my-app">
    <button @click="firstName = '毛毛'">修改firstName</button>
    <!-- 表面上 计算属性和方法所达到的效果的确是一样的 -->
    <!-- 但是当我们多次调用计算属性的时候，发现计算属性只被调用一次，因为默认情况下是有缓存的 -->
    <!-- 计算属性会随着数据（也就是data里面我们计算属性用到的那部分数据）的改变，而发生改变 -->
    <h2>{{fullName}}</h2>
    <h2>{{fullName}}</h2>
    <h2>{{fullName}}</h2>
    <h2>{{getFullName()}}</h2>
    <h2>{{getFullName()}}</h2>
    <h2>{{getFullName()}}</h2>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          firstName: '毛',
          lastName: '俊'
        }
      },
      // 计算属性  计算属性在多次使用时，如果数据没有发生改变，是会重复使用上次的数据的
      computed: {
        // 定义一个计算属性叫做 fullName
        // 虽然计算属性看起来和函数很相似，但是本质上计算属性其实就是一个属性
        fullName() {
          console.log(1);
          return this.firstName + this.lastName
        }
      },
      methods: {
        getFullName() {
          console.log(2);
          return this.firstName + this.lastName
        }
      },
    }).mount('#app')
  </script>
```

![image-20210722153008321](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210722153008321.png)

![image-20210722153036987](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210722153036987.png)



#### 计算属性的缓存

这是什么原因呢？

1. 这是因为计算属性会基于它们的依赖关系进行缓存；

2. 在数据不发生变化时，计算属性是不需要重新计算的;

3. 但是如果依赖的数据发生变化，在使用时，计算属性依然会重新进行计算；



![image-20210722153259949](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210722153259949.png)

#### 计算属性的setter和getter

计算属性在大多数情况下，只需要一个getter方法即可，所以我们会将计算属性**直接写成一个函数**(语法糖)。

但是，如果我们确实想设置计算属性的值呢？

这个时候我们也可以给计算属性设置一个setter的方法；

```js
computed: {
        // 直接书写一个计算属性，实际上是简写，调用的是计算属性的getter方法
        // fullName() {
        //   return this.firstName + this.lastName
        // }
        // 计算属性的完整写法
        fullName:{
          get:function(){
            return this.firstName + this.lastName
          },
          set:function(newVal){
            console.log(newVal);
            this.firstName = newVal.split('')[0];
            this.lastName = newVal.split('')[1];
          }
        }
      }
```

#### 源码如何对setter和getter处理呢？

你可能觉得很奇怪，Vue内部是如何对我们传入的是一个getter，还是说是一个包含setter和getter的对象进行处 理的呢？

事实上非常的简单，Vue源码内部只是做了一个逻辑判断而已；

![image-20210722153542978](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210722153542978.png)

### 认识侦听器watch

**什么是侦听器呢**?

开发中我们在data返回的对象中定义了数据，这个数据通过插值语法等方式绑定到template中；

当数据变化时，template会自动进行更新来显示最新的数据；

但是在某些情况下，我们希望在代码逻辑中监听某个数据的变化，这个时候就需要用侦听器watch来完成了；

侦听器的用法如下：

**选项**：watch

**类型**：{ [key: string]: string | Function | Object | Array}

#### 侦听器案例

举个栗子（例子）：

比如现在我们希望用户在input中输入一个问题；

每当用户输入了最新的内容，我们就获取到最新的内容，并且使用该问题去服务器查询答案；

那么，我们就需要实时的去获取最新的数据变化；

![image-20210722153938057](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210722153938057.png)

![image-20210722153949432](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210722153949432.png)

![image-20210722154322714](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210722154322714.png)

```html
<div id="app"></div>
  <template id="my-app">
    <label for="question">
      请输入问题：
      <input id="question" type="text" v-model="question">
    </label>

  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          question: 'hello Vue3!'
        }
      },
      // 侦听器  侦听某个属性（数据）的变化，并进行一些逻辑的处理
      // 在实际工作时：我们侦听器一般不会监听计算属性，一般来说都是侦听元数据（data）
      watch: {
        // 第一个参数的变化后的新值
        // 第二个参数是变化前的旧值
        question(newVal, oldVal) {
          console.log(newVal,oldVal);
          this.getAnswer(newVal);
        }
      },
      methods: {
        getAnswer(question) {
          console.log(`${question}问题答案是:哈哈哈哈哈`)
        }
      },
    }).mount('#app')
  </script>
```

#### 侦听器watch的配置选项

我们先来看一个例子：

当我们点击按钮的时候会修改info.name的值；

这个时候我们使用watch来侦听info，可以侦听到吗？答案是不可以。

这是因为默认情况下，watch只是在侦听info的引用变化，对于内部属性的变化是不会做出响应的：

这个时候我们可以使用一个选项**deep**进行更深层的侦听；

注意前面我们说过watch里面侦听的属性对应的也可以是一个Object；

还有另外一个属性，是希望一开始的就会立即执行一次：

这个时候我们使用**immediate选项；**

这个时候无论后面数据是否有变化，侦听的函数都会有限执行一次；

![image-20210722154612049](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210722154612049.png)

```html
<div id="app"></div>
  <template id="my-app">
    <button @click="info.gender = '男'">增加info的gender属性</button>
    <button @click="info.name = '哈哈哈'">改变info的name属性</button>
    <h2 v-for="item of info">{{item}}</h2>

  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          info: {
            name: '毛毛',
            age: 22
          }
        }
      },
      watch: {
        // 直接监听这个info对象，发现属性发生改变了也不会触发
        // 特点： 默认情况下只能监听整个对象是否发生改变（也就是是不是换成一个新对象了）
        // 1. 如果直接将对象info直接重新赋值为一个新的对象时，可以直接侦听到
        // 2. 无法直接侦听到对象内部属性的改变
        // info(newVal, oldVal) {
        //   console.log(newVal, oldVal);
        // }
        // 深度侦听：
        // info: {
        //   // 开启深度侦听，可以侦听到对象内部属性的变化了
        //   deep: true, // 即使对象的属性还是一个对象，仍然还是可以侦听到
        //   handler(newVal, oldVal) {
        //     console.log(newVal, oldVal)
        //   },
        //   // 侦听器的立即执行
        //   immediate: true // 在页面刚加载的时候，数据很明显是没有发生变化的（旧的值相当于不存在，也就是undefined），但是我们仍然想执行一遍侦听器的代码逻辑，就可以让 immediate属性为true
        // }

        // 字符串方法名 侦听器的方法也可以放在methods里面，然后通过字符串的方式赋值，
        // info: "changeInfo" // 实际上用的不多

        // 也可以使用回调数组 会被逐一调用
        info: [
          "changeInfo", // 字符串方法名
          function handler2(newVal, oldVal) {
            console.log(newVal, oldVal, 'handle2');
          },
          {
            handler: function handle3(newVal, oldVal) {
              console.log(newVal, oldVal, 'handle3');
            }
          }
        ]
      },
      methods: {
        changeInfo() {

        }
      },
    }).mount('#app')
  </script>
```

#### 侦听器watch的其他方式

1. 字符串方法名

   ```js
   info: "changeInfo" // 实际上用的不多
   ```

2. 回调数组的方式

   ```js
   // 也可以使用回调数组 会被逐一调用
           info: [
             "changeInfo", // 字符串方法名
             function handler2(newVal, oldVal) {
               console.log(newVal, oldVal, 'handle2');
             },
             {
               handler: function handle3(newVal, oldVal) {
                 console.log(newVal, oldVal, 'handle3');
               }
             }
           ]
   ```

3. 另外一个是Vue3文档中没有提到的，但是Vue2文档中有提到的是侦听对象的属性：

   ![image-20210722154814869](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210722154814869.png)

   ```js
   watch: {
           // 直接侦听对象内的某个属性
           // 发现只有对象的这个顺序发生改变时，才会被侦听到
           // 当然，如果整个对象都直接被替换了，也一样是会被侦听的
           "info.name": function (newVal, oldVal) {
             console.log(newVal, oldVal);
           }
         }
   ```

   4. 还有另外一种方式就是使用 $watch 的API：

      我们可以在created的生命周期（后续会讲到）中，使用 this.$watchs 来侦听；

      1. 第一个参数是要侦听的源
      2. 第二个参数是侦听的回调函数callback；
      3. 第三个参数是额外的其他选项，比如deep、immediate；

      ```js
      // 生命周期函数 created 在组件被创建的时候执行（后面会讲）
            created() {
              // 使用当前组件的$watch方法来创建侦听器
              // 参数一：侦听的对象 
              // 参数二：回调函数
              // 参数三：侦听器的配置选项（对象形式） 
              // $watch() 的返回值 unwatch 是一个函数
              // 调用这个返回值函数，可以取消侦听器，而直接定义在watch里面的侦听器一般是没办法取消的
              const unwatch = this.$watch('info', (newVal, oldVal) => {
                console.log(newVal, oldVal);
              }, {
                deep: true,
                immediate: true
              });
              // unwatch() // 调用后 侦听器失效
            }
      ```

      

      

### 综合案例

现在我们来做一个相对综合一点的练习：**书籍购物车**

![image-20210722155110498](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210722155110498.png)

案例说明：

1. 在界面上以表格的形式，显示一些书籍的数据；
2. 在底部显示书籍的总价格;
3. 点击+或者-可以增加或减少书籍数量（如果为1，那么不能继续-）；
4. 点击移除按钮，可以将书籍移除（当所有的书籍移除完毕时，显示：购物车为空~）

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    table {
      border: 1px solid #e9e9e9;
      border-collapse: collapse;
      border-spacing: 0;
    }

    th,
    td {
      padding: 8px 16px;
      border: 1px solid #e9e9e9;
      text-align: left;
    }

    th {
      background-color: #f7f7f7;
      color: #5c6b77;
      font-weight: 600;
    }

    .counter {
      margin: 0 5px;
    }
  </style>
</head>

<body>
  <div id="app"></div>
  <template id="my-app">
    <template v-if="books.length>0">
      <table>
        <thead>
          <th>序号</th>
          <th>书籍名称</th>
          <th>出版日期</th>
          <th>价格</th>
          <th>购买数量</th>
          <th>操作</th>
        </thead>
        <tbody>
          <tr v-for="(book, index) in books">
            <td>{{index + 1}}</td>
            <td>{{book.name}}</td>
            <td>{{book.date}}</td>
            <td>{{formatPrice(book.price)}}</td>
            <td>
              <button :disabled="book.count <= 1" @click="decrement(index)">-</button>
              <span class="counter">{{book.count}}</span>
              <button @click="increment(index)">+</button>
            </td>
            <td>
              <button @click="removeBook(index)">移除</button>
            </td>
          </tr>
        </tbody>
      </table>
      <h2>总价格: {{formatPrice(totalPrice)}}</h2>
    </template>
    <template v-else>
      <h2>购物车为空！</h2>
    </template>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          books: [
            {
              id: 1,
              name: '《算法导论》',
              date: '2006-9',
              price: 85.00,
              count: 1
            },
            {
              id: 2,
              name: '《UNIX编程艺术》',
              date: '2006-2',
              price: 59.00,
              count: 1
            },
            {
              id: 3,
              name: '《编程珠玑》',
              date: '2008-10',
              price: 39.00,
              count: 1
            },
            {
              id: 4,
              name: '《代码大全》',
              date: '2006-3',
              price: 128.00,
              count: 1
            },
          ]
        }
      },
      computed: {
        // vue2 filter/map/reduce
        totalPrice() {
          let finalPrice = 0;
          for (let book of this.books) {
            finalPrice += book.count * book.price;
          }
          return finalPrice;
        },
        // Vue3不支持过滤器了, 推荐两种做法: 使用计算属性/使用全局的方法
        filterBooks() {
          const newBook = this.books.map(item => {
            // 生成新的对象
            const newItem = Object.assign({}, item);
            newItem.price = "¥" + item.price;
            return newItem;
          })
          return newBook
        }
      },
      methods: {
        increment(index) {
          // 通过索引值获取到对象
          this.books[index].count++
        },
        decrement(index) {
          this.books[index].count--
        },
        removeBook(index) {
          this.books.splice(index, 1);
        },
        formatPrice(price) {
          return "¥" + price;
        }
      }
    }).mount('#app')
  </script>
</body>

</html>
```





# Vue3的表单和开发模式

## 侦听器的深度侦听问题

上次，我们学习了关于侦听器的相关语法以及侦听器的使用。

但是：我们发现，在深度侦听一个对象的时候，如果一个对象的某个属性发生改变，我们打印出新值和旧值是一样的。

![image-20210723195047191](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210723195047191.png)



![image-20210723195115106](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210723195115106.png)

```html
<div id="app"></div>
    <template id="my-app">
      <button @click="info.name = '毛毛1'">改变name</button>
      <h2 v-for="item in info">{{item}}</h2>
    </template>
    <script src="../vue3/vue3.js"></script>
    <script>
      Vue.createApp({
        template:'#my-app',
        data(){
          return {
            info:{
              name:'毛毛',
              age : 22
            }
          }
        },
        watch:{
          info:{
            deep:true,
            handler(newVal,oldVal){
              console.log(newVal,oldVal);
            }
          }
        }
      }).mount('#app')
    </script>
```

发生这种情况的原因是什么呢？

其实是因为新值newVal和旧值oldVal保存的都是这个侦听的对象的引用。vue并不会深拷贝这个对象。所以导致属性发生改变，打印出来的结果是相同的。

这个在Vue3的官网也是有所说明的。

![image-20210723195853965](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210723195853965.png)

## 深拷贝和浅拷贝(补充知识)

```js
    // 1. 对象的引用赋值
    const info = { // info本质上保存的是这个对象的内存地址（堆）
      name: '毛毛',
      age: 22
    }
    const obj = info; // 将info保存的地址给了obj，二者指向同一块内存地址
    obj.name = 'obj';
    console.log(info.name); // obj 证明了二者的确指向同一块内存地址，也就是指向同一个对象

    // 2. 对象的浅拷贝
    const info2 = {
      name: '毛毛',
      age: 22
    }
    // 将这个对象的属性拷贝一份，放到一个空对象里面，然后返回
    const obj2 = Object.assign({}, info2);
    info2.name = 'info2';
    console.log(obj2.name); // 毛毛 可见对象的确被复制了一份，二者不是同一个对象了

    const info3 = {
      name: '毛毛',
      age: 22,
      friend: {
        name: 'aaa',
        age: 222
      } // 属性 friend是一个对象类型，所以保存的也是该对象的内存地址
    }
    const obj3 = Object.assign({}, info3); // 此时仍然进行复制
    obj3.friend.name = 'bbb';
    // 证明了复杂数据类型（数组，对象等）拷贝过去的其实也是内存地址，二者（friend属性）指向的还是同一个对象
    console.log(info3.friend.name); // bbb 可以证明两个对象的friend属性指向的是同一个对象

    // 3. 对象的深拷贝
    const info4 = {
      name: '毛毛',
      age: 22,
      friend: {
        name: 'aaa',
        age: 222
      }
    }
    // 对象的深拷贝可以借助JSON对象提供两个方法
    // 先把需要深拷贝的对象转换为JSON字符串，然后把把JSON字符串还原为对象
    const obj4 = JSON.parse(JSON.stringify(info4));
    info4.friend.name = 'bbb';
    console.log(obj4.friend.name); // aaa 输出的值还是 aaa 所以friend属性不指向同一个对象了

```

**注意：**使用JSON来完成对象的深拷贝还是有问题的。比如遇到UNdefined，函数等。没办法完成深拷贝。

## v-model双向绑定

### v-model的基本使用

表**单提交**是开发中非常常见的功能，也是和用户交互的重要手段：

比如用户在**登录、注册**时需要提交账号密码；

比如用户在**检索、创建、更新**信息时，需要提交一些数据；

这些都要求我们可以在代码逻辑中获取到用户提交的数据，我们通常会使用**v-model**指令来完成：

1. v-model指令可以在表单 input、textarea以及select元素上创建双向数据绑定；
2. 它会根据控件类型自动选取正确的方法来更新元素； 
3. 尽管有些神奇，但 v-model 本质上不过是语法糖，它负责监听用户的输入事件来更新数据，并在某种极端场景 下进行一些特殊处理；

```html
    <input type="text" v-model="message">
    <h2>{{message}}</h2>
```

![image-20210724134141119](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724134141119.png)



### v-model的原理

官方有说到，**v-model的原**理其实是背后有两个操作：

1. v-bind绑定value属性的值；
2. v-on绑定input事件监听到函数中，函数会获取最新的值赋值到绑定的属性中；

![image-20210724134521848](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724134521848.png)

![image-20210724134529824](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724134529824.png)

```html
<div id="app"></div>
  <template id="my-app">
    <!-- 输入框的值发生改变，则让下面显示的message也发生改变 -->
    <!-- 所以我们可以监听输入框的输入事件，输入的内容发生了改变，就让message发生改变 -->
    <input type="text" v-bind:value="message" @input="message = $event.target.value">
    <h2>{{message}}</h2>
    <!-- 想要完成上面的效果，需要使用v-bind和v-on两种指令，比较麻烦， -->
    <!-- 而且在输入框等地方，我们一般都会进行这种双向的和数据进行绑定 -->
    <!-- 所以vue给我们提供了一个新的指令 v-model，简化了双向数据绑定 -->
    <!-- 实际上达到的效果和上面一样，但是写法更简单，也可以看成上面的语法糖 -->
    <input type="text" v-model="message">
    <h2>{{message}}</h2>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!'
        }
      }
    }).mount('#app')
  </script>
```

### v-model绑定其他表单

1. 绑定textarea

   ![image-20210724135148198](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724135148198.png)

   ```html
   <!-- v-model绑定textarea -->
   <label for="intro">
       <textarea id="intro" v-model="intro"></textarea>
   </label>
   <h3>{{intro}}</h3>
   ```

2. 绑定复选框checkbox

   ![image-20210724135216810](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724135216810.png)

   我们来看一下v-model绑定checkbox：单个勾选框和多个勾选框:

   **单个勾选框：** 

   1. v-model即为布尔值。 
   2. 此时input的value并不影响v-model的值。

   **多个复选框：** 

   1. 当是多个复选框时，因为可以选中多个，所以对应的data中属性是一个数组。 
   2. 当选中某一个时，就会将input的value添加到数组中。

   ```html
   <!-- 绑定checkbox -->
   爱好：  
   吃饭：<input v-model="hobbies" type="checkbox" name="hobbies" value="吃饭">
   睡觉：<input v-model="hobbies" type="checkbox" name="hobbies" value="睡觉">
   打豆豆：<input v-model="hobbies" type="checkbox" name="hobbies" value="打豆豆"><br>
   
   您选择的爱好：
   <h3 v-for="hobby of hobbies">{{hobby}}</h3>
   ```

3. 绑定单选框radio

   v-model绑定radio，用于选择其中一项；

   ![image-20210724135234538](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724135234538.png)

   ```html
   <!-- 绑定 radio -->
   男：<input v-model="gender" type="radio" name="gender" value="男">
   女：<input v-model="gender" type="radio" name="gender" value="女">
   
   ```

4. 绑定下拉选择框select

   **和checkbox一样，select也分单选和多选两种情况。**

   **单选：**只能选中一个值 

   1. v-model绑定的是一个值； 
   2. 当我们选中option中的一个时，会将它对应的value赋值到fruit中； 

   **多选：**可以选中多个值 

   1. v-model绑定的是一个数组； 

   2. 当选中多个值时，就会将选中的option对应的value添加到数组fruit中；

   ![image-20210724135244489](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724135244489.png)

   ```html
      
   <!-- 下拉选择框 select -->
   课程：
   <select v-model="courses" multiple>
       <!-- <option value="请选择课程" selected>请选择课程</option> -->
       <option value="语文">语文</option>
       <option value="英语">英语</option>
       <option value="高数">高数</option>
       <option value="线代">线代</option>
       <option value="概率论">概率论</option>
   </select>
   ```

```html
<div id="app"></div>
    <template id="my-app">
      <!-- v-model绑定textarea -->
      <label for="intro">
        <textarea id="intro" v-model="intro"></textarea>
      </label>
      <h3>{{intro}}</h3>

      <!-- 绑定checkbox -->
      爱好：  
      吃饭：<input v-model="hobbies" type="checkbox" name="hobbies" value="吃饭">
      睡觉：<input v-model="hobbies" type="checkbox" name="hobbies" value="睡觉">
      打豆豆：<input v-model="hobbies" type="checkbox" name="hobbies" value="打豆豆"><br>

      您选择的爱好：
      <h3 v-for="hobby of hobbies">{{hobby}}</h3>

      <!-- 绑定 radio -->
      男：<input v-model="gender" type="radio" name="gender" value="男">
      女：<input v-model="gender" type="radio" name="gender" value="女">
    
    <!-- 下拉选择框 select -->
    课程：
    <select v-model="courses" multiple>
      <!-- <option value="请选择课程" selected>请选择课程</option> -->
      <option value="语文">语文</option>
      <option value="英语">英语</option>
      <option value="高数">高数</option>
      <option value="线代">线代</option>
      <option value="概率论">概率论</option>
    </select>
    </template>

    <script src="../vue3/vue3.js"></script>
    <script>
      Vue.createApp({
        template:'#my-app',
        data(){
          return {
            intro:'hello Vue3!',
            gender:'男',
            hobbies:['吃饭'],
            courses:['高数','线代']
          }
        },
        watch:{
          gender(newVal,oldVal){
            console.log(newVal,oldVal);
          }
        }
      }).mount('#app')
    </script>
```



### v-model的值绑定

目前我们在前面的案例中大部分的值都是在template中固定好的： 

1. 比如gender的两个输入框值male、female； 
2. 比如hobbies的三个输入框值basketball、football、tennis； 

在真实开发中，我们的数据可能是来自服务器的，那么我们就可以先将值请求下来，绑定到data返回的对象中， 再通过v-bind来进行值的绑定，这个过程就是值绑定。 

这里不再给出具体的做法，因为还是v-bind的使用过程.

### v-model修饰符 - lazy

lazy修饰符是什么作用呢？ 

1. 默认情况下，v-model在进行双向绑定时，绑定的是input事件，那么会在每次内容输入后就将最新的值和绑定 的属性进行同步； 
2. 如果我们在v-model后跟上lazy修饰符，那么会将绑定的事件切换为 change 事件，只有在提交时（比如回车） 才会触发；

```html
    <!-- v-model 默认双向绑定数据，触发的事件是input事件，一旦输入值就会触发 -->
    <!-- 我们可以通过添加事件修饰符 -->
    <!-- lazy 修饰符 输入框失去焦点后，或者用户输入回车键后 才会触发 -->
    <input type="text" v-model.lazy="message"><br>
    <h2>{{message}}</h2>
```

### v-model修饰符 - number

我们先来看一下v-model绑定后的值是什么类型的： 

message总是string类型，即使在我们设置type为number也是string类型；

![image-20210724135844140](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724135844140.png)

![image-20210724140008476](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724140008476.png)

如果我们希望转换为数字类型，那么可以使用 .number 修饰符：

```html
    <!-- input输入框，里面的值一直都是字符串类型，即使把type的值改为number -->
    <!-- 也只是禁用了键盘上的abc等字符，并不是说转为了数字。本质还是数字 -->
    <!-- number 修饰符，将字符串转换为数字 -->
    <input type="text" v-model.number="num"><br>
    <h2>num的类型：{{typeof num}}</h2>
```

![image-20210724140018916](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724140018916.png)

另外，在我们进行逻辑判断时，如果是一个string类型，在可以转化的情况下会进行隐式转换的：下面的score在进行判断的过程中会进行隐式转化的；

![image-20210724135923568](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724135923568.png)

### v-model修饰符 - trim

如果要自动过滤用户输入的首尾空白字符，可以给v-model添加 trim 修饰符：

```html
    <!-- trim修饰符 会删除输入框里面的开头和结尾的空白字符（\n,\r,\t等） -->
    没有trim修饰符：<input type="text" v-model="trim">
    有trim修饰符：<input type="text" v-model.trim="trim">
    <h2> ---{{trim}}---</h2>
```

![image-20210724140110976](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724140110976.png)

![image-20210724140134288](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724140134288.png)

![image-20210724140139720](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724140139720.png)

### v-mode组件上使用

v-model也可以使用在组件上，Vue2版本和Vue3版本有一些区别。 具体的使用方法，后面讲组件化开发再具体学习.



## 人处理问题的方式

**人面对复杂问题的处理方式：**

1. 任何一个人处理信息的逻辑能力都是有限的
2. 所以，当面对一个非常复杂的问题时，我们不太可能一次性搞定一大堆的内容。 
3. 但是，我们人有一种天生的能力，就是将问题进行拆解。 
4. 如果将一个复杂的问题，拆分成很多个可以处理的小问题，再将其放在整体当中，你会发现大的问题也会迎刃而解

![image-20210724140314802](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724140314802.png)





## 认识组件化开发

**组件化也是类似的思想：** 

1. 如果我们将一个页面中所有的处理逻辑 全部放在一起，处理起来就会变得非常 复杂，而且**不利于后续的管理以及扩展**； 
2. 但如果，我们讲一个页面拆分成一**个个 小的功能块，**每个功能块完成属于自己 这部分独立的功能，那么之后**整个页面 的管理和维护**就变得非常容易了； 
3. 如果我们将一个个**功能块拆分**后，就可 以像搭建积木一下来搭建我们的项目；

![image-20210724140432277](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724140432277.png)

## 组件化开发

现在可以说整个的大前端开发都是组件化的天下，无论从三大框架（Vue、React、Angular），还是跨平台方案 的Flutter，甚至是移动端都在转向组件化开发，包括小程序的开发也是采用组件化开发的思想。 

所以，学习组件化最重要的是它的思想，每个框架或者平台可能实现方法不同，但是思想都是一样的。 

**我们需要通过组件化的思想来思考整个应用程序：** 

1. 我们将一个完整的页面分成很多个组件； 
2. 每个组件都用于实现页面的一个功能块； 
3. 而每一个组件又可以进行细分； 
4. 而组件本身又可以在多个地方进行复用；

### Vue的组件化

**组件化是Vue、React、Angular的核心思想**

1. 前面我们的createApp函数传入了一个对象App，这个对象其实本质上就是一个组件，也是我们应用程序的根 组件；
2. 组件化提供了一种抽象，让我们可以开发出一个个独立可复用的小组件来构造我们的应用； 
3. 任何的应用都会被抽象成一颗组件树；

![image-20210724140639619](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724140639619.png)

接下来，我们来学习一下**在Vue中如何注册一个组件**，以及之后如何使用这个注册后的组件

## 注册组件的方式

如果我们现在有一部分内容（模板、逻辑等），我们希望将这部分内容抽取到一个独立的组件中去维护，这个时候 如何注册一个组件呢

我们先从简单的开始谈起，比如下面的模板希望抽离到一个单独的组件：

![image-20210724140744568](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724140744568.png)

**注册组件分成两种：**

1. **全局组件**：在任何其他的组件中都可以使用的组件； 
2. **局部组件**：只有在注册的组件中才能使用的组件；

### 注册全局组件

我们先来学习一下全局组件的注册：

1. 全局组件需要使用我们全局创建的app来注册组件； 
2. 通过component方法传入组件名称、组件对象即可注册一个全局组件了； 
3. 之后，我们可以在App组件的template中直接使用这个全局组件：

![image-20210724140904598](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724140904598.png)

![image-20210724140913142](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724140913142.png)

### 全局组件的逻辑

当然，我们组件本身也可以有自己的代码逻辑：

**比如自己的data、computed、methods等等**

![image-20210724140959991](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724140959991.png)

```html
<div id="app"></div>
  <template id="my-app">
    <!-- 使用我们自定义的全局组件，就和使用html标签一样。把组件名称当做标签来用 -->
    <cpt-mao></cpt-mao>
    <cpt-m/>
  </template>

  <!-- 自定义组件模板 -->
  <template id="cpt-m">
    <!-- vue3在模板里面是可以有多个根标签了，而不需要在外面加上一个div进行包裹了 -->
    <h2>我是组件模板 222</h2>
    <h2>我是第二个定义的自定义组件</h2>
    <h2>{{info}}</h2>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    const app = Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!'
        }
      }
    });
    // 使用app注册一个全局组件
    // component(组件名称 ， 组件对象)
    app.component('cpt-mao', {
      template: '<h2>哈哈哈 我是全局组件！</h2>'
    });
    // 注册的全局组件，意味着这个注册的组件在任何的组件模板中都可以使用
    // 组件可以有自己数据，方法等
    app.component('cpt-m', {
      template: '#cpt-m',
      data() {
        return {
          info: '我是组件内部自己的数据'
        }
      }
    })
    app.mount('#app')
  </script>
```

### 组件的名称

在通过app.component注册一个组件的时候，第一个参数是组件的名称，定义组件名的方式有两种:

**方式一：使用kebab-case（短横线分割符）**

当使用` kebab-case` (短横线分隔命名) 定义一个组件时，你也必须在引用这个自定义元素时使用 `kebab-case`， 例如 ；

![image-20210724141133275](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724141133275.png)

**方式二：使用PascalCase（驼峰标识符）**

当使用 `PascalCase` (首字母大写命名) 定义一个组件时，你在引用这个自定义元素时**两种命名法**都可以使用。也 就是说  和  都是可接受的

![image-20210724141209513](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724141209513.png)



### 注册局部组件

全局组件往往是在应用程序一开始就会全局组件完成，那么就意味着如果某些组件我们并没有用到，也会一起被注 册： 

1. 比如我们注册了三个全局组件：ComponentA、ComponentB、ComponentC； 
2. 在开发中我们只使用了ComponentA、ComponentB，如果ComponentC没有用到但是我们依然在全局进行 了注册，那么就意味着类似于webpack这种打包工具在打包我们的项目时，我们依然会对其进行打包； 
3. 这样最终打包出的JavaScript包就会有关于ComponentC的内容，用户在下载对应的JavaScript时也会增加包 的大小；

**所以在开发中我们通常使用组件的时候采用的都是局部注册**：

1. 局部注册是在我们需要使用到的组件中，通过components属性选项来进行注册； 
2. 比如之前的App组件中，我们有data、computed、methods等选项了，事实上还可以有一个components选 项； 
3. 该components选项对应的是一个对象，对象中的键值对是 组件的名称: 组件对象

![image-20210724141353292](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210724141353292.png)

```html
<div id="app"></div>
  <template id="my-app">
    <cpt-mao></cpt-mao>
  </template>


  <template id="cpt-mao">
    <h2>我是局部组件 哈哈！</h2>
    <h2>{{mes}}</h2>
  </template>
  <script src="../vue3/vue3.js"></script>
  <script>
    Vue.createApp({
      template: '#my-app',
      data() {
        return {
          message: 'hello Vue3!'
        }
      },
      // 局部组件的注册 在属性components里面进行注册
      components: {
        
        // key是组件名称，value是组件对象
        'cpt-mao': {
          template: '#cpt-mao',
          data() {
            return {
              mes: '我是局部组件自己的数据'
            }
          }
        }
      }
    }).mount('#app')
  </script>
```

## Vue的开发模式

目前我们使用vue的过程都是在html文件中，通过template编写自己的模板、脚本逻辑、样式等。 

**但是随着项目越来越复杂，我们会采用组件化的方式来进行开发**

1. 这就意味着每个组件都会有自己的**模板、脚本逻辑、样式**等； 
2. 当然我们依然可以把它们**抽离**到单独的js、css文件中，但是它们还是会分离开来； 
3. 也包括我们的script是在一个全局的作用域下，很容易出现**命名冲突**的问题； 
4. 并且我们的代码为了适配一些浏览器，必须使用ES5的语法； 
5. 在我们编写代码完成之后，依然需要**通过工具对**代码进行构建、代码；

**所以在真实开发中**，我们可以通过一个**后缀名为 .vue** 的single-file components (单文件组件) 来解决，并且可 以使用webpack或者vite或者rollup等**构建工具**来对其进行处理





