# vue笔记

## vue基础

###  vue的基本使用

#### 1. 如何安装(下载)

```shell
npm i vue
# 也可以直接下载 vue.js 文件
```

#### 2. vue的基本使用步骤

1. 提供标签 用于填充数据
2. 导入js脚本文件
3. 使用vue的语法做功能
4. 把vue提供的数据填充到标签中 

```html
<script src="../../vue/vue.js"></script>
<div id="app">
    <!-- 插值表达式  -->
    <!-- 将数据填充到HTML标签中 -->
    <!-- 支持基本的计算操作 -->
    <h1>{{message}}</h1>
  </div>
<script>
    // vue基本使用步骤
    /* 
    1. 提供标签 用于填充数据
    2. 导入js脚本文件
    3. 使用vue的语法做功能
    4. 把vue提供的数据填充到标签中
    */
    const app = new Vue({
      el: "#app",//  元素的挂载位置  就是元素的关联位置 要把数据等渲染在哪里 可以是css选择器或者dom元素
      // data  模型数据 值是一个对象
      data: {
        message: "毛毛很帅"
      }
    });
  </script>
```

#### 3. 插值表达式

插值表达式的作用**将数据填充到HTML标签中**并且**支持基本的计算操作**。

语法格式：

```html
{{message}}
{{1+2}}
{{2 / 3}}
{{"11" + "22"}}  字符串的拼接
```

#### 4. vue代码运行原理分析

- 概述编译过程的概念（vue语法-> 原生语法）

![image-20210413230413687](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210413230413687.png)

### vue的模板语法

#### 1. 如何理解前端渲染

**把数据填充到HTML标签中**

![image-20210413230733847](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210413230733847.png)

#### 2. 前端的渲染方式

1. 原生js拼接字符串

2. 使用前端的模板引擎

   art-template 最快的模板引擎

3. **使用vue特有的模板语法**

   - 插值表达式
   - 指令
   - 事件绑定
   - 属性绑定
   - 样式绑定
   - 分支循环结果

### vue指令

#### 1. 什么是指令

- 什么是自定义属性  ----- 比如H5新增的data- 属性
- 指令的本质就是自定义属性
- 指令的格式： 以 v- 开始 (比如 v-click )

#### 2. v-clock指令的使用

- 插值表达式存在的问题： “闪动”

  网卡的时候会出现没渲染出数据的可能

- 如何解决该问题：  使用 v-cloak 指令
- 解决该问题的原理： 先隐藏，替换好值之后再显示最终的值

```shell
<div id="app" v-cloak>
    <h1>{{message}}</h1>
  </div>
  <script>
    // v-cloak指令的用法：
    /*
      1. 提供样式
        [v-cloak]{
          display:none;
        }
      2. 在插值表达式所在的标签中添加v-cloak指令

      背后的原理：
        先通过样进行隐藏内容  然后在内存中进行值的替换 替换好后在显示最终的值
    */
    const app = new Vue({
      el: "#app",
      data: {
        message:"哈哈"
      }
    })
  </script>
```

#### 3. 数据绑定相关的指令

- v-text 填充纯文本

   相比于插值表达式更加简洁

- v-html 填充html片段
  1. 存在安全问题
  2. 本网站内数据可以使用，来自第三方的数据不可以用

- v-pre 填充原始信息

  ​    显示原始信息，跳过编译过程

```html
<div id="app">
    <h1>{{msg1}}</h1>
    <h3 v-text="msg2"></h3>
    <div v-html="msg3"></div>
    <div v-pre>{{msg}}</div>
  </div>
  <script>
    const app = new Vue({
      el:"#app",
      data:{
        msg:"哈哈",
        msg2:"<h1>v-text</h1>",
        msg3:"<h1>哈哈哈html</h1>"
      }
    });
  </script>
```

#### 4. 数据响应式

- 如何理解数据响应式
  - html5中的响应式(屏幕尺寸的变化导致样式的变化)
  - 数据的响应式（数据的变化导致页面的变化）
- 什么是数据绑定
  - 数据绑定： 将数据填充到标签中
- v-once 指令 只编译一次
  - 显示内容之后不在具有响应式

```html
<div id="app">
    <div>{{msg}}</div>
    <!-- 如果显示的信息 后续不需要再次修改 -->
    <!-- 可以使用 v-once指令 提高性能 -->
    <div v-once>{{msg}}</div>
  </div>
  <script>
    const app = new Vue({
      el: "#app",
      data: {
        msg: "毛毛"
      }
    });
    // 发现绑定了 v-once指令的标签 里面的值只会渲染一次
    setTimeout(() => {
      app.msg = "111";
    }, 3000);
  </script>
```



#### 5. 双向数据绑定

##### 5.1 什么是双向数据绑定

![image-20210414134839530](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210414134839530.png)

##### 5.2 双向数据绑定分析

使用 v-model 指令

```html
<input type="text" v-model="uname" />

<div id="app">
    <div>{{msg}}</div>
    <!-- 如果显示的信息 后续不需要再次修改 -->
    <!-- 可以使用 v-once指令 提高性能 -->
    <div v-once>{{msg}}</div>
    <!-- 会发现输入框里面的值发生改变 与之绑定的数据变量里面的数据也会发生改变 -->
    <input type="text" v-model="msg">
  </div>
  <script>
    const app = new Vue({
      el: "#app",
      data: {
        msg: "毛毛"
      }
    });
    // 发现绑定了 v-once指令的标签 里面的值只会渲染一次
    setTimeout(() => {
      app.msg = "111";
    }, 3000);
  </script>
```

##### 5.3 MVVM 设计思想

- M(model) 数据  模型

- V(view) 视图

- VM(view-Model)  实现控制逻辑。  

   视图到模型： 事件监听的方式

  模型到视图： 数据绑定

![image-20210414135138117](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210414135138117.png)

#### 6. 事件绑定

##### 6.1 Vue如何处理事件 

- v-on: 指令

```html
<input type="button" v-on:click="num++"/> 
```

- v-on:  简写形式（语法糖）

```html
<input type="button" @click="num++"/> 
```

##### 6.2 事件绑定的调用方式

- 直接绑定函数名称

  `<input type="button" v-on:click="add" value="加" />`

- 调用函数

  `<input type="button" @click="mul()" value="减" />`

<div id="app">
    <div>{{num}}</div>
    <br>
    <input type="button" v-on:click="num++" value="加" />
    <input type="button" v-on:click="add" value="加" />
    <!-- 绑定点击事件的简写形式 -->
    <input type="button" @click="num--" value="减" />
    <input type="button" @click="mul()" value="减" />
  </div>
  <script>
    const app = new Vue({
      el: "#app",
      data: {
        msg: "毛毛",
        num: 0,
      },
      // 定义方法
      methods: {
        add(){
          // 在方法内部访问其属性需要使用 this进行访问
          this.num++;
        },
        mul(){
          this.num--;
        }
      }
    });
  </script>

##### 6.3 事件函数参数传递

- 普通参数和事件对象

  ```html
  <button @click="say('hi',$event)"> aaa</button>
  $event 就是事件对象
  ```

  ```html
  <input type="button" @click="say('哈哈',$event)" value="say" />
  
    *<!-- 事件绑定函数的名称 默认传递事件对象作为函数的第一个参数（第一个形参） -->*
  
    *<!-- 直接将函数进行绑定而没有使用函数的调用（也就是括号）默认会自动传递一个参数 $event -->*
  
    *<!-- 如果事件绑定函数调用 那么事件对象必须作为最后一个参数进行显示传递 并且事件的对象名称必须是 $event -->*
  
    <input type="button" @click="hh" value="hh" />
  
  
  ```

  ```js
  // 定义方法
        methods: {
          add() {
            // 在方法内部访问其属性需要使用 this进行访问
            this.num++;
          },
          mul() {
            this.num--;
          },
          say(str, e) {
            console.log(str);
            console.log(e.target);
          },
          hh(e) {
            console.log(e.target);
          }
        }
  ```

  ##### 6.4 事件修饰符

  - .stop 阻止冒泡
  - .prevent  阻止默认行为

```html
  <div id="app">
    <div>{{num}}</div>
    <div v-on:click="handle2">
      <!-- 同一个指令可以使用多个事件修饰符 -->
      <!-- 使用事件修饰符 阻止冒泡 v-on:click.stop -->
      <div v-on:click.stop="handle1">子元素</div>
      <!-- 阻止默认行为： 阻止跳转 @click.prevent -->
      <a href="http://www.baidu.com" @click.prevent="move">不会跳转到百度</a>
    </div>
  </div>
  <script>
    // 事件绑定  事件修饰符
    const app = new Vue({
      el: "#app",
      data: {
        num: 0
      },
      methods: {
        handle1(e){
          // 阻止冒泡
          // e.stopPropagation();
          this.num++;
        },
        handle2:function(){
          this.num++;
        },
        move(e){
          // e.preventDefault();
          
        }
      },
    })
  </script>
```

##### 6.5 按键修饰符 keyup 事件的

- .enter 回车键
- .delete 删除键

```html
<div id="app">
    <form>
      <div>
        <label for="username">用户名：</label>
        <!-- keyup.enter 按下回车键触发 执行该函数 -->
        <!-- keyup.delete  按下删除键触发该函数 后面绑定的函数 -->
        <input type="text"  @keyup.enter="submit" v-model="username" id="username" name="username"
          placeholder="请输入用户名" />
      </div>
      <div>
        <label for="password">密码：</label>
        <!-- 按下delete键 清空密码 -->
        <input type="text" v-on:keyup.delete="dele" v-model="password" id="password" name="password" placeholder="请输入密码" />
      </div>
      <div>

        <button id="submit" v-on:click.prevent>提交</button>
      </div>
    </form>
  </div>
  <script>
    /* 事件绑定 按键修饰符 */
    // 我们希望点击回车键表单也能提交
    const app = new Vue({
      el: "#app",
      data: {
        username: "",
        password: ""
      },
      methods: {
        submit(e) {
          // e.preventDefault();
          console.log("提交表单");
          console.log(this.username, this.password);
        },
        dele(e){
          this.password = '';
        }
      },
    });
  </script>
```

##### 6.6 自定义按键修饰符

**全局config.keyCodes**对象

```js
Vue.config.keyCodes.f1 = 112   // 每个按键的唯一标识
```

```html
<div id="app">
    <!-- <form action=""> -->
    <!-- 只有输入a/A的时候才有结果 -->
    <input type="text" v-on:keyup.65="handle" v-model="info">
    <!-- 自定义按键修饰符 -->
    <input type="text" v-on:keyup.aaa="handle" v-model="info">
    <!-- </form> -->
  </div>
  <script>
    // 自定义按键修饰符 
    // 规则:  m名字是自定义的 但是值必须要对应的keyCode的值
    Vue.config.keyCodes.aaa = 65;
    const app = new Vue({
      el: "#app",
      data: {
        info: ""
      },
      methods: {
        handle(e) {
          e.preventDefault();
          console.log(e.keyCode);
        }
      },
    })
  </script>
```

#### 7. 属性绑定

##### 7.1 Vue如何动态处理属性

- v-bind指令

  ```html
  <a v-bind:href="url"></a>
  <-- 简写形式 -->
  <a :href="url"></a>
  ```

  ```html
  <div id="app">
      <!-- 属性绑定 -->
      <!-- 动态绑定属性 -->
      <a v-bind:href="url">跳转</a>
    </div>
    <script>
      const app = new Vue({
        el: "#app",
        data: {
          url: 'http://www.baidu.com'
        }
      });
    </script>
  ```

  

##### 7.2 v-model的原理分析

```html
<input v-bind:value="message" @input="message=$event.target.value"/>
```

```html
<div id="app">
  <h1 v-text="message"></h1>
  <!-- input事件 当输入框的值发生改变时触发 $event.target 触发该事件的目标 获取其对应的value -->
  <input v-bind:value="message" @input="message=$event.target.value"/>
</div>
<script src="../../vue/vue.js"></script>
<script>
  const app = new Vue({
    el: "#app",
    data: {
      message: "哈哈"
    }
  });
</script>
```

##### 7.3 样式绑定

###### 7.3.1 class样式处理

- 对象语法

  ```html
  <div v-bind:class="{active:isActive}"></div>
  ```

- 数组语法

  ```html
  <div :class="[activeClass,errorClass]"></div>
  ```

  

```html
 <style>
      .active {
          color: #aca;
      }
      .Red{
          color:red;
      }
  </style>
</head>
<body>
<div id="app">
  <!--  样式绑定 1. 对象形式-->
  <h2 :class="{active:isActive}">{{message}}</h2>
  <button v-on:click="toggle">样式切换</button>
  <!--  数组语法形式-->
  <h1 v-bind:class="[activeClass,Red]">{{message}}</h1>
</div>
<script src="../../vue/vue.js"></script>
<script>
  const app = new Vue({
    el: "#app",
    data: {
      message: "哈哈",
      isActive: true,
      activeClass:"active",
      Red:"Red"
    },
    methods: {
      toggle() {
        this.isActive = !this.isActive;
      }
    }
  });
</script>
```

###### 7.3.2 样式绑定相关语法细节：

1. 对象绑定和数组绑定可以结合使用

2. class绑定的值可以简化操作

   ```html
   <input type="text" v-bind:class="arrClass"/>
   <input type="text" v-bind:class="objClass"/>
   <script>
   new Vue({
       el:"#app",
       data:{
           arrClass:["cla1","cla2"],
           objClass:{
               active:true,
               error:false
           }
       }
   })
   </script>
   ```

   

3. 默认的class如何处理？

   默认的class会保留  不会被覆盖的。

###### 7.3 style样式处理

- 对象语法

  ```html
  <div v-bind:style="{color:activeColor,fontSize:fontSize}">
      111
  </div>
  ```

  

- 数组语法

```html
<div v-bind:style="[abseStyle,overrideStyle]">
    111
</div>
```

```html
<div id="app">
  <!--  样式绑定 1. 对象形式-->
  <h1 :style="{color:baseColor,fontSize:fontSize}">{{message}}</h1>
  <h1 :style="objColor">{{message}}</h1>
  <!--  数组形式 数组形式的每一个元素的值都是一个对象-->
  <div :style="[color1,color2]">{{message}}</div>
</div>
<script src="../../vue/vue.js"></script>
<script>
  const app = new Vue({
    el: "#app",
    data: {
      message: "哈哈",
      fontSize: "55px",
      baseColor: "red",
      color1: {fontSize: "22px"},
      color2: {color: "#aca",background:"skyblue"},
      objColor: {
        background: "#aca",
      }

    },
    methods: {}
  });
</script>
```

#### 8. 分支循环结构

##### 8.1 分支结构

- v-if
- v-else
- v-else-if
- v-show

##### 8.2 v-if和v-show的区别：

- v-if控制元素是否渲染到页面

- v-show控制元素是否显示（已经渲染到了页面，dom元素还存在在页面中，只是隐藏了）

  ```html
  <div id="app">
      <h2>{{message}}</h2>
      成绩：<input type="text" v-on:change="score=$event.target.value"><br>
      <!-- 分支结构的基本用法： -->
      <div v-if="score>=90">优秀</div>
      <!-- 多个分支结构使用 v-else-if -->
      <!-- 单个分支可以使用v-if 或者和v-else联合使用 -->
      <div v-else-if="80<=score<90">良好</div>
      <div v-else-if="60<=score<80">一般</div>
      <div v-else="score<60">不及格</div>
      <!-- v-show的用法 -->
      <!-- 传递的参数是false则不显示 也就是在标签上加了一个display:none 标签本身还存在 -->
      <!-- dom 元素标签本身还是存在的 -->
      <div v-show="flag">测试v-show</div>
      <button @click="handle()">显示v-show</button>
    </div>
    <!-- v-if和v-show的区别： v-if -->
    <script src="../../vue/vue.js"></script>
    <script>
      const app = new Vue({
        el: "#app",
        data: {
          message: "哈哈",
          score: 10,
          flag: false
        },
        methods: {
          handle(){
            this.flag = !this.flag;
          }
        },
      });
    </script>
  ```

  

##### 8.3 循环结构

- v-for遍历数组

  ```html
  <li v-for="item in list">{{item}}</li>
  <li v-for="(item,index) in list">{{item}}+'------------'+{{index}}</li>
  ```

  

