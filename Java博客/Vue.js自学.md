# Vue.js自学...

## 一. 什么是Vue.js

Vue (读音 /vjuː/，类似于 **view**) 是一套用于构建用户界面的**渐进式框架**。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与[现代化的工具链](https://cn.vuejs.org/v2/guide/single-file-components.html)以及各种[支持类库](https://github.com/vuejs/awesome-vue#libraries--plugins)结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

## 二. Vue.js初识

### 2.1 如何安装Vue.js

#### 2.1.1 直接 `<script>` 引入

直接下载并用 `<script>` 标签引入，`Vue` 会被注册为一个全局变量。

```javascript
<script src="../vue/vue.js"></script>
```

#### 2.1.2 CDN

对于制作原型或学习，你可以这样使用最新版本：

<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

### 2.2 Vue.js案例

#### 2.2.1 使用Vue进行列表展示

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>列表展示</title>
    <script src="../vue/vue.js"></script>
</head>
<body>
<div  id="app">
    {{message}},
    {{movies}},
    <ul>
        <li v-for="item in movies">{{item}}</li>
    </ul>
</div>
<script>
    const app = new Vue({
        //el :挂载元素
        el:"#app",
        data:{
            message:"你好？？",
            movies:['星际穿越','毛毛最帅','大话西游']
        }
    })
</script>
</body>
</html>
```

#### 2.2.2 计数器

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="../vue/vue.js"></script>
</head>

<body>
<div id="app">
    当前计数：{{counter}}
    <!--  <button v-on:click="counter++">+</button>
     <button v-on:click="counter--">-</button> -->
    <button v-on:click="add">+</button>
    <button v-on:click="sub">-</button>
</div>

<script>
    /**
     * el： 类型：string | HTMLElement
     *      作用：决定之后vue实例会管理哪一个Dom
     * data: 类型：Object | Function（组件当中data必须是一个函数）
     *      作用：Vue实例对应的数据对象
     * methods：类型：{[key:string]:Function}
     *          作用：定义属于Vue的一些方法，可以在其他地方调用，也可以在指令中调用
     */
    //语法糖 ： 简写
    const app = new Vue({
        // el: "#app",
        el: document.querySelector("#app"),
        data: {
            counter: 0
        },
        //methods 定义方法
        methods: {
            add: function () {
                this.counter++;
            },
            sub: function () {
                this.counter--;
            }
        },
    })
</script>
</body>

</html>
```

### 2.3 创建Vue实例传入的options（当前常用）

- el ：类型：string | HTMLElement

  ​		作用：决定之后vue实例会管理哪一个Dom

- data： 类型：Object | Function（组件当中data必须是一个函数）

  ​			 作用：Vue实例对应的数据对象

- methods：类型：{[key:string]:Function}

  ​					作用：定义属于Vue的一些方法，可以在其他地方调用，也可以在指令中调用

  

  