- key的作用：帮助Vue区别不同元素，从而提高性能

  ```html
  <li v-bind:key='item.id' v-for="(item,index) in list">{{item}}+'------------'+{{index}}</li>
  ```

  - 可以这样理解key的作用：**key 就是相当于一个标识，做一个标识可以方便vue辨识每一个看起来一样的元素节点**。这个值不一定就是item的id，也可能是其他的值，只要是唯一的就行。比如可以是index的值。

- v-for遍历对象

  ```html
  <!-- 第一个值是该对象属性的值，第二个值是该对象的属性 第三个值是这个属性的索引 -->
        <li v-for="(value,key,index) in obj">{{value}},{{key}},{{index}}</li>
  ```

- v-if和v-for结合使用

  ```html
   <li v-if="value==='毛毛'" v-for="(value,key,index) in obj">{{value}},{{key}},{{index}}</li>
  ```

  

```html
  <div id="app">
    <h2>{{message}}</h2>
    <ul>
      <li v-for="item in fruits">{{item}}</li>
    </ul>
    <ol>
      <li v-for="(item,index) in fruits">{{item}} ---------> {{index}}</li>
    </ol>
    <!-- 遍历对象 -->
    <ul>
      <!-- 第一个值是该对象属性的值，第二个值是该对象的属性 第三个值是这个属性的索引 -->
      <li v-for="(value,key,index) in obj">{{value}},{{key}},{{index}}</li>
    </ul>
    <ul>
      <li v-if="value==='毛毛'" v-for="(value,key,index) in obj">{{value}},{{key}},{{index}}</li>
    </ul>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    const app = new Vue({
      el: "#app",
      data: {
        message: "哈哈",
        fruits: ["apple", "orange", "banana"],
        obj: {
          name: "毛毛",
          age: 22,
          gender: "男"
        }
      }
    });
  </script>
```

#### 9. vue指令案例

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    div {
      display: none;
    }

    #app {
      display: block;
    }

    li {
      border: 1px solid orange;
      list-style: none;
      flex: 1;
      text-align: center;
    }

    ul {
      display: flex;
      justify-content: center;
      width: 300px;
    }

    .active {
      background-color: orchid;
    }

    .divActive {
      display: block;
    }
  </style>
</head>

<body>
  <div id="app">
    <ul>
      <li :key="item.id" v-on:click="change(index)" v-bind:class="currentIndex===index?'active':''"
        v-for="(item,index) in list">{{item.title}}</li>
    </ul>
    <div :key="item.id" :class="currentIndex===index?'divActive':''" v-for="(item,index) in list">{{item.path}}</div>  <!-- 这里div的内部应该是图片标签img
		然后将path赋值给src属性 别忘了其属性需要使用v-bind进行动态绑定哦
-->
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    const app = new Vue({
      el: "#app",
      data: {
        // 元素下标 默认选中第一个
        currentIndex: 0,
        list: [
          {
            id: 1,
            title: "apple",
            path: "苹果图片"
          },
          {
            id: 2,
            title: "orange",
            path: "橘子图片"
          },
          {
            id: 3,
            title: "lemon",
            path: "柠檬图片"
          },
        ]
      },
      methods: {
        change(index) {
          // 实现选项的切换  本质上就是操作类名
          // 就是通过切换 currentIndex的值为当前点击li的值完成类名的控制
          this.currentIndex = index;
        }
      },
    });
  </script>
</body>

</html>
```

#### 10. vue的编程风格

**声明式编程**： 模板的结构和最终显示的效果基本一致。









### Vue常用特性

#### 1. 常用指令预览

- 表单操作
- 自定义指令
- 计算属性
- 过滤器
- 侦听器
- 生命周期

#### 2. 表单操作

![image-20210415235549605](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210415235549605.png)

##### 2.1 基于Vue的表单操作

- input 单行文本
- textarea 多行文本
- select 下拉多选
- radio 单选框
- checkbox 多选框

##### 2.2 表单域修饰符

- number ： 转换为数值
- trim：去掉开头和结尾的空格
- lazy： 将input事件切换为change事件

```html
<input v-model.number="age" type="number" />
```

- 单选框如何实现单选？

  1、 两个单选框需要同时通过v-model 双向绑定 一个值 
  2、 每一个单选框必须要有value属性  且value 值不能一样
  3、 当某一个单选框选中的时候 v-model  会将当前的 value值 改变 data 中的 数据 

- 复选框如何实现复选

  1、 复选框需要同时通过v-model 双向绑定 一个值 
  2、 每一个复选框必须要有value属性  且value 值不能一样 
  3、 当某一个单选框选中的时候 v-model  会将当前的 value值 改变 data 中的 数据

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
<div id="app">
  <form>
    <div>
      <span>姓名：</span>
      <span>
        <input type="text" v-model="uname">
      </span>
    </div>
    <div>
      <span>性别：</span>
      <span>
        <input type="radio" id="male" value="1" v-model="gender">
        <label for="male">男</label>
        <input type="radio" id="female" value="2" v-model="gender">
        <label for="female">女</label>
      </span>
    </div>
    <div>
      <span>爱好：</span>
      <input type="checkbox" id="ball" value="1" v-model="hobby">
      <label for="ball">篮球</label>
      <input type="checkbox" id="sing" value="2" v-model="hobby">
      <label for="sing">唱歌</label>
      <input type="checkbox" id="code" value="3" v-model="hobby">
      <label for="code">写代码</label>
    </div>
    <div>
      <span>职业：</span>
      <!-- 单选多选都可以 多选的话 occupation是一个数组 -->
      <select v-model="occupation" multiple='true'>
        <option value="0">请选择职业</option>
        <option value="1">教师</option>
        <option value="2">软件工程师</option>
        <option value="3">路上</option>
      </select>
    </div>
    <div>
      <span>个人简介：</span>
      <textarea v-model="desc"></textarea>
    </div>
    <!-- 阻止默认行为 -->
    <input type="submit" @click.prevent="handle">
  </form>
</div>
<script src="../../vue/vue.js"></script>
<script>
  const app = new Vue({
    el: "#app",
    data: {
      uname:'李四',
      // 默认选中男
      gender:"1",
      hobby:['2'],
      occupation:[1,2],
      desc:'你好！'
    },
    methods: {
      handle(){
        console.log(this.uname);
        console.log(this.gender);
        console.log(this.hobby);
        console.log(this.occupation);
        console.log(this.desc);
        // 调用ajax发起提交
      }
    },
  });
</script>
</body>
</html>
```

```html
<div id="app">
    <form>
      <!-- 输入的年龄必须为数字 -->
      <!-- 使用 .number  可以直接将输入框中的字符串转为数字 前提是可以转换为数字 -->
      <!-- 因此可以结合输入框的number属性一起使用 -->
      <!-- 这样可以确保一定可以转换为数字 -->
      年龄：<input type="number" v-model.number="age"><br>
      <!-- 去掉开头和结尾的空格 -->
      可以去掉空格：<input type="text" v-model.trim="info">
      <br>
      <!-- .lazy  可以理解为懒加载的那种机制 -->
      <!-- 将输入框的input事件切换为change事件 -->
      <!-- v-model 的双向绑定用的事件是input事件 -->
      <!-- 只有内容发生改变就会立刻触发 -->
      <!-- change事件是失去焦点时才会触发 -->
      改变触发时机：<input type="text" v-model.lazy="con">
      <span>{{con}}</span>
      <br><button v-on:click.prevent="handle">点击</button>
    </form>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    const app = new Vue({
      el: "#app",
      data: {
        age: '',
        info: '',
        con: ''
      },
      methods: {
        handle() {
          console.log(this.age + 11);
          console.log(this.info);
          console.log(this.con);
        }
      },
    });
  </script>
```

#### 3. 自定义指令

##### 3.1 为什么需要自定义指令

内置的指令不满足需求。

##### 3.2 自定义指令的语法（获取元素焦点）

```html
<script>
Vue.directive("focus", {
              inserted:function(el){
// 获取元素焦点
    el.focus();
}
              })
</script>
```

```html
<div id="app">
    <!-- 使用自定义指令时 需要在前面加上 v- -->
    <!-- 这是一个自动获取元素焦点的自定义指令 -->
    <input type="text" v-color='msg'>       
    <input type="text" v-color='msg2'>       
    <input type="text" v-color='{"color":"#aca"}'>       
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    // 注册一个全局的自定义指令
    // 改变输入框的背景颜色
    Vue.directive("color",{
      // bind 只调用一次  指令第一次绑定到元素时调用
      // inserted 被绑定元素插入父节点时调用（保证父节点存在 但不一定已被插入文档中）
      bind:function(el,binding){
        // binding  一个对象
        /* 
        binding 对象包含的属性
          name: 指令的名称  不包含 v-
          rawName: 指令的名称 包含 v-
          value: 传递的值  也就是 = 右边的值
          modifiers:
          def:
        */
        console.log(binding);
        // 根据指令的参数 设置背景色
        el.style.backgroundColor = binding.value.color;
      }
    });
    const app = new Vue({
      el:"#app",
      data:{
        msg:{
          color:"skyblue"
        },
        msg2:{
          color:"red"
        }
      }
    });
  </script>
```



##### 3.3 自定义指令用法

```html
<input type="text" v-focus>
```

##### 3.4 带参数的自定义指令

```html
<div id="app">
    <!-- 使用自定义指令时 需要在前面加上 v- -->
    <!-- 这是一个自动获取元素焦点的自定义指令 -->
    <input type="text" v-color='msg'>       
    <input type="text" v-color='msg2'>       
    <input type="text" v-color='{"color":"#aca"}'>       
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    // 注册一个全局的自定义指令
    // 改变输入框的背景颜色
    Vue.directive("color",{
      // bind 只调用一次  指令第一次绑定到元素时调用
      // inserted 被绑定元素插入父节点时调用（保证父节点存在 但不一定已被插入文档中）
      bind:function(el,binding){
        // binding  一个对象
        /* 
        binding 对象包含的属性
          name: 指令的名称  不包含 v-
          rawName: 指令的名称 包含 v-
          value: 传递的值  也就是 = 右边的值
          modifiers:
          def:
        */
        console.log(binding);
        // 根据指令的参数 设置背景色
        el.style.backgroundColor = binding.value.color;
      }
    });
    const app = new Vue({
      el:"#app",
      data:{
        msg:{
          color:"skyblue"
        },
        msg2:{
          color:"red"
        }
      }
    });
  </script>
```

##### 3.5 局部指令

```html
<div id="app">
    <input type="text" v-color="{color:'#aea'}" v-focus>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    const app = new Vue({
      el: "#app",
      data: {},
      // 定义局部组件  directives 指令  只能在本实例中使用
      directives: {// 自定义局部组件可以有多个
        color: {
          inserted: function (el, binding) {
            el.style.backgroundColor = binding.value.color;
          }
        },
        focus: {
            inserted: function (el) {
              // 获取焦点
              el.focus();
            }
          }
      }
    })
  </script>
```

#### 4. 计算属性

##### 4.1 为什么需要计算属性

表达式的计算逻辑可能会比较复杂，使用计算属性可以使模板内容更加简洁。

```html
<div id="app">
    <h2>{{message}}</h2>
    <!-- 字符串反转 -->
    <!-- 使用计算属性让字符串进行反转 -->
    <h2>{{reverseStr}}</h2>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    const app = new Vue({
      el: "#app",
      data: {
        message: "123456"
      },
      computed: {
        // 计算属性操作的数据发送了改变 那么计算属性会重新计算值
        reverseStr() {
          return this.message.split("").reverse().join("");
        }
      }
    })
  </script>
```

##### 4.2 计算属性和方法的区别

- 计算属性是基于他们的依赖进行缓存的
- 方法不存在缓存

```html
<div id="app">
    <h2>{{message}}</h2>
    <!-- 字符串反转 -->
    <!-- 使用计算属性让字符串进行反转 -->
    <!-- 计算属性对于同样的数据，如果数据不发生改变，只会调用一次，因为有缓存 -->
    <h2>{{reverseStr}}</h2>
    <h2>{{reverseStr}}</h2>
    <!-- 发现方法执行了两次 -->
    <h2>{{reverseString()}}</h2>
    <h2>{{reverseString()}}</h2>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    const app = new Vue({
      el: "#app",
      data: {
        message: "123456"
      },
      methods: {
        reverseString() {
          console.log("methods");
          return this.message.split("").reverse().join("");
        }
      },
      computed: {
        // 计算属性操作的数据发送了改变 那么计算属性会重新计算值
        reverseStr() {
          console.log("computed");
          return this.message.split("").reverse().join("");
        }
      }
    })
  </script>
```

**只有当计算属性的依赖发生改变，也就是那个数据发生改变，计算属性的缓存才会发生改变，当数据发生改变时，计算属性会自动重新计算（只要调用过计算属性），刷新值**



#### 5. 侦听器

![image-20210416223116055](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210416223116055.png)

**侦听器**： 是一个对象  键是需要观察的表达式 值是对应的回调函数。值也可以是方法名，或者包含选项的对象。Vue 实例将会在实例化时调用 $watch()，遍历 watch 对象的每一个 property。

##### 5.1 侦听器的应用场景

数据变化时执行异步或开销较大的操作。

##### 5.2 侦听器的用法

```html
<div id="app">
    <span>名：</span><input type="text" v-model="lastName"><br>
    <span>姓：</span><input type="text" v-model="firstName"><br>
    全名：<span>{{fullName}}</span>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    const app = new Vue({
      el:"#app",
      data:{
        firstName:"Jim",
        lastName:"Green",
        fullName:"Jim Green"
      },
      // 定义侦听器  数据发生改变会会调用相应侦听器里面的回调函数
      // 侦听器： 是一个对象  键是需要观察的表达式 值是对应的回调函数
      // 值也可以是方法名，或者包含选项的对象。Vue 实例将会在实例化时调用 $watch()，遍历 watch 对象的每一个 property。
      watch:{
        firstName:function(val){
          // val是变化后的值
          this.fullName = val+ " "+ this.lastName
        },
        lastName(val){
          this.fullName = this.firstName + " " + val;
        }
      }
    })
  </script>
```

##### 侦听器的案例

验证用户名是否可用：

![image-20210416225201306](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210416225201306.png)

![image-20210416225343720](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210416225343720.png)

```html
<div id="app">
    <span>用户名：<input type="text" v-model="uname"></span>
    <span>{{tip}}</span>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    const app = new Vue({
      el: "#app",
      data: {
        uname: '',
        tip: ''
      },
      methods: {
        checkName(uname) {
          // 调用接口验证数据，但是可以采用定时任务的方式模拟接口调用
          // 保存当前的this  或者使用call()绑定调用this的值
          function check() {
            if (uname === 'admin') {
              this.tip = "用户名已存在！";
            } else if (uname !== '') {
              this.tip = '用户名可用使用！';
            } else {
              this.tip = ''
            }
          }
          // 改变函数内部的this指向  指向为当前对象  不使用call和apply方法 是因为哪两个方法在改变函数内部this指向的同时，将函数也调用了一次
          // 顺便复习一下，call方法的参数除了第一个是this指向，后面都是正常顺序传递参数到函数内部就可以
          // apply方法除了this指向，后面的参数以伪数组的形式进行传递
          check = check.bind(this);
          setTimeout(check, 1000);
        }
      },
      // 定义侦听器
      watch: {
        // 使用监听器处理用户名的变化
        // 如果用户名发生变化 调用后台接口进行验证
        // 根据验证的结果调整提示信息
        uname(val) {
          // 调用后台接口进行验证用户名的合法性
          this.checkName(val);
          // 修改提示信息
          this.tip = "正在验证...用户名是否可用";
        }
      }
    })
  </script>
```

#### 6. 过滤器

##### 6.1 过滤器的作用是什么

格式化数据，比如将字符串格式化为首字母大写，将日期格式化为指定的日期格式等等。

![image-20210416232416987](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210416232416987.png)

##### 6.2 自定义过滤器

- 全局过滤器

  ```js
  Vue.filter("过滤器名称",(value)=>{
     // 过滤器的业务逻辑 
  });
  ```

  

- 局部过滤器

  ```js
  // 局部过滤器 只能在Vue实例里面使用
        filters: {
          lower(val) {
            return val.charAt(0).toLowerCase() + val.slice(1);
          }
        }
  ```

  ##### 6.3 过滤器的使用

  ```html
  <div> {{msg | upper}}</div>
  <div> {{msg | upper | lower}}</div>
  <div v-bind:id='id | formatId'>  </div>
  ```

  

```html
<div id="app">
    <input type="text" v-model="msg">
    首字母大写：<div>{{msg | upper}}</div>
    <!-- 两个过滤器 上一个过滤器的输出作为第二个过滤器的输入 -->
    <!-- 这个就是过滤器的级联操作 -->
    首字母小写：<div>{{msg | upper | lower}}</div>
    <!-- 过滤器也可以在标签属性里面使用 -->
    <!-- 发现dom元素中 id的值首字符大写了 -->
    <div v-bind:id="id | upper"></div>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    // 定义过滤器
    Vue.filter("upper", function (val) {
      // 首字母大写
      // 需要返回这个值
      return val.charAt(0).toUpperCase() + val.slice(1);
    })
    const app = new Vue({
      el: "#app",
      data: {
        msg: "",
        id:'abc',
      },
      // 局部过滤器 只能在Vue实例里面使用
      filters: {
        lower(val) {
          return val.charAt(0).toLowerCase() + val.slice(1);
        }
      }
    });
  </script>
```

**<span style="color:red">注意：Vue实例（比如这里的app）本身也是一个组件</span>**。

##### 6.4 带参数的过滤器

```js
Vue.filter("format",(value,arg1)=>{
    // value 就是过滤器传递过来的参数  也就是过滤器要处理的参数
    // 所以手动传递的参数 是从第二个开始的（按照顺序来的）
    // 实际上 我们只需手动传递处理数据时需要的参数就可以了，要处理的数据是在 | 前的输出自动输入进来的
})
```

##### 6.5 带参数的过滤器的使用

```html
<div>{{date | format("yyyy-MM-dd")}}</div>
```

##### 6.6 格式化日期的案例

![image-20210416234813411](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210416234813411.png)

```html
<div id="app">
    <!-- v-cloak数据渲染完毕以后才显示 -->
    <div v-cloak>{{date | format("yyyy-MM-dd hh:mm:ss")}}</div>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    /* Vue.filter("format", (val, pattern) => {
      // 格式化日期
      // 获取日期对象的年份，月份，该月份的第几天
      if(pattern === "yyyy-MM-dd"){
        const date = new Date(val);
      const y = date.getFullYear();
      const M = date.getMonth() + 1;
      const d = date.getDate();
      return `${y}-${M}-${d}`;
      }else{
        
      }
    }); */
    Vue.filter('format', function (value, arg) {
      function dateFormat(date, format) {
        if (typeof date === "string") {
          var mts = date.match(/(\/Date\((\d+)\)\/)/);
          if (mts && mts.length >= 3) {
            date = parseInt(mts[2]);
          }
        }
        date = new Date(date);
        if (!date || date.toUTCString() == "Invalid Date") {
          return "";
        }
        var map = {
          "M": date.getMonth() + 1, //月份 
          "d": date.getDate(), //日 
          "h": date.getHours(), //小时 
          "m": date.getMinutes(), //分 
          "s": date.getSeconds(), //秒 
          "q": Math.floor((date.getMonth() + 3) / 3), //季度 
          "S": date.getMilliseconds() //毫秒 
        };

        format = format.replace(/([yMdhmsqS])+/g, function (all, t) {
          var v = map[t];
          if (v !== undefined) {
            if (all.length > 1) {
              v = '0' + v;
              v = v.substr(v.length - 2);
            }
            return v;
          } else if (t === 'y') {
            return (date.getFullYear() + '').substr(4 - all.length);
          }
          return all;
        });
        return format;
      }
      return dateFormat(value, arg);
    })

    const app = new Vue({
      el: "#app",
      data: {
        date: new Date()
      },
    });
    setInterval(() => {
      app.date = new Date();
    }, 1000);
  </script>
```

##### 6.7 Vue实例的生命周期

###### 6.7.1 主要阶段

- 挂载（初始化相关属性）
  1. beforeCreate
  2. created
  3. beforeMount
  4. mounted
- 更新（元素或组件的变更操作）
  1. beforeUpdate
  2. updated
- 销毁（销毁相关属性）
  1. beforeDestroy
  2. destroyed

![image-20210417001536823](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210417001536823.png)

###### 6.7.2 Vue实例的产生过程

1. beforeCreate 在实例初始化之后，数据观测和事件配置之前被调用。
2.  created 在实例创建完成后被立即调用。
3. beforeMount 在挂载开始之前被调用。
4. mounted el被新创建的app.$el替换，并挂载到实例上去之后调用该钩子。
5. beforeUpdate 数据更新时调用，发生在虚拟DOM打补丁之前
6. updated 由于数据更改导致的虚拟DOM重新渲染和打补丁，在这之后会调用该钩子
7. beforeDestroy 实例销毁之前调用。
8. destroyed 实例销毁后调用。

#### 7. 图书管理综合案例

##### 7.1 补充知识（数组相关API）

###### 7.1.1. 变异方法（修改原有数据）

Vue包含一组观察数组的变异方法，所以它们也将会触发视图更新，方法如下：

- push() 追加元素  返回值是数组的长度
- pop()  删除元素 返回值是删除的那个元素
- shift()  删除第一个元素  返回的是被删除元素的值  
- unshift()  在第一个元素之前追加
- splice(start,number, ...args)  从start开始删除，删除number个数据，还可以有第三个参数，也就是用于替换操作，将前面的元素删除一行替换为新的元素  
- sort()  排序
- reverse()  反转

###### 7.1.2  替换数组（生成新的数组）

- filter() 过滤
- concat() 拼接
- slice()  切割

```html
<div id="app">
    <span>
      <input type="text" v-model="name">
      <button @click="add">添加</button>
      <button @click="del">删除</button>
      <button @click="change">替换</button>
    </span>
    <ul>
      <li :key="index" v-for="(item,index) in list">{{item}}</li>
    </ul>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    const app = new Vue({
      el:"#app",
      data:{
        list:['苹果','香蕉','水皮套'],
        name:''
      },
      methods: {
        add(){
          this.list.push(this.name);
        },
        del(){
          this.list.pop();
        },
        change(){
          // 将原来的数组替换为新数组页面才会发生响应式的数据改变
          this.list = this.list.slice(0,2);
        }
      },
    });

  </script>
```

###### 7.1.3 修改响应式数据

- Vue.set(app.items,indexOfltem,newValue)
- app.$set(app.items,indexOfltem,newValue)
  1. 参数1 表示要处理的数组名称
  2. 参数2 表示要处理的数组的索引
  3. 参数3 表示要处理的数组的值

```html
<div id="app">
    <ul>
      <li :key="index" v-for="(item,index) in list">{{item}}</li>
    </ul>
    <ol>
      <li :key="index" v-for="(item,index) in list2">{{item}}</li>
    </ol>
    <ul>
      <li :key="index" v-for="(item,index) in list3">{{item}}</li>
    </ul>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    const app = new Vue({
      el: "#app",
      data: {
        list: ['苹果', '香蕉', '水皮套'],
        list2: ['苹果', '香蕉', '水皮套'],
        list3: ['苹果', '香蕉', '水皮套'],
        name: ''
      },
    });
    // 动态处理响应式数据
    setTimeout(() => {
      // 我们直接修改数组里面的数据 发现页面并不会响应式的修改页面里面渲染过的数据
      // 索引的形式修改的数据不是响应式的
      app.list[1] = "哈哈";
      app.list[4] = "hahah";
      // 类似的，这种直接添加到数组或者对象上的属性或元素都不是响应式的
    },/*  1000 */3000);
    setTimeout(() => {
      // 使用 Vue.set()修改数组里面的值是响应式的  后面添加的值也是响应式的
      // 这样修改页面的数据也会实时的发生渲染，所以上面的list的数据也被页面重新渲染了
      // 前提是在我这个修改生效之前 也就是渲染之前
      Vue.set(app.list2, 1, "哈哈");
    }, 1000);
    setTimeout(() => {
      // 使用 app.$set() 添加的数据或者修改的数据也都是响应式的
      app.$set(app.list3, 1, "毛毛")
    }, 2000);
  </script>
```

###### 7.1.4 图书案例代码的实现

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style type="text/css">
    .grid {
      margin: auto;
      width: 530px;
      text-align: center;
    }

    .grid table {
      border-top: 1px solid #C2D89A;
      width: 100%;
      border-collapse: collapse;
    }

    .grid th,
    td {
      padding: 10;
      border: 1px dashed #F3DCAB;
      height: 35px;
      line-height: 35px;
    }

    .grid th {
      background-color: #F3DCAB;
    }

    .grid .book {
      padding-bottom: 10px;
      padding-top: 5px;
      background-color: #F3DCAB;
    }
  </style>
</head>

<body>
  <div id="app">
    <div class="grid">
      <div>
        <h1>图书管理</h1>
        <div class="book">
          <div>
            <label for="id">编号：</label>
            <input type="text" id="id" v-model="id" v-bind:disabled="flag">
            <label for="name">名称：</label>
            <input type="text" id="name" v-model="name">
            <button v-on:click="handle">提交</button>
          </div>
        </div>
      </div>
      <table>
        <thead>
          <tr>
            <th>编号</th>
            <th>名称</th>
            <th>时间</th>
            <th>操作</th>
          </tr>
        </thead>
        <tbody>
          <tr v-cloak :key='item.id' v-for='item in books'>
            <td>{{item.id}}</td>
            <td>{{item.name}}</td>
            <td>{{item.date}}</td>
            <td>
              <a href="" @click.prevent="toEdit(item.id)">修改</a>
              <span>|</span>
              <a href="" @click.prevent="deleteBook(item.id)">删除</a>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    /*
      图书管理-图书列表展示功能
      注意事项：<a href="" @click.prevent>修改</a>
      事件绑定时，可以只添加修饰符，而不绑定事件函数
    */
    var vm = new Vue({
      el: '#app',
      data: {
        id: '',
        name: '',
        books: [{
          id: 1,
          name: '三国演义',
          date: ''
        }, {
          id: 2,
          name: '水浒传',
          date: ''
        }, {
          id: 3,
          name: '红楼梦',
          date: ''
        }, {
          id: 4,
          name: '西游记',
          date: ''
        }],
        flag: false
      },
      methods: {
        handle() {
          if (this.flag) {
            // 编辑操作
            // 根据当前的id去更新数组中的数据
            // 监听函数的this就是父级作用域中的this
            this.books.some((item) => {
              if (item.id === this.id) {
                item.name = this.name;
                // 完成更新操作以后终止循环
                // 将表单恢复可使用的 状态
                this.flag = false;
                this.name = '',
                  this.id = ''
                return true;
              }
            })
            return;
          }
          // 添加图书
          const book = {};
          book.id = this.id;
          book.name = this.name;
          book.date = '';
          this.books.push(book);
          // 清空表单
          this.id = '';
          this.name = '';
        },
        // 修改图书数据
        toEdit(id) {
          // 禁止修改id
          this.flag = true;
          // 根据id查询出要编辑的数据
          const book = this.books.filter((item) => {
            return item.id === id;
          });
          // 把获取到的信息填充到表单
          this.id = book[0].id;
          this.name = book[0].name;
        },
        // 删除图书
        deleteBook(id) {
          // 根据id从数组中查找元素的索引
          // 这个索引表示的是在数组中的第几号元素
          const index = this.books.findIndex((item) => {
            return item.id === id;
          });
          // 根据索引删除数组元素
          this.books.splice(index, 1);

          // 也可以通过filter方法进行删除  也就是将不要的数据过滤掉
          // 然后将现有的数据替换为新数组
          // this.books = this.books.filter((item)=>{item.id!==id});
        }
      },
    });
  </script>
</body>

</html>
```

#### 8. 常用特性应用场景

- 过滤器（格式化日期）
- 自定义指令（获取表单焦点）
- 计算属性（统计图书数量，总价格）
- 侦听器（验证图书存在性）
- 生命周期（图书数据处理）





## Vue组件化

### 组件化开发思想

#### 1. 现实中的组件化

![image-20210417231730489](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210417231730489.png)

- 标准
- 分治
- 重用
- 组合

#### 2. 编程中的组件化

![image-20210417232006962](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210417232006962.png)



#### 3. 组件化规范： Web Components

- 我们希望尽可能多的重用代码
- 自定义组件的方式不太容易（html，css 和 js）
- 多次重用可能导致冲突

![image-20210417232247833](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210417232247833.png)



### 组件注册

#### 1. 全局组件注册语法

```js
Vue.component("组件名称",{
    data:组件数据,
    template:组件模板内容
});
```

#### 2. 组件的使用

```html
<div id="app">
    <!-- 使用自定义的全局组件 -->
    <!-- 和app形成了父子组件的关系 -->
    <button-counter></button-counter>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    // 注册全局组件
    Vue.component("button-counter", {
      // data 是数据  其类型是一个函数  函数的返回值是一个对象
      data: function () {
        // return 返回是一个对象
        return {
          count: 0
        };
      },
      // 组件内容
      template: "<button @click='add'>点击了{{count}}次</button>",
      // 组件可以使用的方法
      methods: {// 事件处理
        add() {
          this.count++;
        }
      },
    });
    const app = new Vue({
      el: "#app",
      data: {}
    })
  </script>
```

#### 3. 组件的注意事项

- 组件中的**data**是一个**函数**，其**返回值**是一个**对象**。

  为什么data要是一个函数呢？

  因为函数会形成一个闭包环境，可以保证每次使用这个组件形成的实例都有自己的独立的那一份数据，而不会相互之间发生数据的影响。

  如果data和Vue的实例(app)一样，都是一个对象，那么多次使用组件，修改其相应的值数据可能发生相应影响。因为其共用一份数据。一个data对象。

- 组件模板template内容必须是单个根元素（只能有一个根标签）

  

- 组件模板内容可以是模板字符串（``）

  模板字符串需要浏览器提供支持（ES6 语法）

  ```js
  template: `
        <div>
          <button @click='add'>点击了{{count}}次</button>
          </div>
        `,
  ```

  

- 组件命名方式

  - 短横线方式

    ```html
    <script>
    Vue.component("button-counter", {
          // data 是数据  其类型是一个函数  函数的返回值是一个对象
          data: function () {
            // return 返回是一个对象
            return {
              count: 0
            };
          },
          // 组件内容
          template: `
          <div>
            <button @click='add'>点击了{{count}}次</button>
            </div>
          `,
          // 组件可以使用的方法
          methods: {// 事件处理
            add() {
              this.count++;
            }
          },
        });
    </script>
    ```

    

  - 驼峰式组件

    ```html
    <script>
    Vue.component("helloWorld", {
          data: function () {
            return {
              msg: "hello world"
            }
          },
          template: `
            <div>{{msg}}</div>
          `,
        });
        template: `
          <div>
            <button @click='add'>点击了{{count}}次</button>
            <helloWorld></helloWorld>
            </div>
          `,
    </script>
    ```

    

**注意：我们在进行组件注册的时候，如果使用驼峰式命名的组件，那么在使用组件的时候，只能在字符串模板中用驼峰式的方式使用该组件，在普通标签模板中，必须使用短横线的方式使用组件*。**

```html
<div id="app">
    <!-- 使用自定义的全局组件 -->
    <!-- 和app形成了父子组件的关系 -->
    <button-counter></button-counter>
    <hello-world></hello-world>
    <!-- 驼峰式的命名不能直接使用 需要替换为短横线的方式使用 -->
    <!-- 但是我们可以在定义的其他组件模板中直接使用驼峰式的组件标签 -->
    <!-- <helloWorld></helloWorld> -->
  </div>
```

#### 4. 局部组件的注册方式

```html
<!-- 在父组件外面没办法使用 -->
  <!-- 即使是在定义的全局组件模板中，也不能使用 -->
  <comp-btn></comp-btn>
  <div id="app">
    <!-- 在父组件Vue实例中直接使用 -->
    <comp-btn></comp-btn>
    <comp-bbt></comp-bbt>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    // 定义局部组件模板
    const ComponentB = {
      // data仍然还是一个函数 返回值是一个对象
      data: function () {
        return {
          msg: "hello world"
        }
      },
      template: `<div>{{msg}}</div>`,
    };
    const app = new Vue({
      el: "#app",
      data: {},
      // 局部组件在这里注册 这里定义的组件只能在Vue实例里面用 可以理解为局部的子组件  因为实例也是组件哦
      // 局部组件只能在注册它的父组件中使用
      components: {
        "comp-btn": {
          data: function () {
            return {
              msg: "哈哈",
            }
          },
          template: "<div>{{msg}}</div>"
        },
        "comp-bbt": ComponentB,
      }
    })
  </script>
```

### Vue调试工具用法

<img src="F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210418202519750.png" alt="image-20210418202519750" style="zoom:50%;" />

1. 克隆仓库

   ```shell
   git clone git@github.com:vuejs/vue-devtools.git
   ```

   

2. 安装依赖包

   ```shell
   # 先进入下载下的目标
   cd vue-devtools
   ```

   

3. 构建

   ```shell
   # 下依赖项
   npm install
   # 打包
   npm run build
   ```

   

4. 打开Chrome拓展页面

5. 选中开发者模式

6. 加载以加载的拓展，选择shells/chrome

### 组件间数据交互

#### 1. 组件内部通过 props 属性接收传递过来的值

```js
Vue.component("menu-item",{
    props:['title'],
    templete:"<div>{{title}}</div>"
})
```

#### 2. 父组件通过属性将值传递给子组件

```html
<menu-item title="来自父组件的数据"></menu-item>
<menu-item :title="title"></menu-item>
```

```html
<!--基本使用-->
<div id="app">
    <menu-item title1="哈哈哈我是父组件没想到吧" :tit="tit"></menu-item>
    <menu-item :title1="title" :tit="tit"></menu-item>
    <menu-item :title1="title" :tit="tit" :aaa="aaa"></menu-item>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    Vue.component("menu-item", {
      data: function () {
        return {
          msg: "我是子组件的值"
        }
      },
      // 在组件中使用props属性接收来自父组件的值
      // props属性里面定义的数组的元素，就是子组件的属性 该属性可以接收来自父组件的值
      // 相当于 props里面定义的属性可以用来接收父组件传过来的参数
      // 如果没有定义的属性 是没办法接收到父组件传递过来的参数的
      props: ['title1', 'tit'],
      // 下面的 {{aaa}} 因为没有定义 所以会报错
      template: "<div>{{title1}}---{{tit}}---{{aaa}}---{{msg}}</div>"
    });
    const app = new Vue({
      el: "#app",
      data: {
        title: "我是父组件的值",
        tit: "我是父组件的值",
        aaa: '啊哈哈哈'
      }
    })
  </script>
```

#### 3. props 属性名规则

- **在props 中使用驼峰形式，模板中需要使用短横线的形式**

  原因是在dom标签中，属性是不区分大小写的。 **`<html tit-b="">``</html>`标签和`<html TIT-b="">``</html>`**里面的属性是一样的。

- 字符串形式的模板中没有这个限制（比如插值表达式，在其他组件中使用这个组件中定义的属性，可以使用驼峰式）

```html
<div id="app">
    <menu-item menu-title="哈哈"></menu-item>
    <menu-item menu-TITLE="哈哈"></menu-item>
    <!-- 直接使用 menuTitle 发现不起效果 并且控制台会报警告 -->
    <!-- 提醒我们驼峰式的命名需要使用 - 来连接，进行替代 -->
    <menu-item menuTitle="哈哈"></menu-item>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    // 注册组件
    Vue.component("second-com",{
      props:["titTitle"],
      template:"<div>{{titTitle}}</div>"
    })
    Vue.component("menu-item",{
      props:['menuTitle'],
      // 我们在插值表达式中 可以直接使用驼峰式的变量命名来获取值
      // 在这个组件中使用其他的组件，也可以直接视图其他组件中的驼峰式的接收属性
      template:`<div>
        {{menuTitle}}
        <second-com titTitle="哼哼"></second-com>
        </div> `
    });
    const app = new Vue({
      el:"#app"
    })
  </script>
```

#### 4. props 属性值的类型

props属性值的类型，就是可以接收父组件传递过来的参数类型。

- 字符串
- 数值
- 布尔值
- 数组
- 对象

#### 5. 子组件向父组件传值

**Vue不推荐在子组件内部直接修改父组件传递过来的值**。*子组件可以直接操作 父组件 传递过来的数据*，*一旦操作数据，父组件的数据也会发生改变*。

* 但是： props属性传递数据原则： 单向数据流
* Vue 推荐 ： 采用子组件通过自定义事件的方式向父组件传递信息*

##### 5.1 组件通过自定义事件的方式向父组件传递信息

```html
<button v-on:click='$emit("enlarge-text")'>
    增大字体
</button>
```

##### 5.2 父组件监听子组件的事件

```html
<menu-item v-on:enlarge-text="fontSize += 1"></menu-item>
```



```html
<div id="app">
    <!-- 字符串形式的参数 -->
    <menu-item :parr='arr' @enlarge-text="handle"></menu-item>
    <div v-bind:style="{'font-size':fontSize+'px'}">哈哈</div>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    // 子组件向父组件传值的基本用法
    // 子组件可以直接操作 父组件 传递过来的数据
    // 一旦操作数据，父组件的数据也会发生改变
    /* 
    但是： props属性传递数据原则： 单向数据流
    Vue 推荐 ： 采用子组件通过自定义事件的方式向父组件传递信息
    */
    // 注册组件
    Vue.component("menu-item", {
      props: ['parr'],
      template: `<div>
        <ul>
          <li v-for='item in parr'>{{item}}</li>
          </ul>
          <button @click="parr.push('哈哈')">添加</button>
          <button @click="$emit('enlarge-text')">增大字体</button>
        </div> `
    });
    const app = new Vue({
      el: "#app",
      data: {
        arr: ['苹果', '香蕉', '蜜桃'],
        fontSize:10
      },
      methods: {
        handle(){
          // 扩大字体
          this.fontSize+=10
        }
      },
    })
  </script>
```

##### 5.3 子组件通过自定义事件向父组件传递信息(带参数)

```html
<button v-on:click='$emit("enlarge-text",10)'></button>
```

##### 5.4 父组件监听子组件带参数的事件

```html
<menu-item v-on:enlarge-text="fontSize += $event"></menu-item>
```

```html
<div id="app">
    <!-- 采用 $event 接收子组件传递过来的参数 -->
    <!-- $event 名字固定  就是子组件传递出来的值 -->
    <menu-item :parr='arr' @enlarge-text="handle($event)"></menu-item>
    <div v-bind:style="{'font-size':fontSize+'px'}">哈哈</div>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    Vue.component("menu-item", {
      props: ['parr'],
      template: `<div>
        <ul>
          <li v-for='item in parr'>{{item}}</li>
          </ul>
          <button @click="parr.push('哈哈')">添加</button>
          <button @click="$emit('enlarge-text',10)">增大字体</button>
        </div> `
    });
    const app = new Vue({
      el: "#app",
      data: {
        arr: ['苹果', '香蕉', '蜜桃'],
        fontSize:10
      },
      methods: {
        handle(val){
          // 扩大字体
          this.fontSize+=val
        }
      },
    })
  </script>
```



#### 6. 非父子组件间传值

##### 6.1 单独的事件中心管理组件间的通信

![image-20210419092310908](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210419092310908.png)

```js
const eventHub = new Vue()
```

##### 6.2 监听事件与销毁事件

```js
eventHub.$on("自定义事件名称", 事件函数)
eventHub.$off('要销毁的自定义事件名称')
```

##### 6.3 触发事件

```js
eventHub.$emit("要触发的自定义事件", [参数])
```

```html
<div id="app">
    <div>父组件的内容</div>
    <div>
      销毁事件按钮
      <button @click="handle">销毁</button>
    </div>
    <test-tom></test-tom>
    <test-jerry></test-jerry>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    // 兄弟组件间的通信

    // 提供事件中心
    const hub = new Vue();

    Vue.component("test-tom", {
      data() {
        return {
          num: 0
        }
      },
      template: `
      <div>
        <div>TOM:{{num}}</div>
        <button @click="handle">按钮</button>
        </div
      `,
      methods: {
        handle() {
          hub.$emit("jerry-event", 2);
        }
      },
      // 生命周期函数
      mounted() {
        // 这个钩子函数被触发  说明模板已经就绪 可以渲染数据了
        // 在这里监听事件，本组件内部自定义的一个事件
        hub.$on("tom-event", (val) => {
          this.num += val;
        })
      },
    });
    Vue.component("test-jerry", {
      data() {
        return {
          num: 0
        }
      },
      template: `
      <div>
        <div>JERRY:{{num}}</div>
        <button @click="handle">按钮</button>
        </div
      `,
      methods: {
        handle() {
          // 在这里我们要触发监听的对方(兄弟组件)的通信事件
          hub.$emit("tom-event", 1);
        }
      },
      mounted() {
        hub.$on("jerry-event", (val) => {
          this.num += val;
        })
      },
    });
    const app = new Vue({
      el: "#app",
      methods: {
        handle() {
          // 这两个事件都会失效了
          hub.$off("tom-event");
          hub.$off("jerry-event");
        }
      },
    })
  </script>
```

#### 7. 组件插槽

##### 7.1 组件插槽的作用

- 父组件向子组件传递内容（模板内容）

  ![image-20210419095749636](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210419095749636.png)

##### 7.2 组件插槽的基本用法

###### 7.2.1 插槽位置  

就是位于组件模板中的 `<slot></slot>` 标签

```js
Vue.component("alert-box",{
    template:`
		<div class="demo-arert-box">
		<strong>Error!!!</strong>
		<slot><slot>
</div>
`
});
```

###### 7.2.2 插槽内容

使用组件标签的时候，标签内部填充的数据 就会传递到组件插槽中，将组件插槽替换为实际的数据

```html
<alert-box>啊哈打瞌睡达克赛德</alert-box>
```

```html
<div id="app">
    <!-- 插槽的使用 -->
    <text-box>bug问题</text-box>
    <text-box>有警告</text-box>
    <text-box></text-box>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    Vue.component("text-box",{
      // 定义含有插槽的模板 <slot></slot>
      template:`
      <div>
        <strong>Error: </strong>
        <slot>默认内容</slot>
        </div>
      `
    })
    const app = new Vue({
      "el":"#app"
    })
  </script>
```

##### 7.3 具名插槽

###### 7.3.1 插槽定义

```js
Vue.component("test-box",{
    template:`
	<div>
	<header>
	<slot name="header"></slot>
</header>
<main>
<slot></slot>
</main>
<dooter>
<slot name="footer"></slot>
</footer>
</div>
`
})
```

###### 7.3.2 插槽内容

```html
<test-box>
<h1 slot="header">
    标题内容
    </h1>
    <p>
        主要内容1
    </p>
    <p>
        主要内容2
    </p>
    <p slot="footer">
        底部内容
    </p>
</test-box>
```

```html
<div id="app">
    <!-- 具名插槽的使用 -->
    <text-box>
      <p slot="header">标题</p>
      <p>哈哈哈</p>
      <div slot="footer">底部</div>
    </text-box>

    <!-- 引入新的标签 template slot属性的值就是具名插槽 slot 的 name的值-->
    <text-box>
      <!-- template标签是临时包裹信息，并不会渲染到页面 -->
      <!-- 因为template一次可以包裹多个标签来渲染具名插槽的数据 -->
      <!-- 而直接在标签上使用slot具名插槽 一次只能绑定一个，需要多次绑定才能达到效果 -->
      <template slot="header">
        <p>标题1</p>
        <p>标题2</p>
      </template>
      <template>
        <p>内容1</p>
      </template>
      <template slot="footer">
        <p>底部内容</p>
      </template>
    </text-box>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    Vue.component("text-box", {
      template: `
      <div>
	<header>
	<slot name="header"></slot>
</header>
<main>
<slot></slot>
</main>
<footer>
<slot name="footer"></slot>
</footer>
</div>
      `
    });
    const app = new Vue({
      el:"#app"
    })
  </script>
```

#### 8. 作用域插槽

- 应用场景：**父组件对子组件的内容进行加工处理**

##### 8.1 插槽定义

```js
template: `
          <div>
            <ul>
              <li :key='item.id' v-for="item in fruits">
                <slot v-bind:tt='item'>
                {{item.name}}
                </slot>
                </li>
              </ul>
            </div>
          `
```

##### 8.2 插槽内容

```html
<div id="app">
    <!-- 父组件决定那个子组件的数据高亮显示 -->
    <tmp-fruit :fruits="fruits">
      <!-- template标签的 slot-scope属性 可以得到插槽动态绑定的属性 -->
      <!-- slotProps 可以得到所有动态传递出来的属性 通过 . 的方法拿到需要的属性 -->
      <template slot-scope='slotProps' v-if="slotProps.tt.id === 2">
        <!-- slotProps.tt 拿到我们动态绑定的 tt 属性 -->
        <!-- 满足条件就填充插槽，不满足就按照默认的插槽里面的内容来渲染 -->
        <!-- 注意，这个 v-if在内部和在template上都可以作为判断条件，但是在内部更合适，有时候可以减轻的判断压力 -->
        <strong v-if="slotProps.tt.id === 2">
          {{slotProps.tt.name}}
        </strong>
      </template>
    </tmp-fruit>
  </div>
  <script src="../../vue/vue.js"></script>
  <script>
    const app = new Vue({
      el: "#app",
      data: {
        fruits: [{ id: 1, name: '苹果' },
        { id: 2, name: '香蕉' },
        { id: 3, name: '水利' },
        { id: 4, name: '火龙果' },
        ]
      },
      components: {
        'tmp-fruit': {
          props: ['fruits'],
          // 子组件提供一个插槽
          // 插槽动态绑定一个自定义的属性 比如tt
          template: `
          <div>
            <ul>
              <li :key='item.id' v-for="item in fruits">
                <slot v-bind:tt='item'>
                {{item.name}}
                </slot>
                </li>
              </ul>
            </div>
          `
        }
      }
    });
  </script>
```

### 组件案例

#### 1. 根据组件化方式 实现业务需求

![image-20210419172819932](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210419172819932.png)

- 根据业务功能进行组件化划分
  1. ​	标题组件（展示文本）
  2. ​    列表组件（列表展示，商品数量变更，商品删除）
  3. ​    结算组件（计算商品总额）

#### 2.功能实现步骤

- 实现整体布局和样式效果
- 划分独立的功能组件
- 组合所有的子组件形成整体结构
- 逐个实现各个组件功能
  - 标题组件
  - 列表组件
  - 结算组件

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <style type="text/css">
    .container {}

    .container .cart {
      width: 300px;
      margin: auto;
    }

    .container .title {
      background-color: lightblue;
      height: 40px;
      line-height: 40px;
      text-align: center;
      /*color: #fff;*/
    }

    .container .total {
      background-color: #FFCE46;
      height: 50px;
      line-height: 50px;
      text-align: right;
    }

    .container .total button {
      margin: 0 10px;
      background-color: #DC4C40;
      height: 35px;
      width: 80px;
      border: 0;
    }

    .container .total span {
      color: red;
      font-weight: bold;
    }

    .container .item {
      height: 55px;
      line-height: 55px;
      position: relative;
      border-top: 1px solid #ADD8E6;
    }

    .container .item img {
      width: 45px;
      height: 45px;
      margin: 5px;
    }

    .container .item .name {
      position: absolute;
      width: 90px;
      top: 0;
      left: 55px;
      font-size: 16px;
    }

    .container .item .change {
      width: 100px;
      position: absolute;
      top: 0;
      right: 50px;
    }

    .container .item .change a {
      font-size: 20px;
      width: 30px;
      text-decoration: none;
      background-color: lightgray;
      vertical-align: middle;
    }

    .container .item .change .num {
      width: 40px;
      height: 25px;
    }

    .container .item .del {
      position: absolute;
      top: 0;
      right: 0px;
      width: 40px;
      text-align: center;
      font-size: 40px;
      cursor: pointer;
      color: red;
    }

    .container .item .del:hover {
      background-color: orange;
    }
  </style>
</head>

<body>
  <div id="app">
    <div class="container">
      <my-cart></my-cart>
    </div>
  </div>
  <script type="text/javascript" src="../../vue/vue.js"></script>
  <script type="text/javascript">

    var CartTitle = {
      props: ['uname'],
      template: `
        <div class="title" >{{uname}}的商品</div>
      `
    }
    var CartList = {
      props: ['list'],
      methods: {
        del(id) {
          // 删除商品  我们一般不在子组件里面操作父组件传递过来的数据
          // 将要删除的数据 通过自定义事件传递出去 通知父组件删除
          this.$emit('cart-del', id);
        },
        // 失去焦点时修改商品数量
        changeNum(id, value) {
          // 将id传递给父组件 父组件进行修改
          this.$emit("change-num", { id, type: "change", value });
        },
        sub(id) {
          // 加法和减法我们的触发事件使用同一个
          // 所以我们执行操作的时候需要传递一个标志，是加法还是减法
          this.$emit("change-num", { id, type: 'sub' });
        },
        add(id) {
          this.$emit("change-num", { id, type: 'add' });
        }
      },
      template: `
        <div>
          <div :key='item.id' v-for="item in list" class="item">
            <img :src="item.img"/>
            <div class="name">{{item.name}}</div>
            <div class="change">
              <a href='#' @click.prevent='sub(item.id)'>－</a>
              <input type="text" class="num" @blur='changeNum(item.id,$event.target.value)' :value='item.num' />
              <a href="" @click.prevent='add(item.id)'>＋</a>
            </div>
            <div class="del" @click="del(item.id)">×</div>
          </div>
        </div>
      `
    }
    var CartTotal = {
      props: ['list'],
      template: `
        <div class="total">
          <span>总价：{{total}}</span>
          <button>结算</button>
        </div>
      `,
      computed: {
        // 使用计算属性完成总价的计算
        total() {
          // 计算商品总价
          let sum = 0;
          this.list.forEach(item => {
            sum += item.price * item.num;
          });
          return sum;
        }
      },

    }
    Vue.component('my-cart', {
      data: function () {
        return {
          uname: "张三",
          list: [{
            id: 1,
            name: 'TCL彩电',
            price: 1000,
            num: 1,
            img: 'img/a.jpg'
          }, {
            id: 2,
            name: '机顶盒',
            price: 1000,
            num: 1,
            img: 'img/b.jpg'
          }, {
            id: 3,
            name: '海尔冰箱',
            price: 1000,
            num: 1,
            img: 'img/c.jpg'
          }, {
            id: 4,
            name: '小米手机',
            price: 1000,
            num: 1,
            img: 'img/d.jpg'
          }, {
            id: 5,
            name: 'PPTV电视',
            price: 1000,
            num: 2,
            img: 'img/e.jpg'
          }]
        }
      },
      template: `
        <div class='cart'>
          <cart-title :uname='uname'></cart-title>
          <cart-list :list='list' @change-num="changeNum($event)" @cart-del="delCart($event)"></cart-list>
          <cart-total :list='list'></cart-total>
        </div>
      `,
      components: {
        'cart-title': CartTitle,
        'cart-list': CartList,
        'cart-total': CartTotal
      },
      methods: {
        delCart(id) {
          // 根据子组件传递出来的 id  找到要删除的数据
          // 将那个对应的数据进行删除
          // 找到id对应的数据的索引
          const index = this.list.findIndex(item => {
            return item.index === id;
          });
          this.list.splice(index, 1);
        },
        changeNum(shopObj) {
          // 分为三种情况进行处理
          // 1. 输入域变更
          // 2. 加号变更
          // 3. 减号变更
          if (shopObj.type === "change") {
            // 根据子组件传递过来的值更新最新的商品数量
            this.list.some(item => {
              if (item.id === shopObj.id) {
                item.num = shopObj.value;
                // 终止遍历
                return true;
              }
            });
          } else if (shopObj.type === "add") {
            // 执行加 1 操作
            this.list.some(item => {
              if (item.id === shopObj.id) {
                item.num ++;
                // 终止遍历
                return true;
              }
            });
          } else if (shopObj.type === "sub") {
            // 执行减 1 操作
            this.list.some(item => {
              if (item.id === shopObj.id) {
                item.num --;
                // 终止遍历
                return true;
              }
            });
          }

        }
      },
    });
    var vm = new Vue({
      el: '#app',
      data: {

      }
    });

  </script>
</body>

</html>
```







## 前后端交互

### 前后端交互模式

#### 1. 接口调用方式

- 原生ajax （XMLHttpRequest）
- 基于jQuery封装的ajax
- fetch （ajax的升级版，一种新的异步调用方式）
- axios （第三方库，比fetch更强）

![image-20210419211720487](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210419211720487.png)

#### 2. URL 地址的格式

##### 2.1 传统形式的URL

- 格式： **schema://host:port/path?query#fragment**

  - schema: 协议  例如：http，https等
  - host： 域名或者IP地址
  - port:  端口号，http默认端口80，可以省略
  - path： 路径，例如 /ab/c/d
  - query:   查询字符串（查询参数）例如：name=张三&age=22
  - fragment：锚点（哈希Hash） 用于定位页面的某个位置

- 符合规则的URL

  ①http://www.itcast.cn

  ②http://www.itcast.cn/java/web

  ③http://www.itcast.cn/java/web?flag=1

  ④http://www.itcast.cn/java/web?flag=1#function

##### 2.2 Restful 形式的URL

**这种形式的URL与提交形式密切相关**

- HTTP 请求的方式
  1. GET   查询
  2. POST  添加
  3. PUT   修改
  4.    DELETE 删除

- 符合规则的URL地址

  **同一个地址，但是提交方式不同，请求的资源就不同**	

  这种形式的传参，可以直接在地址后面传递一个参数，然后服务器端进行接收

  **http://www.mao.com/add/:id**

  ①http://www.hello.com/books     GET

  ②http://www.hello.com/books     POST

  修改 id 为 123 的这本书

  ③http://www.hello.com/books/123  PUT

  删除 id 为 123 的这本书

  ④http://www.hello.com/books/123  DELETE



### Promise 用法

#### 1. 异步调用

- 异步效果分析
  - 定时任务
  - Ajax
  - 事件函数
- 多次异步调用的依赖分析
  - 多次异步调用的结果顺序不确定
  - 异步调用结果如果存在依赖需要嵌套

```js
$.ajax({
  success: function(data){
    if(data.status == 200){
      $.ajax({
        success: function(data){
          if(data.status == 200){
            $.ajax({
              success: function(data){
                if(data.status == 200){}
              }
            });
          }
        }
      });
    }
  }
});
```

#### 2. Promise 概述

**Promises是异步编程的一种解决方案，从语法上讲，Promise是一个对象（也是构造函数），从它可以获取异步操作的消息。**

使用Promise的好处：

- 可以避免多层异步调用嵌套问题(回调地狱)
- Promise 对象提供了简洁的API，使得控制异步操作更加容易

官网：**https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise**

*单词 Promise 许诺 允诺*



#### 3. Promise基本用法

- 实例化 Promise 对象，构造函数中传递函数，该函数中用于处理异步任务。
- 通过传递的函数的两个参数，resolve 和 reject 两个参数，用于处理成功和失败两种情况，并通过实例对象的then方法获取处理结果。

```js
const p = new Promise(function(resolve, reject){
    // 成功时调用 resolve();
    // 失败时调用 reject();
});
p.then(function(ret){
    // 从resolve得到正常的结果
}, function(ret){
    // 从 reject 得到错误的消息
})
```



```js
// 单词 Promise 许诺 允诺
    console.log(typeof Promise); // function
    console.dir(Promise);

    // 创建 Promise的实例
    const p = new Promise(function (resolve, reject) {
      // 这里用于实现异步任务
      setTimeout(function () {
        let flag = false;
        if (flag) {
          // 正常情况
          // resolve()里面的参数就是我们传递出去的参数 
          resolve("成功！");
        } else {
          reject("失败！");
        }
      }, 1000);
    });
    p.then(function (data) {
      // data 就是处理成功时 拿到的参数
      console.log(data);
    }, function (info) {
      // info 是处理错误时传递的参数
      console.log(info);
    })
```



#### 4. 基于Promise处理ajax请求

##### 4.1 处理原生Ajax

```js
 function queryData(){
  return new Promise(function(resolve,reject){
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function(){
      if(xhr.readyState !=4) return;
      if(xhr.status == 200) {
        resolve(xhr.responseText)
      }else{
        reject('出错了');
      }
    }
    xhr.open('get', '/data');
    xhr.send(null);
  })
 }
queryData("http://localhost:3000/data").then(function(data){
      console.log(data);
    },function(info){
      console.log(info);
    })
```

##### 4.2 发送多次Ajax请求

```js
 queryData()
  .then(function(data){
    return queryData();
  })
  .then(function(data){
    return queryData();
  })
  .then(function(data){
    return queryData();
  });

```

```html
<script>

    // 基于 Promise发送多个ajax请求，并且保证顺序
    function queryData(url){
      // 基于 Promise 发送Ajax请求
    const p = new Promise((resolve, reject) => {
      // 创建异步请求对象
      const xhr = new XMLHttpRequest();
      // 监听xhr.onreadystatechange 事件 
      // 当服务器响应
      xhr.onreadystatechange = ()=>{
        // 监听 xhr对象的请求状态 readyState  与服务器响应状态 status
        if(xhr.readyState!==4){
          return;
        }
        if(xhr.readyState===4 && xhr.status === 200){
          // 处理正常的情况
          resolve(xhr.responseText);
        }else{
          // 发起请求成功 但是服务器响应错误
          reject("服务器内部错误！");
        }
        
      }
      // 指定请求方法和地址
      xhr.open("GET",url);
      // 发起请求
      xhr.send(null);
    });
    return p;
    }

    // 基于 Promise发送多个ajax请求，并且保证顺序
    queryData("http://localhost:3000/data").then((data)=>{
      console.log(data);
      // 在第一次处理完请求以后，调用发起第二次请求
      // 并将第二次发起的请求返回出去
      return queryData("http://localhost:3000/data1");
    }).then((data)=>{
      // 这里的回调处理函数，处理的是上面第一次请求完成后发起的第二次请求的处理函数
      console.log(data);
      return queryData("http://localhost:3000/data2");
    }).then((data)=>{
      console.log(data);
    })
  </script>
```

##### 4.3 then函数的参数中的函数返回值

###### 1. 返回Promise实例对象

- 返回的该实例对象会调用下一个then函数

###### 2. 返回普通值

- 返回的普通值会直接传递给下一个then函数，通过then函数 参数中的函数的参数接收该值。

```js
queryData("http://localhost:3000/data").then((data)=>{
      console.log(data);
      // 调用新的queryData函数 发起新的请求
      return queryData("http://localhost:3000/data1");
    }).then((data)=>{
      // 处理上面返回的第一个请求
      console.log(data);
      // 创建一个新的 Promise 实例对象
      return new Promise((resolve,reject)=>{
        setTimeout(function(){
          resolve("11111");
        },1000);
      })
    }).then((data)=>{
      // 这里处理的是上面创建的新的Promise实例对象的成功以后的数据
      console.log(data);// 11111
      // 这里我们返回一个普通的字符串
      // 返回一个普通值  内部会默认创建一个新的Promise对象并返回
      return "哈哈";
    }).then((data)=>{
      // 这里处理的是上面返回的具体的值
      console.log(data);
    })
```



#### 5. Promise 常用的API

##### 5.1. 实例方法

- p.then()  得到异步任务的正确结果
- p.catch()  获取异常信息
- p.finally()  成功与否都会执行（尚且不是正式标准）

```js
 queryData(url)
  .then(function(data){
    console.log(data);
  })
  .catch(function(data){
    console.log(data);
  })
  .finally(function(){
    console.log(‘finished');
  });

```

```js
 // Promise 常用的API  实例方法
    console.dir(Promise);
    // then().catch().finally()
    function foo(){
      return new Promise((resolve,reject)=>{
        setTimeout(function(){
          resolve(111);
        },1000);
        // reject("出错了！")
      });
    }
    foo()
    // then中我们只处理正常返回的成功数据
    .then((data)=>{
      console.log(data);
    })
    // catch() 我们处理发生错误等异常数据
    .catch((info)=>{
      console.log(info);
    })
    // finally() 中的代码 无论成功失败都执行
    .finally(()=>{
      console.log("finally");
    });
```

##### 5.2 对象方法（通过Promise关键字直接调用的方法）

- Promise.all() 并发处理多个异步任务，所有的任务都执行完才能得到结果
- Promise.race()  并发处理多个异步任务，只要有一个任务完成就能得到结果

```js
 Promise.all([p1,p2,p3]).then((result) => {
   console.log(result)
 })

 Promise.race([p1,p2,p3]).then((result) => {
   console.log(result)
 })

```

```js
// Promise 常用的API  对象方法
    console.dir(Promise);
    // Promise.all()  Promise.race()
    function queryData(url) {
      // 基于 Promise 发送Ajax请求
      const p = new Promise((resolve, reject) => {
        // 创建异步请求对象
        const xhr = new XMLHttpRequest();
        // 监听xhr.onreadystatechange 事件 
        // 当服务器响应
        xhr.onreadystatechange = () => {
          // 监听 xhr对象的请求状态 readyState  与服务器响应状态 status
          if (xhr.readyState !== 4) {
            return;
          }
          if (xhr.readyState === 4 && xhr.status === 200) {
            // 处理正常的情况
            resolve(xhr.responseText);
          } else {
            // 发起请求成功 但是服务器响应错误
            reject("服务器内部错误！");
          }

        }
        // 指定请求方法和地址
        xhr.open("GET", url);
        // 发起请求
        xhr.send(null);
      });
      return p;
    }
    const p1 = queryData("http://localhost:3000/a1");
    const p2 = queryData("http://localhost:3000/a2");
    const p3 = queryData("http://localhost:3000/a3");
    // Promise.all() 所有的异步任务处理完成以后才能得到结果
    // 会发起三次请求
    Promise.all([p1,p2,p3]).then((data)=>{
      // data 是一个数组  里面的元素返回值和我们传递到all函数里面的数组里面的顺序是一致的
      console.log("all()------>",data);
    });
    // Promise.race()  只要有一个异步任务执行成功就会得到返回值
    Promise.race([p1,p2,p3]).then((data)=>{
      // 虽然只有有一个异步任务执行成功就会得到返回值，但是三个异步任务还是会都执行的
      // 所以这里 data的值是第一个执行完的异步任务的返回值
      console.log("race()------>",data);
    });
```



### 接口调用-fetch用法

#### 1. 基本特性

- 更加简单的数据获取方式，功能更强大，更灵活，可以看做是xhr的升级版
- 基于Promise实现

#### 2. 语法结构

```javascript
  fetch(url).then(fn2)
            .then(fn3)
            ...
            .catch(fn)

```

**官方地址：https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API**

#### 3. fetch的基本用法

```js
  fetch('/abc').then(data=>{
      return data.text();
  }).then(ret=>{
      // 注意这里得到的才是最终的数据
      console.log(ret);
  });

```

```js
// fetch API 的基本使用
    // text() 方法 属于 fetch API 的一部分 返回的是Promise实例对象
    fetch("http://localhost:3000/fdata")
    .then((data)=>{
      // 我们不能直接拿到服务器返回的数据
      // 需要使用 data.text()方法拿到返回值
      // data.text()的返回值是一个Promise实例对象
      console.log(data);
      // 要注意 data.text() 方法获取到返回的Promise实例对象只能调用一次
      // 二次调用 text()方法 会报错
      const p = data.text();
      console.log(p);
      // 然后将得到的Promise返回，通过下一个then处理函数得到实际的返回值
      return p;
    }).then((data)=>{
      // 这里是真正的服务器返回的数据
      console.log(data);
    })
```

#### 4. fetch 请求参数

##### 4.1 常用配置选项

- method(String) : HTTP请求方法，默认为GET（GET，POST，PUT，DELETE）
- body(String) :   HTTP 的请求参数
- headers(Object)  :  HTTP的请求头，默认为 {}

##### 4.2 get和delete方式的参数传递

```js
 fetch('/abc' , {
    method: ‘get’
 }).then(data=>{
    return data.text();
 }).then(ret=>{
    // 注意这里得到的才是最终的数据
    console.log(ret);
 });
 fetch(‘/abc/123’,{
    method: ‘get’
 }).then(data=>{
    return data.text();
 }).then(ret=>{
    // 注意这里得到的才是最终的数据
    console.log(ret);
 });
 fetch(‘/abc/123' ,{
    method: ‘delete’
 }).then(data=>{
    return data.text();
 }).then(ret=>{
    // 注意这里得到的才是最终的数据
    console.log(ret);
 });

```

```js
// GET 请求方式的参数传递
    fetch("http://localhost:3000/books?id=1", {
      // 默认是get方式 可以不写
      method: 'get'
    }).then((data) => {
      return data.text();
    }).then((data) => {
      console.log(data);
    })

    // 动态传参  后台地址为 /books/:id
    fetch("http://localhost:3000/books/456", {
      // 默认是get方式 可以不写
      method: 'get'
    }).then((data) => {
      return data.text();
    }).then((data) => {
      console.log(data);
    })

    // delete 请求方式
    fetch("http://localhost:3000/books/789", {
      method: 'delete'
    }).then((data) => {
      return data.text();
    }).then((data) => {
      console.log(data);
    })
```

##### 4.3 post和put方式请求的参数传递

```js
 fetch(‘/books' ,{
    method: ‘post’,
    body: ‘uname=lisi&pwd=123’,
    headers: {
       'Content-Type': 'application/x-www-form-urlencoded‘,
    }
 }).then(data=>{
    return data.text();
 }).then(ret=>{
    console.log(ret);
 });

 fetch(‘/books' ,{
    method: ‘post’,
    body: JSON.stringify({
       uname: ‘lisi’,
       age: 12
    })
    headers: {
       'Content-Type': 'application/json ‘,
    }
 }).then(data=>{
    return data.text();
 }).then(ret=>{
    console.log(ret);
 });

 fetch(‘/books/123' ,{
    method: ‘put’,
    body: JSON.stringify({
       uname: ‘lisi’,
       age: 12
    })
    headers: {
       'Content-Type': 'application/json ‘,
    }
 }).then(data=>{
    return data.text();
 }).then(ret=>{
    console.log(ret);
 });


// POST 和 PUT 方式请求
    fetch("http://localhost:3000/books",{
      method:"POST",
      // 请求体  里面是请求参数
      body:'uname=李四&pwd=111',
      // 设置请求头
      headers:{
        // 表单类型数据
        'Content-Type':'application/x-www-form-urlencoded'
      }
    }).then((data)=>{
      return data.text();
    }).then((data)=>{
      console.log(data);
    });

    fetch("http://localhost:3000/books",{
      method:"POST",
      // 请求体  里面是请求参数  json 类型的传参
      body:JSON.stringify({
        uname:"毛毛",
        pwd:'111'
      }),
      // 设置请求头
      headers:{
        // json类型数据
        'Content-Type':'application/json'
      }
    }).then((data)=>{
      return data.text();
    }).then((data)=>{
      console.log(data);
    });
    // PUT 方式请求
    fetch("http://localhost:3000/books/123",{
      method:"PUT",
      // 请求体  里面是请求参数  json 类型的传参
      body:JSON.stringify({
        uname:"毛毛",
        pwd:'111'
      }),
      // 设置请求头
      headers:{
        // 表单类型数据
        'Content-Type':'application/json'
      }
    }).then((data)=>{
      return data.text();
    }).then((data)=>{
      console.log(data);
    });
```

#### 5. fetch 响应结果

响应数据格式：

- text(): 将返回体处理成字符串类型
- json():  返回结果和JSON.parse(responseText)一样

```js
fetch("http://localhost:3000/json").then((data) => {
      // 返回json格式的数据（JSON是对象）
      return data.json();
    }).then((data) => {
      console.log(data);
      console.log(typeof data);
    })
```

### 接口调用 axios用法

#### 1. axios 的基本特性

**axios（官网：https://github.com/axios/axios）是一个基于Promise 用于浏览器和 node.js 的 HTTP 客户端。**

它具有以下特征：

- 支持浏览器和 node.js
- 支持 promise
- 能拦截请求和响应
- 自动转换 JSON 数据

#### 2. axios的基本用法

```js
  axios.get(‘/adata')
       .then(ret=>{
          // data属性名称是固定的，用于获取后台响应的数据
          console.log(ret.data)
       })

 axios.get("http://localhost:3000/adata").then((ret)=>{
      // ret 是axios给我们封装了的json对象
      console.log(ret);
      // ret.data  是我们真实的服务器返回过来的数据
      console.log(ret.data);
    })
```

#### 3. axios的常用API

- get： 查询数据
- post： 添加数据
- put：  修改数据
- delete： 删除数据

#### 4. axios的参数传递

##### 4.1 GET传递参数

- 通过URL传递参数
- 通过params选项传递参数

```js
// 发起get请求  参数以查询字符串的形式 直接书写在url地址后面
    axios.get("http://localhost:3000/axios?id=123").then(ret => {
      console.log(ret.data);
    });
    // 也可以 将参数形式改为动态传参  
    // 也就是 Restful 形式的地址传参 后台地址为 /axios/:id 或者 /:id/:name 多个参数
    axios.get("http://localhost:3000/axios/1").then(ret => {
      console.log(ret.data);
    });
    // 方式二 通过params选项传参
    axios.get("http://localhost:3000/axios",{
      params:{
        id:111,
        name:"11"
      }
    }).then(ret => {
      console.log(ret.data);
    });
```

##### 4.2 DELETE 传递参数

- 与GET方式基本一致

```js
  axios.delete(‘/adata?id=123')
       .then(ret=>{
          console.log(ret.data)
       })
  axios.delete(‘/adata/123')
       .then(ret=>{
          console.log(ret.data)
       })
  axios.delete(‘/adata‘,{
          params: {
            id: 123
          }
       })
       .then(ret=>{
          console.log(ret.data)
       })

// DELETE 方式传参  和get基本一致
    axios.delete("http://localhost:3000/axios", {
      params: {
        id: 1
      }
    }).then(ret => {
      console.log(ret.data);
    });
    axios.delete("http://localhost:3000/axios/222").then(ret => {
      console.log(ret.data);
    });
```

##### 4.3 POST 的参数传递

- 通过选项传递参数（默认传递的是json格式的数据）
- *通过 URLSearchParams实例对象传参*（数据格式是表单类型数据 application/x-www-form-urlencoded）

```js
// post传递参数
    axios.post("http://localhost:3000/axios", {
      'uname': "毛毛",
      pwd: "111111111111"
    }).then((ret) => {
      console.log(ret.data);
    })
    // post方式 通过 URLSearchParams实例对象传参
    const params = new URLSearchParams();
    params.append("uname", "哈哈");
    params.append("pwd", "sss");
    axios.post("http://localhost:3000/axios", params).then((ret) => {
      console.log(ret.data);
    })
```



##### 4.4 PUT 的参数传递

- 参数传递的方式和POST有些类似

```js
// put请求 和post类似  传递参数 还是在地址上动态拼接
    axios.put("http://localhost:3000/axios/99", {
      'uname': "毛毛",
      pwd: "111111111111"
    }).then((ret) => {
      console.log(ret.data);
    })
```

#### 5. axios 的响应结果

##### 响应结果的主要属性

- data ： 实际响应回来的数据
- headers：响应头信息
- status： 响应状态码
- statusText： 响应状态信息

```js
// 服务器返回json格式的数据
    axios.get("http://localhost:3000/axios-json").then((ret) => {
      console.log(ret.data);
    })
```

#### 6. axios的全局配置

- axios.default.timeout = 3000; // 设置超时时间
- axios.default.baseURL = “http://localhost:3000/app”  //  基准地址，后面发起请求时地址不需要每次都携带前面的这个路径。
- axios.default.headers[‘’mytoken‘’] = “aaaaaaaaall”  // 设置请求头（jwt验证）

#### 7. axios拦截器

##### 7.1 请求拦截器

在发出请求之前设置一些信息。发起请求之前会经过请求拦截器

![image-20210421091852019](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210421091852019.png)

```js
  //添加一个请求拦截器
  axios.interceptors.request.use(function(config){
    //在请求发出之前进行一些信息设置
    return config;
  },function(err){
    // 处理响应的错误信息
  });

// 在请求之前设计一个请求拦截器
    axios.interceptors.request.use(function (config) {
      console.log(config.url);
      config.url = "http://localhost:3000" + config.url;
      config.headers.mytoken = '1111111111';
      // 需要将外面的配置返回出去，这样才能生效
      return config;
    }, err => {
      console.log(err);
    });
    axios.get("/adata").then((data) => {
      console.log(data.data);
    })
```

##### 7.2 响应拦截器

在获取数据之前对数据做一些加工处理

![image-20210421092548345](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210421092548345.png)

```js
  //添加一个响应拦截器
  axios.interceptors.response.use(function(res){
    //在这里对返回的数据进行处理
    return res;
  },function(err){
    // 处理响应的错误信息
  })

// 设置响应拦截器
    axios.interceptors.response.use(res => {
      console.log(res);
      // 我们可以直接返回服务器返回过来的真实数据
      // 这里对服务器响应的数据进行处理以后  任然需要返回
      return res.data;
    })

    axios.get("/adata").then((data) => {
      // 我们可以直接拿到真实的数据 不需要data.data 获取了  在响应拦截器中进行处理了
      console.log(data);
    })
```



### 接口调用-async/await用法

#### 1. async/await的基本用法

- async/await 是ES7引入的新语法，可以更方便的进行异步操作
- async关键字用于函数上(async函数的返回值是Promise实例对象)
- await关键字用于async函数中（await可以得到异步的结果）

```js
  async function queryData(id) {
    const ret = await axios.get('/data');
    return ret;
  }
  queryData.then(ret=>{
    console.log(ret)
  })


// async和await处理异步任务
    axios.get("http://localhost:3000/adata").then((ret)=>{
      console.log(ret.data);
    });
    // 使用async和await关键字
    async function queryData(){
      // ret 就是得到的axios帮我们封装好的响应数据
      // 我们可以直接使用  也可以在这个函数里面返回 
      // await 后面跟的是Promise的实例对象
      const ret = await axios.get("http://localhost:3000/adata");
      return ret;
    }
    queryData().then((ret)=>{
      console.log(typeof ret);
      console.log(ret.data);
    });

    async function queryData2(){
      // await 后面跟的是Promise的实例对象
      const ret = await new Promise((resolve,reject)=>{
        setTimeout(()=>{
          resolve("成功！");
        },1000);
      });
      return ret;
    };
    queryData2().then((data)=>{
      console.log(data);
    })
```

#### 2. async/await 处理多个异步任务

**多个异步请求的场景**

```js
  async function queryData(id) {
    const info = await axios.get('/async1');
    const ret = await axios.get(‘async2?info=‘+info.data);
    return ret;
  }
  queryData.then(ret=>{
    console.log(ret)
  })

async function queryData() {
      // await 是用来得到后面异步请求结果的关键字 等待
      const info = await axios.get("http://localhost:3000/async1");
      // 第一个异步任务的结果 作为第二个异步任务的参数进行传递
      const res = await axios.get("http://localhost:3000/async2?info=" + info.data);
      return res.data;
    };
    queryData().then(data => {
      console.log(data);
    })
```

### 基于接口的案例分析

- 图书相关的操作基于后台接口数据进行操作

- 需要调用接口的功能点

  ①图书列表数据加载           GET    http://localhost:3000/books

  ②添加图书                  POST   http://localhost:3000/books

  ③验证图书名称是否存在        GET    http://localhost:3000/books/book/:name

  ④编辑图书-根据ID查询图书信息  GET    http://localhost:3000/books/:id

  ⑤编辑图书-提交图书信息       PUT    http://localhost:3000/books/:id

  ⑥删除图书                  DELETE  http://localhost:3000/books/:id







## 前端路由

### 路由

路由是一个比较广义和抽象的概念，路由的本质就是对应关系。

在开发中，路由分为：

- 后端路由
- 前端路由

#### 1. 后端路由

##### 1.1. 概念

**概念：** 根据不同的用户URL请求，返回不同的内容

**本质：**URL请求地址与服务器资源之间的对应关系

![image-20210421125049386](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210421125049386.png)



##### 1.2. SPA（Single Page Application）

- 后端渲染（存在性能问题）
- Ajax前端渲染（前端渲染提高性能，但是不支持浏览器的前进后退操作）
- SPA单页面应用程序：整个网站只有一个页面，内容的电话通过Ajax局部更新实现，同时支持浏览器地址栏的前进和后退操作
- SPA实现原理之一： 基于URL地址的hash（hash【类似于锚点链接】的变化会导致浏览器记录访问历史的变化，但是hash的变化不能触发新的URL请求）
- 在实现SPA过程中，最核心的技术点就是前端路由

#### 2. 前端路由

##### 2.1. 概念 

**概念：**根据不同的用户事件，显示不同的页面内容

**本质：**用户事件与事件处理函数之间的对应关系

![image-20210421130440621](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210421130440621.png)

#### 3. 实现简易的前端路由

- 基于URL中的hash实现（点击菜单的时候改变URL的hash，根据hash的变化控制组件的切换）

![image-20210421130731917](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210421130731917.png)

```js
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>

</head>
<body>
  <!-- 被 vue 实例控制的 div 区域 -->
  <div id="app">

    <!-- 切换组件的超链接 -->
    <a href="#/zhuye">主页</a>
    <a href="#/keji">科技</a>
    <a href="#/caijing">财经</a>
    <a href="#/yule">娱乐</a>

    <!-- 根据 :is 属性指定的组件名称，把对应的组件渲染到 component 标签所在的位置 -->
    <!-- 可以把 component 标签当做是【组件的占位符】 -->
    <component :is="comName"></component>
  </div>
  <script src="../vue/vue.js"></script>
  <script>
    // #region 定义需要被切换的 4 个组件
    // 主页组件
    const zhuye = {
      template: '<h1>主页信息</h1>'
    }

    // 科技组件
    const keji = {
      template: '<h1>科技信息</h1>'
    }

    // 财经组件
    const caijing = {
      template: '<h1>财经信息</h1>'
    }

    // 娱乐组件
    const yule = {
      template: '<h1>娱乐信息</h1>'
    }
    // #endregion

    // #region vue 实例对象
    const vm = new Vue({
      el: '#app',
      data: {
        comName: 'keji'
      },
      // 注册私有组件
      components: {
        zhuye,
        keji,
        caijing,
        yule
      }
    })
    // #endregion

    // 监听 window 的 onhashchange 事件，根据获取到的最新的 hash 值，切换要显示的组件的名称
    window.onhashchange = function() {
      // 通过 location.hash 获取到最新的 hash 值
      console.log(location.hash.slice(2));
      /* switch(location.hash.slice(2)){
        case 'keji':
          vm.comName = 'keji'
      } */
      vm.comName = location.hash.slice(2)
    }
  </script>
</body>
</html>
```

#### 3. Vue Router

**Vue Router**（官网：https://router.vuejs.org/zh/）是 Vue.js 官方的路由管理器。

它和 Vue.js 的核心深度集成，可以非常方便的用于SPA应用程序的开发。

Vue Router 包含的功能有：

- 支持HTML5历史模式或hash模式
- 支持嵌套路由
- 支持路由参数
- 支持编程式路由
- 支持命名路由

##### 3.1 Vue-router的基本使用

1. 引入相关的库文件
2. 添加路由链接
3. 添加路由填充位
4. 定义路由组件
5. 配置路由规则并创建路由实例
6. 把路由挂载到Vue根实例中

###### 3.1.1 引入相关的库文件

**一定要先导入vue文件，在导入vue-router文件**

```html
<!-- 导入 vue 文件，为全局 window 对象挂载 vue 构造函数 -->
<script src='vue.js'></script>
<!-- 导入vue-router 为全局 window 对象挂载 VueRouter 构造函数  -->
<script src='vue-router.js'></script>
```

###### 3.1.2 添加路由链接

```html
<!-- router-link 是 vue 中提供的标签，默认会被渲染为 a 标签 -->
<!-- to 属性默认会被渲染为 href 属性 -->
<!-- to 属性的值默认会被渲染为 # 开头的 hash 地址 -->
<router-link to="/user">User</router-link>
<router-link to="/register">Register</router-link>
```

###### 3.1.3 添加路由填充位

```html
<!-- 路由填充位（也叫做路由占位符） -->
<!-- 将来通过路由规则匹配到的组件，将会被渲染到 router-view 所在的位置 --> 
<router-view></router-view>
```

###### 3.1.4 定义路由组件

```js
var User = {
 template: '<div>User</div>'
 }
 var Register = {
 template: '<div>Register</div>'
 }
```

###### 3.1.5 配置路由规则并创建路由实例

```js
// 创建路由实例对象 
 var router = new VueRouter({
 // routes 是路由规则数组
 routes: [
 // 每个路由规则都是一个配置对象，其中至少包含 path 和 component 两个属性：
 // path 表示当前路由规则匹配的 hash 地址
 // component 表示当前路由规则对应要展示的组件
 {path:'/user',component: User},
 {path:'/register',component: Register}
 ]
 })
```

###### 3.1.6 把路由挂载到 Vue 根实例中

```js
 new Vue({
 el: '#app',
 // 为了能够让路由规则生效，必须把路由对象挂载到 vue 实例对象上
 router
 })
```

##### 3.2 路由重定向

路由重定向： 用户在访问地址A的时候，强制用户跳转到地址C，从而展示特定的组件页面；通过路由规则的redirect属性，指定一个新的路由地址，可以很方便地设置路由的重定向:

```js
var router = new VueRouter({
 routes: [
 // 其中，path 表示需要被重定向的原地址，redirect 表示将要被重定向到的新地址
 {path:'/', redirect: '/user'},
 {path:'/user',component: User},
 {path:'/register',component: Register}
 ]
 })
```

##### 3.3 嵌套路由

###### 3.3.1 嵌套路由功能分析

- 点击父级路由链接显示模板内容
- 模板内容中又有子级路由链接
- 点击子级路由链接显示子级模板内容

###### 3.3.2 父路由组件模板

- 父级路由链接
- 父组件路由填充位

```html
<p>
 <router-link to="/user">User</router-link>
 <router-link to="/register">Register</router-link>
 </p>
 <div>
 <!-- 控制组件的显示位置 -->
 <router-view></router-view>
</div>
```

###### 3.3.3 子级路由模板

- 子级路由链接
- 子级路由填充位

```html
const Register = {
 template: `<div>
 <h1>Register 组件</h1>
 <hr/>
 <router-link to="/register/tab1">Tab1</router-link>
 <router-link to="/register/tab2">Tab2</router-link>
 <!-- 子路由填充位置 -->
 <router-view/>
 </div>`
 }
```

###### 3.3.4 嵌套路由配置

- 父级路由通过children属性配置子级路由

```js
const router = new VueRouter({
 routes: [
 { path: '/user', component: User },
 {
 path: '/register',
 component: Register,
 // 通过 children 属性，为 /register 添加子路由规则
 children: [
 { path: '/register/tab1', component: Tab1 },
 { path: '/register/tab2', component: Tab2 }
 ]
 }
 ]
 })
```

#### 4. vue的动态路由匹配

##### 4.1 动态匹配路由的基本用法

**思考：**

```html
<!--有如下三个路由链接-->
<router-link to="/user/1">User1</router-link>
<router-link to="/user/2">User2</router-link>
<router-link to="/user/3">User3</router-link>

<script>
// 定义如下三个对应的路由规则，是否可行？？？
{ path: '/user/1', component: User }
{ path: '/user/2', component: User }
{ path: '/user/3', component: User }
</script>
```

**应用场景：通过动态路由参数的模式进行洛阳匹配**

```js
const router  =  new VueRouter({
    routes:[
        // 动态路径参数 以冒号开头
 { path: '/user/:id', component: User }
    ]
})

const User = {
 // 路由组件中通过$route.params获取路由参数
 template: '<div>User {{ $route.params.id }}</div>'
}
```

```html
<div id="app">
    <router-link to='/user/1'>user1</router-link>
    <router-link to='/user/2'>user2</router-link>
    <router-link to='/user/3'>user3</router-link>

    <router-view></router-view>
  </div>
  <script>
    const User = {
      // 动态路由匹配 可以使用 {{ $route.params.id }} 获取动态传递的id属性
      template: "<h3>User {{$route.params.id}}</h3>"
    }
    const router = new VueRouter({
      routes: [
        { path: '/', redirect: "/user1" },
        // 动态路由匹配，将前面一样的地址定下来，后面的动态参数 以 /:params 的形式进行传递
        { path: "/user/:id", component: User }
      ]
    });
    const app = new Vue({
      el: "#app",
      router
    })
  </script>
```



##### 4.2 路由组件传递参数

$route与对应路由形成高度耦合，不够灵活，所以可以使用props将组件和路由解耦

###### 4.2.1 props 的值为布尔值

```js
const router = new VueRouter({
 routes: [
 // 如果 props 被设置为 true，route.params 将会被设置为组件属性
 { path: '/user/:id', component: User, props: true }
 ]
 })
const User = {
 props: ['id'], // 使用 props 接收路由参数
 template: '<div>用户ID：{{ id }}</div>' // 使用路由参数
 }

```

###### 4.2.2 props的值为对象类型

```js
const router = new VueRouter({
 routes: [
 // 如果 props 是一个对象，它会被按原样设置为组件属性
 { path: '/user/:id', component: User, props: { uname: 'lisi', age: 12 }}
 ]
 })
const User = {
 props: ['uname', 'age'],
 template: ‘<div>用户信息：{{ uname + '---' + age}}</div>'
 }
```

###### 4.2.3 props的值为函数类型

```js
const router = new VueRouter({
 routes: [
 // 如果 props 是一个函数，则这个函数接收 route 对象为自己的形参
 { path: '/user/:id', 
 component: User, 
 props: route => ({ uname: 'zs', age: 20, id: route.params.id })}
 ]
 })
/*
上面的函数体写法也可以写成下面这种，也就是小括号代表了return语句了在这里
path: "/user/:id", component: User, props: route => {
            return {
              name: "毛毛",
              age: 22,
              id: route.params.id
            }
          }

*/
const User = {
 props: ['uname', 'age', 'id'],
 template: ‘<div>用户信息：{{ uname + '---' + age + '---' + id}}</div>'
 }
```

##### 4.3 命名路由

###### 4.3.1 命名路由的规则匹配

为了更加方便的表示路由的路径，可以给路由规则起一个别名，也就是‘ 命名路由 ’。

```js
const router = new VueRouter({
 routes: [
 {
 path: '/user/:id',
 name: 'user',
 component: User
 }
 ]
 })
```

```html
<router-link :to="{ name: 'user', params: { id: 123 }}">User</router-link>
 router.push({ name: 'user', params: { id: 123 }})
```



##### 4.4. 页面导航的两种方式

- 声明式导航：通过点击链接实现导航的方式，叫做声明式导航 例如：普通网页中的  链接 或 vue 中的 
- 编程式导航：通过调用JavaScript形式的API实现导航的方式，叫做编程式导航 例如：普通网页中的 location.href

##### 4.5 编程式导航的基本用法

常用的编程式导航API如下：

- this.$router.push('hash地址')
- this.$router.go(n)

```js
const User = {
 template: '<div><button @click="goRegister">跳转到注册页面</button></div>',
 methods: {
 goRegister: function(){
 // 用编程的方式控制路由跳转
 this.$router.push('/register');
 }
 }
 }
```

##### 4.6 编程式导航参数规则

**router.push() 方法的参数规则**

```js
// 字符串(路径名称)
 router.push('/home')
 // 对象
 router.push({ path: '/home' })
 // 命名的路由(传递参数)
 router.push({ name: '/user', params: { userId: 123 }})
 // 带查询参数，变成 /register?uname=lisi
 router.push({ path: '/register', query: { uname: 'lisi' }}
```

##### 5. vue-router 案例

![image-20210422083341959](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210422083341959.png)

**用到的路由技术点：**

- 路由的基础用法
- 嵌套路由
- 路由重定向
- 路由传参
- 编程式导航

**根据项目的整体布局划分好组件结构，通过路由导航控制组件的显示**

1. 抽离并渲染 App 根组件
2. 将左侧菜单改造为路由链接
3. 创建左侧菜单对应的路由组件
4. 在右侧主体区域添加路由占位符
5. 添加子路由规则
6. 通过路由重定向默认渲染用户组件
7. 渲染用户列表数据
8. 编程式导航跳转到用户详情页
9. 实现后退功能





## 前端模块（模块）化

### 1. 模块化相关规范

#### 1.1 模块化概述

**传统开发模式的主要问题**：

1. 命名冲突
2. 文件依赖
3. **通过模块化解决上述问题**
   - 模块化就是把单独的一个功能封装到一个模块（文件）中，模块之间相互隔离，但是可以通过特定的接口公开内部成 员，也可以依赖别的模块。
   - 模块化开发的好处：方便代码的重用，从而提升开发效率，并且方便后期的维护

#### 1.2 浏览器端模块化规范

##### 1. AMD

Require.js(http://www.requirejs.cn/)

##### 2. CMD

Sea.js(http://seajs.github.io/seajs/docs/)

#### 1.3 服务器端模块化规范

##### 1. CommonJS

- 模块分为 单文件模块 与 包
- 模块成员导出： module.exports 和 exports
- 模块成员导入： require(“模块标识符”)

#### 1.4 大一统的模块化规范- ES6模块化

在 ES6 模块化规范诞生之前，Javascript 社区已经尝试并提出了 AMD、CMD、CommonJS 等模块化规范。

但是，这些社区提出的模块化标准，还是存在一定的差异性与局限性、并不是浏览器与服务器通用的模块化标准，例如：

- AMD 和 CMD 适用于浏览器端的 Javascript 模块化
- CommonJS 适用于服务器端的 Javascript 模块化

因此，ES6 语法规范中，在语言层面上定义了 ES6 模块化规范，是浏览器端与服务器端通用的模块化开发规范。

ES6模块化规范中定义：

- 每个 js 文件都是一个独立的模块
- 导入模块成员使用 import 关键字
- 暴露模块成员使用 export 关键字

##### 1. Node.js中通过babel 体验ES6模块化

```shell
# 1. 
npm install --save-dev @babel/core @babel/cli @babel/preset-env @babel/node
# 2. 
npm install --save @babel/polyfill

```

3. 项目跟目录创建文件 babel.config.js

4. babel.config.js 文件内容如下代码：

   ```js
    const presets = [
    ["@babel/env", {
    targets: {
    edge: "17",
    firefox: "60",
    chrome: "67",
    safari: "11.1"
    }
    }]
    ];
    module.exports = { presets }
   ```

5. 通过 npx babel-node index.js 执行代码

#### 1.5 ES6模块化的基本语法

##### 1. 默认导出 与 默认导入

- 默认导入语法 `export default`  默认导出的成员

```js
// 当前文件模块为 m1.js
// 定义私有成员 a 和 c
let a = 10
let c = 20
// 外界访问不到变量 d ,因为它没有被暴露出去
let d = 30
function show() {}
// 将本模块中的私有成员暴露出去，供其它模块使用
export default {
 a,
 c,
 show
}
```

- 默认导入语法 `import` 接收名称 `from` ‘模块标识符’ 

```js
// 导入模块成员
 import m1 from './m1.js'
 console.log(m1)
 // 打印输出的结果为：
 // { a: 10, c: 20, show: [Function: show] }
```

**注意：每个模块中，只允许使用唯一的一次 export default，否则会报错！**

##### 2. 按需导出 与 按需导入

- 按需导出语法 `export let s1 = 10`

```js
// 当前文件模块为 m1.js
// 向外按需导出变量 s1
export let s1 = 'aaa' 
// 向外按需导出变量 s2
export let s2 = 'ccc'
// 向外按需导出方法 say
export function say = function() {}

```

- 按需导入语法 `import { s1 } from '模块标识符'`

```js
// 导入模块成员
 import { s1, s2 as ss2, say } from './m1.js'
 console.log(s1) // 打印输出 aaa
 console.log(ss2) // 打印输出 ccc
 console.log(say) // 打印输出 [Function: say]
```

```js
// 按需导入 和 默认导出的成员同时进行导入
import m1,{e} from './m1';
```



##### 3. 直接导入并执行模块代码

有时候，我们只想单纯执行某个模块中的代码，并不需要得到模块中向外暴露的成员，此时，可以直接导入并执行模块代码

```js
// 当前文件模块为 m2.js
// 在当前模块中执行一个 for 循环操作
for(let i = 0; i < 3; i++) {
 console.log(i)
}
```

```js
 // 直接导入并执行模块代码
 import './m2.js'
```



### 2. webpack

#### 2.1 当前Web开发面临的问题

- 文件依赖关系错综复杂
- 静态资源请求效率低
- 模块化支持不友好
- 浏览器对高级JavaScript特性兼容性程度较低
- etc...

#### 2.2 webpack 概述

webpack 是一个流行的前端项目构建工具（打包工具），可以解决当前 web 开发中所面临的困境。 webpack 提供了友好的模块化支持，以及代码压缩混淆、处理 js 兼容问题、性能优化等强大的功能，从而让程序员把 工作的重心放到具体的功能实现上，提高了开发效率和项目的可维护性。 

目前绝大多数企业中的前端项目，都是基于 webpack 进行打包构建的。

![image-20210422190458324](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210422190458324.png)

#### 2.3 webpack打包工具的基本使用

**npm每次安装模块的时候，后面的 -d 的意思是，安装包会在 配置文件的 devDependencies  对象中，简称dev，是开发环境用到的。可以减少产品上线的体积。-s安装包会在 dependencies对象中，是生产环境中要用到的，运行过程中不可缺少的**

##### 1. 创建一个列表隔行变色的项目

1.  新建项目空白目录，并运行 npm init –y 命令，初始化包管理配置文件 package.json
2. 新建 src 源代码目录
3. 新建 src -> index.html 首页
4. 初始化首页基本的结构
5. 运行 npm install jquery –S 命令，安装 jQuery
6. 通过模块化的形式，实现列表隔行变色效果

##### 2. 在项目中安装和配置webpack

1. 运行 `npm  i webpack webpack-cli -D` 命令，安装webpack相关的包

2. 在项目根目录中，创建名为 webpack.config.js 的 webpack 配置文件

3. 在 webpack 的配置文件中，初始化如下基本配置：

   ```js
   // webpack配置文件
   module.exports = {
     // mode  指定构建模式  development 表示通过开发模式来构建
     // 开发模式 我们代码不会进行压缩与混淆  转化速度比较快
     // product  产品上线模式  进行压缩与混淆
     mode:'development'
   }
   ```

4. 在 package.json 配置文件中的 scripts 节点下，新增 dev 脚本如下：

   ```json
   "scripts": {
   "dev": "webpack" // script 节点下的脚本，可以通过 npm run 执行
   }
   ```

5. 终端中运行 `npm run dev` 命令，启动 webpack 进行项目打包。

##### 3. 配置打包的入口和出口

webpack 的 4.x 版本中默认约定：

- 打包的入口文件为 src -> index.js
- 打包的输出文件为 dist -> main.js

如果要修改打包的入口与出口，可以在 webpack.config.js 中新增如下配置信息：

```js
// webpack配置文件

// 导入操作路径的模块
const path  = require("path");

module.exports = {
  // 也叫做编译模式
  // mode  指定构建模式  development 表示通过开发模式来构建
  // 开发模式 我们代码不会进行压缩与混淆  转化速度比较快
  // production  产品上线模式  进行压缩与混淆
  mode:'development',
  // mode:'production',
  // 打包入口文件的路径  绝对路径
  entry:path.join(__dirname,'./src/index.js'),
  // 文件的出口位置
  output:{
    path:path.join(__dirname,'./dist'),// 输出文件的存放路径
    filename:'bundle.js'// 输出文件的名称
  }
}
```

##### 4. 配置 webpack 的自动打包功能

1. 运行 `npm i webpack-dev-server -D`命令，安装支持项目自动打包的工具

2. 修改 package.json  scripts 中的 dev 命令：

   ```json
   "scripts": {
    "dev": "webpack-dev-server" // script 节点下的脚本，可以通过 npm run 执行
   }
   
   ```

3. 将 src -> index.html 中，script 脚本的引用路径，**修改为 "/buldle.js“**

4. 运行 npm run dev 命令，重新进行打包

5. 在浏览器中访问 http://localhost:8080 地址，查看自动打包效果

**注意：**

- webpack-dev-server 会启动一个实时打包的 http 服务器
- webpack-dev-server 打包生成的**输出文件，默认放到了项目根目录中，而且是虚拟的、看不见的。**



##### 5. 配置 html-webpack-plugin 生成预览页面

1. 运行 `npm install html-webpack-plugin –D` 命令，安装生成预览页面的插件

2. 修改 webpack.config.js 文件头部区域，添加如下配置信息：

   ```js
   // 导入生成预览页面的插件，得到一个构造函数
   const HtmlWebpackPlugin = require('html-webpack-plugin')
   const htmlPlugin = new HtmlWebpackPlugin({ // 创建插件的实例对象
    template: './src/index.html', // 指定要用到的模板文件
    filename: 'index.html' // 指定生成的文件的名称，该文件存在于内存中，在目录中不显示
   })
   
   ```

3. 修改 webpack.config.js 文件中向外暴露的配置对象，新增如下配置节点：

   ```js
   module.exports = {
    plugins: [ htmlPlugin ] // plugins 数组是 webpack 打包期间会用到的一些插件列表
   }
   ```

##### 6. 配置自动打包相关的参数

```js
// package.json中的配置
 // --open 打包完成后自动打开浏览器页面
 // --host 配置 IP 地址
 // --port 配置端口
 "scripts": {
 "dev": "webpack-dev-server --open --host 127.0.0.1 --port 8888"
 },
```



#### 2.4 webpack 中的加载器

**package.json 配置文件的版本**

```json
{
  "name": "proj",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack-dev-server --open --host 127.0.0.1 --port 8888",
    "build": "webpack -p"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "jquery": "^3.3.1"
  },
  "devDependencies": {
    "@babel/core": "^7.2.2",
    "@babel/plugin-proposal-class-properties": "^7.3.0",
    "@babel/plugin-transform-runtime": "^7.2.0",
    "@babel/preset-env": "^7.3.1",
    "@babel/runtime": "^7.3.1",
    "autoprefixer": "^9.4.6",
    "babel-loader": "^8.0.5",
    "css-loader": "^2.1.0",
    "file-loader": "^3.0.1",
    "html-webpack-plugin": "^3.2.0",
    "less": "^3.9.0",
    "less-loader": "^4.1.0",
    "node-sass": "^4.11.0",
    "postcss-loader": "^3.0.0",
    "sass-loader": "^7.1.0",
    "style-loader": "^0.23.1",
    "url-loader": "^1.1.2",
    "webpack": "^4.29.0",
    "webpack-cli": "^3.2.1",
    "webpack-dev-server": "^3.1.14"
  }
}

```



##### 1. 通过loader 打包 非 js 模块

**在实际开发过程中，webpack 默认只能打包处理以 .js 后缀名结尾的模块，其他非 .js 后缀名结 尾的模块，webpack 默认处理不了，需要调用 loader 加载器才可以正常打包，否则会报错！**

loader 加载器可以协助 webpack 打包处理特定的文件模块，比如：

- less-loader 可以打包处理 .less 相关的文件

- sass-loader 可以打包处理 .scss 相关的文件

- url-loader 可以打包处理 css 中与 url 路径相关的文件

##### 2. loader 的调用过程

![image-20210422202129813](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210422202129813.png)



#### 2.5 webpack 中加载器的基本使用

![image-20210422202207455](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210422202207455.png)

##### 1. 打包处理 css 文件

1. 运行 `npm i style-loader css-loader -D`   命令，安装处理 css 文件的 loader

2. 在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下：

   ```js
   // 所有第三方文件模块的匹配规则
    module: {
    rules: [
    { test: /\.css$/, use: ['style-loader', 'css-loader'] }
    ]
    }
   
   ```

   其中，test 表示匹配的文件类型， use 表示对应要调用的 loader

**注意：**

- use 数组中指定的 loader 顺序是固定的
- 多个 loader 的调用顺序是：从后往前调用

##### 2. 打包处理 less 文件

1. 运行 `npm i less-loader less -D`   命令

2. 在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下：

   ```js
    // 所有第三方文件模块的匹配规则
    module: {
    rules: [
    { test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] }
    ]
    }
   ```

##### 3. 打包处理 scss 文件

1. 运行 `npm i sass-loader node-sass -D `  命令

2. 在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下：

   ```js
   // 所有第三方文件模块的匹配规则
    module: {
    rules: [
    { test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader'] }
    ]
   }
   ```

##### 4. 配置 postCSS 自动添加 css 的兼容前缀

1. 运行 npm i postcss-loader autoprefixer -D 命令

2. 在项目根目录中创建 postcss 的配置文件 postcss.config.js，并初始化如下配置：

   ```js
   const autoprefixer = require('autoprefixer') // 导入自动添加前缀的插件
    module.exports = {
    plugins: [ autoprefixer ] // 挂载插件
    }
   ```

3. 在 webpack.config.js 的 module -> rules 数组中，修改 css 的 loader 规则如下：

   ```js
   module: {
    rules: [
    { test:/\.css$/, use: ['style-loader', 'css-loader', 'postcss-loader'] }
    ]
    }
   
   ```

   

##### 5. 打包样式表中的图片和字体文件

1. 运行 `npm i url-loader file-loader -D`   命令

2. 在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下：

   ```js
   module: {
    rules: [
    { 
    test: /\.jpg|png|gif|bmp|ttf|eot|svg|woff|woff2$/, 
    use: 'url-loader?limit=16940'
    }
    ]
    }
   
   ```

   **其中 ? 之后的是 loader 的参数项。**

   limit 用来指定图片的大小，单位是字节(byte),只有小于 limit 大小的图片，才会被转为 base64 图片

##### 6. 打包处理 js 文件中的高级语法

1. 安装babel转换器相关的包：`npm i babel-loader @babel/core @babel/runtime -D`

2. 安装babel语法插件相关的包：`npm i @babel/preset-env @babel/plugin-transform-runtime @babel/plugin-proposal-class-properties –D`

3. 在项目根目录中，创建 babel 配置文件 babel.config.js 并初始化基本配置如下：

   ```js
   module.exports = {
    presets: [ '@babel/preset-env' ],
    plugins: [ '@babel/plugin-transform-runtime', '@babel/plugin-proposal-class-properties’ ]
    }
   
   ```

4. 在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下：

   ```js
   // exclude 为排除项，表示 babel-loader 不需要处理 node_modules 中的 js 文件
    { test: /\.js$/, use: 'babel-loader', exclude: /node_modules/ }
   ```

   



### 3. Vue单文件组件

#### 3.1 传统组件的问题和解决方案

##### 1. 问题

1. 全局定义的组件必须保证组件的名称不重复
2. 字符串模板缺乏语法高亮，在 HTML 有多行的时候，需要用到丑陋的 \
3. 不支持 CSS 意味着当 HTML 和 JavaScript 组件化时，CSS 明显被遗漏
4. 没有构建步骤限制，只能使用 HTML 和 ES5 JavaScript, 而不能使用预处理器（如：Babel）

##### 2. 解决方案

针对传统组件的问题，Vue 提供了一个解决方案 —— 使用 Vue 单文件组件。

#### 3.2 Vue单文件组件的基本用法

**单文件组件的组成结构**

- template 组件的模板区域
- script 业务逻辑区域
- style 样式区域

```vue
<template>
 <!-- 这里用于定义Vue组件的模板内容 -->
 </template>
 <script>
 // 这里用于定义Vue组件的业务逻辑
 export default {
 data: () { return {} }, // 私有数据
 methods: {} // 处理函数
 // ... 其它业务逻辑
 }
 </script>
 <style scoped>
 /* 这里用于定义组件的样式 */
 </style>
```

#### 3.3 webpack中配置vue组件的加载器

1. 运行 npm i vue-loader vue-template-compiler -D 命令

2. 在 webpack.config.js 配置文件中，添加 vue-loader 的配置项如下：

   ```js
   const VueLoaderPlugin = require('vue-loader/lib/plugin')
   module.exports = {
    module: {
    rules: [
    // ... 其它规则
    { test: /\.vue$/, loader: 'vue-loader' }
    ]
    },
    plugins: [
    // ... 其它插件
    new VueLoaderPlugin() // 请确保引入这个插件！
    ]
   }
   ```

#### 3.4 在 webpack 项目中使用vue

1. 运行 npm i vue –S 安装 vue

2. 在 src -> index.js 入口文件中，通过 import Vue from 'vue' 来导入 vue 构造函数

3. 创建 vue 的实例对象，并指定要控制的 el 区域

4. 通过 render 函数渲染 App 根组件

   ```js
   // 1. 导入 Vue 构造函数
   import Vue from 'vue'
   // 2. 导入 App 根组件
   import App from './components/App.vue'
   const vm = new Vue({
    // 3. 指定 vm 实例要控制的页面区域
    el: '#app',
    // 4. 通过 render 函数，把指定的组件渲染到 el 区域中
    render: h => h(App)
   })
   ```

   

#### 3.5 webpack打包发布

上线之前需要通过webpack将应用进行整体打包，可以通过 package.json 文件配置打包命令：

```js
// 在package.json文件中配置 webpack 打包命令
 // 该命令默认加载项目根目录中的 webpack.config.js 配置文件
 "scripts": {
 // 用于打包的命令
 "build": "webpack -p",
 // 用于开发调试的命令
 "dev": "webpack-dev-server --open --host 127.0.0.1 --port 3000",
 },
```







### 4. Vue脚手架

#### 4.1 Vue脚手架的基本使用

Vue 脚手架用于快速生成 Vue 项目基础架构，其官网地址为：https://cli.vuejs.org/zh/

**使用步骤**

1. 安装3.x版本的vue脚手架

   ```shell
   npm i -g @vue/cli  （我们安装的3.3.0版本）
   ```

**基于3.x版本的脚手架创建vue项目**

```js
 // 1. 基于 交互式命令行 的方式，创建 新版 vue 项目
 vue create my-project
 // 2. 基于 图形化界面 的方式，创建 新版 vue 项目
 vue ui
 // 3. 基于 2.x 的旧模板，创建 旧版 vue 项目
 npm install -g @vue/cli-init
 vue init webpack my-project
```

**方式一**：基于 交互式命令行 的方式，创建 新版 vue 项目

通过上下箭头来选择，我们这里选择手动方式（第二个），回车

![image-20210424142645484](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424142645484.png)

通过上下箭头进行选择，点击空格表示选中这个行，我们这里选 babel，router，Linter/Formatter, 回车

![image-20210424143047674](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424143047674.png)

是否安装历史模式的路由，我们这里选择 n，安装hash模式的路由

![image-20210424143303381](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424143303381.png)

选中第三个 标准模式

![image-20210424143418393](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424143418393.png)

选中默认的就可以了，Lint on save，只要代码保存，我们就对代码的格式进行校验

![image-20210424143502818](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424143502818.png)

工具的配置文件是 1. 为每个工具单独创建  2. 都放在一个 package.json配置文件中。这里我们选择第一个

![image-20210424143620769](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424143620769.png)

是否将前面的选择作为模板，（下次就不需要一直勾选了）

![image-20210424143740062](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424143740062.png)

然后cd进入该项目文件根目录下

![image-20210424145230755](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424145230755.png)

然后 npm run serve

![image-20210424145259771](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424145259771.png)

在浏览器地址栏 输入 http://localhost:8080/ 项目创建成功！



**第二种方式创建：**

输入命令： vue ui  然后会自动打开浏览器网页

![image-20210424145402631](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424145402631.png)

这里我们选择 创建

![image-20210424145440760](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424145440760.png)



![image-20210424145508923](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424145508923.png)





输入新项目名称：其他的东西可以都选默认，然后下面的git我们可以填写一些信息

![image-20210424145537664](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424145537664.png)



![image-20210424145651823](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424145651823.png)



然后我们选中手动进行配置：

![image-20210424145755875](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424145755875.png)



这里我们选中五个，babel不要取消掉了

![image-20210424145910810](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424145910810.png)

最下面的使用配置文件 也不要忘记勾选，这样每个工具都会生成自己单独的配置文件，而不会都放到同一个配置文件中。

![image-20210424150018649](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424150018649.png)

选中版本为2.x的vue.js，然后关闭历史路由（默认是关闭的），然后linter一定要选中标准的

![image-20210424150328957](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424150328957.png)

然后点击创建项目就可以了



最后弹出的预设，我们可以保存一下当前的设置

![image-20210424150526398](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424150526398.png)



这个预设 一般保存在用户根目录下的 .vuerc 文件中

![image-20210424150759104](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424150759104.png)



**第三种方式（基于cli2.x版本创建的项目）**

先安装 @vue/cli-init模块 ，然后才能创建2.x的项目

![image-20210424151334694](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424151334694.png)

执行命令 `vue init webpack my-project3` 来创建项目

输入项目名称，我们这里使用默认的 直接回车就可以，或者自己输入想要的名称，然后回车

![image-20210424151555233](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424151555233.png)

项目的描述，我们也可以直接回车

![image-20210424151705468](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424151705468.png)

作者：   输入自己想要的名称就可以了

![image-20210424151743206](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424151743206.png)

使用上下箭头来选择vue的版本，我们这里都使用第二个，也就是阉割版的vue，里面的东西不全

![image-20210424151854215](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424151854215.png)

输入 y  安装路由

![image-20210424151919142](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424151919142.png)

安装 ESlint  用来对代码进行规范性校验

![image-20210424151956161](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424151956161.png)

选择标准版就可以；

![image-20210424152030567](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424152030567.png)

单元测试 test 我们不安装

![image-20210424152103219](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424152103219.png)

也不安装下面这个

![image-20210424152123256](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424152123256.png)

使用那种方式来安装包，第一种是npm，第二个是yarn安装，第三种是暂时不安装，等后面手动安装。我们选择第一个就可以了

![image-20210424152158898](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424152158898.png)

安装完成

![image-20210424152345599](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424152345599.png)

进入该目录，然后 npm run dev 进行项目的编译和运行

在浏览器上输入地址进行访问

![image-20210424152454572](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424152454572.png)

![image-20210424152518620](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424152518620.png)





#### 4.2 Vue脚手架生成的项目结构分析

![image-20210423164940218](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210423164940218.png)

![image-20210424152701737](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424152701737.png)

#### 4.3 Vue脚手架的自定义配置

##### 1. 通过package.json配置项目

```js
// 必须是符合规范的json语法
"vue":{
    "devServer": {
 "port": "8888",
 "open" : true
 }
}
```

**注意**：

不推荐使用这种配置方式。因为 package.json 主要用来管理包的配置信息；为了方便维护，推荐将 vue 脚 手架相关的配置，单独定义到 vue.config.js 配置文件中

##### 2. 通过单独的配置文件配置项目

1. 在项目中根目录创建文件 vue.config.js

2. 在该文件中进行相关配置，从而覆盖默认配置

   ```js
   // vue.config.js
   module.exports = {
     devServer:{
       // 自动打开浏览器
       open:true,
       // 端口号
       port:9999
  }
   }
   ```
   
   





### 5. Element-UI的基本使用

Element-UI：一套为开发者、设计师和产品经理准备的基于 Vue 2.0 的桌面端组件库。 官网地址为： http://element-cn.eleme.io/#/zh-CN

#### 1. 基于命令行方式手动安装 

1. 安装依赖包  `npm i element-ui -S`
2. 导入 Element-UI 相关资源

```js
 // 导入组件库
 import ElementUI from 'element-ui';
 // 导入组件相关样式
 import 'element-ui/lib/theme-chalk/index.css';
 // 配置 Vue 插件
 Vue.use(ElementUI);
```

**下面说下“`2 errors and 0 warnings potentially fixable with the --fix option`”报错
这种只要隐藏.eslintrc.js中的’@vue/standard’就行了，如下图：**

![image-20210424155756343](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424155756343.png)



第二种“2 errors and 0 warnings potentially fixable with the --fix option”报错解决方式，
functineName() 左括号前没有空格报错，在.eslintrc.js的rules中输入’space-before-function-paren’: 0

![image-20210424160103396](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424160103396.png)



#### 2. 基于图形化界面自动安装

1. 运行 vue ui 命令，打开图形化界面
2. 通过 Vue 项目管理器，进入具体的项目配置面板
3. 点击 插件 -> 添加插件，进入插件查询面板
4. 搜索 vue-cli-plugin-element 并安装
5. 配置插件，实现按需导入，从而减少打包后项目的体积

![image-20210424160630816](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424160630816.png)

![image-20210424160832185](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cvue%5Cvue.assets%5Cimage-20210424160832185.png)