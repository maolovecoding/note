# Vue-router

## 什么是路由

### 认识前端路由

**路由其实是网络工程中的一个术语：**

- 在架构一个网络时，非常重要的两个设备就是路由器和交换机。 
- 当然，目前在我们生活中路由器也是越来越被大家所熟知，因为我们生活中都会用到路由器： 
- 事实上，路由器主要维护的是一个映射表； 
- 映射表会决定数据的流向；

路由的概念在软件工程中出现，最早是在后端路由中实现的，原因是web的发展主要经历了这样一些阶段：

- 后端路由阶段； 
- 前后端分离阶段； 
- 单页面富应用（SPA）；

### 后端路由阶段

早期的网站开发整个HTML页面是由服务器来渲染的.。服务器直接生产渲染好对应的HTML页面, 返回给客户端进行展示.

**但是, 一个网站, 这么多页面服务器如何处理呢?**

- 一个页面有自己对应的网址, 也就是URL； 
- URL会发送到服务器, 服务器会通过正则对该URL进行匹配, 并且最后交给一个Controller进行处理； 
- Controller进行各种处理, 最终生成HTML或者数据, 返回给前端.

上面的这种操作, 就是后端路由：

- 当我们页面中需要请求不同的路径内容时, 交给服务器来进行处理, 服务器渲染好整个页面, 并且将页面返回给客户端. 
- 这种情况下渲染好的页面, 不需要单独加载任何的js和css, 可以直接交给浏览器展示, 这样也有利于SEO的优化.

**后端路由的缺点:**

- 一种情况是整个页面的模块由后端人员来编写和维护的； 
- 另一种情况是前端开发人员如果要开发页面, 需要通过PHP和Java等语言来编写页面代码； 
- 而且通常情况下HTML代码和数据以及对应的逻辑会混在一起, 编写和维护都是非常糟糕的事情；

### 前后端分离阶段

**前端渲染的理解：**

- 每次请求涉及到的静态资源都会从静态资源服务器获取，这些资源包括HTML+CSS+JS，然后在前端对这些请 求回来的资源进行渲染； 
- 需要注意的是，客户端的每一次请求，都会从静态资源服务器请求文件； 
- 同时可以看到，和之前的后端路由不同，这时后端只是负责提供API了；

**前后端分离阶段：**

- 随着Ajax的出现, 有了前后端分离的开发模式； 
- 后端只提供API来返回数据，前端通过Ajax获取数据，并且可以通过JavaScript将数据渲染到页面中； 
- 这样做最大的优点就是前后端责任的清晰，后端专注于数据上，前端专注于交互和可视化上； 
- 并且当移动端(iOS/Android)出现后，后端不需要进行任何处理，依然使用之前的一套API即可； 
- 目前比较少的网站采用这种模式开发（jQuery开发模式）；

### URL的hash

前端路由是如何做到URL和内容进行映射呢？监听URL的改变。

**URL的hash**

- URL的hash也就是锚点(#), 本质上是改变window.location的href属性； 
- 我们可以通过直接赋值location.hash来改变href, 但是页面不发生刷新；

hash的优势就是兼容性更好，在老版IE中都可以运行，但是缺陷是有一个#，显得不像一个真实的路径。

```html
<div id="app">
    <a href="#/home">home</a>
    <a href="#/about">about</a>
    <div id="router-view"></div>
  </div>
  <script>
    const routerView = document.querySelector('#router-view');
    // url的hash
    // URL的hash也就是锚点，本质上就是改变window.location 的 href 属性
    // 我们可以通过直接赋值location.hash 来改变 href 但是页面不会刷新
    window.addEventListener('hashchange',()=>{
      console.log(location.hash);
      if(location.hash==="#/home"){
        routerView.innerHTML = "home";
      }else if(location.hash==="#/about"){
        routerView.innerHTML = "about";
      }
    })
  </script>
```

![image-20210829195623661](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829195623661.png)

### HTML5的History

history接口是HTML5新增的, 它有l六种模式改变URL而不刷新页面：

- replaceState：替换原来的路径； 
- pushState：使用新的路径； 
- popState：路径的回退； 
- go：向前或向后改变路径； 
- forward：向前改变路径； 
- back：向后改变路径；

#### HTML5的History测试

#### pushState

`pushState()` 需要三个参数: 一个状态对象, 一个标题 (目前被忽略), 和 (可选的) 一个URL. 让我们来解释下这三个参数详细内容：

- **状态对象** — 状态对象state是一个JavaScript对象，通过pushState () 创建新的历史记录条目。无论什么时候用户导航到新的状态，popstate事件就会被触发，且该事件的state属性包含该历史记录条目状态对象的副本。

  ​    状态对象可以是能被序列化的任何东西。原因在于Firefox将状态对象保存在用户的磁盘上，以便在用户重启浏览器时使用，我们规定了状态对象在序列化表示后有640k的大小限制。如果你给 `pushState()` 方法传了一个序列化后大于640k的状态对象，该方法会抛出异常。如果你需要更大的空间，建议使用 `sessionStorage` 以及 `localStorage`.

- **标题** — Firefox 目前忽略这个参数，但未来可能会用到。在此处传一个空字符串应该可以安全的防范未来这个方法的更改。或者，你可以为跳转的state传递一个短标题。

- **URL** — 该参数定义了新的历史URL记录。注意，调用 `pushState()` 后浏览器并不会立即加载这个URL，但可能会在稍后某些情况下加载这个URL，比如在用户重新打开浏览器时。新URL不必须为绝对路径。如果新URL是相对路径，那么它将被作为相对于当前URL处理。新URL必须与当前URL同源，否则 `pushState()` 会抛出一个异常。该参数是可选的，缺省为当前URL。

```html
<div id="app">
    <a href="home">home</a>
    <a href="about">about</a>
    <div id="router-view"></div>
  </div>
  <script>
    const routerView = document.querySelector('#router-view');
    const as = document.querySelectorAll('a');
    const arr = Array.from(as);
    arr.forEach(item => {
      item.addEventListener('click', event => {
        event.preventDefault();
        // pushState() 需要三个参数: 一个状态对象,
        //  一个标题 (目前被忽略), 和 (可选的) 一个URL. 
        //history.pushState({}, "", item.getAttribute("href"));
                  history.replaceState({}, "", item.getAttribute("href"));

        historyChange()
      })
    })
    function historyChange() {
      // location.pathname 项目根路径开始的虚拟地址
      console.log(location.pathname.slice(location.pathname.lastIndexOf("/")));
      switch (location.pathname.slice(location.pathname.lastIndexOf("/"))) {
        case "/home":
          routerView.innerHTML = "home";
          break;
        case "/about":
          routerView.innerHTML = "about";
          break;
        default:
          routerView.innerHTML = "default";
          break;
      }
    }
    window.addEventListener('popstate',historyChange);
    window.addEventListener('go',historyChange);
</script>
```

## vue-router

### 认识vue-router

**目前前端流行的三大框架, 都有自己的路由实现:**

- Angular的ngRouter 
- React的ReactRouter 
- Vue的vue-router

Vue Router 是 Vue.js 的官方路由。它与 Vue.js 核心深度集成，让用 Vue.js 构建单页应用变得非常容易。

目前Vue路由最新的版本是4.x版本.

**vue-router是基于路由和组件的**

- 路由用于设定访问路径, 将路径和组件映射起来. 

- 在vue-router的单页面应用中, 页面的路径的改变就是组件的切换

**安装vue-router**

```shell
# 安装 4.x
npm install vue-router@4
```

### 路由的使用步骤

使用vue-router的步骤:

- 第一步：创建路由组件的组件； 
- 第二步：配置路由映射: 组件和路径映射关系的routes数组； 
- 第三步：通过createRouter创建路由对象，并且传入routes和history模式； 
- 第四步：使用路由通过`<router-link>`和`<router-view>`；

### 路由的基本使用流程

```js
// 路由的配置规则
import About from "../pages/About.vue"
import Home from "../pages/Home.vue"
// 导入vue-router
// 导入创建路由对象的函数，以及路由方式的函数
import { createRouter, createWebHistory, createWebHashHistory } from 'vue-router';

// 路由规则,映射关系
const routes = [
  { path: "/home", component: Home },
  { path: "/about", component: About }
]
// 路由对象，管理路由的映射
const router = createRouter({
  routes,
  // 指定路由使用的模式 这里指定使用history路由 不使用hash模式了
  //history: createWebHashHistory(),
  history: createWebHistory(),
});

// 导出路由
export default router;
```

![image-20210829213342670](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829213342670.png)

在浏览器输入地址/home

![image-20210829213424691](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829213424691.png)

当然这样在地址栏输入地址是比较麻烦的。

所以我们可以使用`<router-link>`组件来进行路由的切换。

![image-20210829213928215](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829213928215.png)

### 路由的默认路径

我们这里还有一个不太好的实现:

- 默认情况下, 进入网站的首页, 我们希望渲染首页的内容； 

- 但是我们的实现中, 默认没有显示首页组件, 必须让用户点击才可以；

如何可以让路径默认跳到到首页, 并且<router-view>渲染首页组件呢?

**我们在routes中又配置了一个映射：**

- path配置的是根路径: / 
- redirect是重定向, 也就是我们将根路径重定向到/home的路径下, 这样就可以得到我们想要的结果了

**默认情况下，根路径没有匹配到组件。所以不会显示其他的组件。**

![image-20210829214824036](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829214824036.png)

**这时，我们可以在路由规则中，配置一下根路径的重定向，让其重定向到/home路径下。**

![image-20210829214930953](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829214930953.png)

这样即使访问的是根路径，也会进行重定向到/home这个路由。

![image-20210829215011288](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829215011288.png)

### history模式

上面的代码的测试，路径URL都是采用的hash模式。接下来我们将使用history模式。

![image-20210829215231161](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829215231161.png)

![image-20210829215301413](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829215301413.png)

这种地址看起来更加真实。

### router-link

**router-link事实上有很多属性可以配置：**

- to属性： 
  - 是一个字符串，或者是一个对象

- replace属性： 
  - 设置 replace 属性的话，当点击时，会调用 router.replace()，而不是 router.push()；

- active-class属性： 
  - 设置激活a元素后应用的class，默认是router-link-active

- exact-active-class属性： 
  - 链接精准激活时，应用于渲染的 a标签 的 class，默认是router-link-exact-active；(**路由嵌套的时候我们再说这个知识**)

#### replace

```html
<div>
    <!-- 路由的跳转 to属性的值就是我们要前往的地址 -->
    <!-- to属性 可以是字符串，直接表示路径地址，也可以是对象（暂时不管） -->
    <!-- replace 属性。
    不设置该属性时：
    默认情况下路由之间的切换，地址的变化是采用push的方式 history模式里面url的变化可以理解为pushState()
    如果设置该属性时：
    路由之间的切换以后，是不可以返回上一个路由的。
    -->
    <router-link to="/home" :replace="true">首页home</router-link>
    <router-link to="/about" :replace="true">关于about</router-link>
    <!-- 路由的占位符 -->
    <router-view></router-view>
  </div>
```

![image-20210829220319252](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829220319252.png)

一旦设置该属性，就不能进行路由的返回了。



#### active-class、exact-active-class

当我们点击某个<router-link>组件的时候，vue会默认在上面添加两个class样式。类名默认是router-link-active 和 router-link-exact-active

![image-20210829220754248](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829220754248.png)

**激活的router-link具有某种样式，有助于我们进行设置。**比如我们可以把激活的那个<router-link>组件的样式进行更改。

![image-20210829221150533](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829221150533.png)

当然active-class属性可以修改激活路由具有的类名。

```html
<!-- 使用 active-class属性 可以设置当前路由被点击（选中）后的样式 -->
    <router-link to="/home" active-class="mao-active">首页home</router-link>
    <router-link to="/about" active-class="mao-active">关于about</router-link>
    <!-- 路由的占位符 -->
    <router-view></router-view>
```

![image-20210829222349258](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829222349258.png)

![image-20210829222411688](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829222411688.png)





### 路由懒加载

当打包构建应用时，JavaScript 包会变得非常大，影响页面加载：

- 如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就会 更加高效； 
- 也可以提高首屏的渲染效率；

其实这里还是我们前面讲到过的webpack的分包知识，而Vue Router默认就支持动态来导入组件：

- 这是因为component可以传入一个组件，也可以接收一个函数，该函数 需要放回一个Promise； 
- 而import函数就是返回一个Promise；

```js
// 路由规则,映射关系
const routes = [
  // { path: "/", component: Home },
  // 访问根路径的时候，进行路由的重定向 redirect
  { path: "/", redirect: "/home" },
  // 路由的懒加载 实现懒加载 我们只需要让component属性的值设置为一个函数
  // 这个函数的返回值是一个promise对象，且也有这个路由匹配的组件
  {
    path: "/home", component: () => {
      // 使用import函数加载文件 
      // import函数的返回值本身就是一个promise对象
      // 当我们这样配置以后，在使用webpack打包的时候，会自动帮我们进行分包的操作
      // 只有用到这些组件的时候，或者说这些路由激活以后，
      // 才会发起请求获取对应的组件数据
      return import("../pages/Home.vue");
    }
  },
  {
    path: "/about",

    component: () => import("../pages/About.vue")
  }
]
```

#### 打包效果分析

我们看一下打包后的效果：

我们会发现分包是没有一个很明确的名称的，其实webpack从3.x开始支持对分包进行命名（chunk name）：

```js
const routes = [
  // { path: "/", component: Home },
  // 访问根路径的时候，进行路由的重定向 redirect
  { path: "/", redirect: "/home" },
  // 路由的懒加载 实现懒加载 我们只需要让component属性的值设置为一个函数
  // 这个函数的返回值是一个promise对象，且也有这个路由匹配的组件
  {
    path: "/home", component: () => {
      // 使用import函数加载文件 
      // import函数的返回值本身就是一个promise对象
      // 当我们这样配置以后，在使用webpack打包的时候，会自动帮我们进行分包的操作
      // 只有用到这些组件的时候，或者说这些路由激活以后，
      // 才会发起请求获取对应的组件数据
      return import(/* webpackChunkName:"home-chunk" */"../pages/Home.vue");
    }
  },
  {
    path: "/about",
    // TODO   webpack特性
    // 如果我们想在webpack打包的时候，
    // 给我们用到的分包管理的路由组件文件 起名字
    // 那么就用到了 魔法注释
    // 格式  /**/
    // 我们在import函数的括号前使用这个魔法注释
    // 这个注释会被webpack解析的，不能乱写
    component: () => import(/* webpackChunkName: "about-chunk" */"../pages/About.vue")
  }
]
```

**没有进行懒加载时的打包效果：**

![image-20210829231647731](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829231647731.png)

**组件懒加载后的分包效果：（这里的分包我指定了名称，不使用魔法注释的话其实我们是无法分辨分出来的包对应哪一个文件）**

![image-20210829231727222](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210829231727222.png)





### 路由的其他属性

- name属性：路由记录独一无二的名称；

- meta属性：自定义的数据

```js
{
    path: "/home",
    // name 属性 给这个路由起名字 我们进行路由的跳转的时候
    // 也可以通过name属性的值 进行页面的跳转
    name: "home",
    // 还可以使用 meta属性 配置一些元数据
    meta: {
      name: "毛毛",
      age: 21,
      gender: "男"
    },
    component: () => {
      return import(/* webpackChunkName:"home-chunk" */"../pages/Home.vue");
    }
  }
```

### 动态路由

#### 动态路由基本匹配

很多时候我们需要将给定匹配模式的路由映射到同一个组件：

- 例如，我们可能有一个 User 组件，它应该对所有用户进行渲染，但是用户的ID是不同的；

- 在Vue Router中，我们可以在路径中使用一个动态字段来实现，我们称之为 路径参数；

```js
// 动态路由，有时候我们希望这个路由的路径不是写死的
  // 比如那个用户登录 就在后面显示那个用户 /user/mao
  {
    // 动态路由需要在地址上动态传递参数
    path: "/user/:username",
    component: User
  }
```

- 在router-link中进行如下跳转：

```html
<!-- 动态路由的使用，后面动态地址那部分不是不变的 -->
    <!-- /user/mao      /user/fan -->
    <router-link to="/user/mao">用户user</router-link>

```

#### 获取动态路由的值

- 那么在User中如何获取到对应的值呢？
  - 在template中，直接通过 $route.params获取值；
  - 在created中，通过 this.$route.params获取值；
  - 在setup中，我们要使用 vue-router库给我们提供的一个hook useRoute；
  - 该Hook会返回一个Route对象，对象中保存着当前路由相关的值；

##### template

![image-20210830094347776](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830094347776.png)



##### created

```js
// 在组件创建完毕后 就可以拿到动态路由的参数
  created() {
    // 使用 this.$route 就可以拿到当前匹配的路由 获取地址等
    // 甚至这个参数里面还可以获取到查询字符串
    // 也可以获取到我们在前面路由中定义的 name，meta等属性的值
    console.log(this.$route);
    // params 就是我们的动态路由的参数
    console.log(this.$route.params);
  }
```

![image-20210830094443576](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830094443576.png)



##### setup



```js
// 通过获取该函数的返回值，可以拿到当前要显示的route路由规则对象
import { useRoute } from "vue-router"
setup() {
    const route = useRoute()
    console.log(route);
    console.log(route.params.username);// mao
  }
```

![image-20210830095824134](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830095824134.png)





#### 匹配多个参数

```js
{
    // 动态路由需要在地址上动态传递参数
    path: "/user/:username/:id",
    component: () => import("../pages/User.vue")
  }
```

![image-20210830100102910](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830100102910.png)



#### NotFound

对于哪些没有匹配到的路由，我们通常会匹配到固定的某个页面

比如NotFound的错误页面中，这个时候我们可编写一个动态路由用于匹配所有的页面；

```js
{
    // TODO  这个路由的匹配规则优先级最低 只有其他路由匹配不到才会匹配这个
    // 当匹配不到其他所有的路由的时候，就会匹配这个路由
    // 这个写法可以匹配所有的路由路径
    // patchMatch() 路径匹配
    // ()里面的是正则表达式
    path: "/:patchMatch(.*)",
    component: NotFound
  }
```

![image-20210830112535991](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830112535991.png)



#### 匹配规则加*

这里还有另外一种写法：

**注意：我在/:pathMatch(.*)后面又加了一个 *；**

```js
{    
    // 如果在patchMatch()函数后面再加上一个 *
    // 会把我们当前匹配到的路径按照 / 进行分割，放到数组里面
    // 也就是通过 $route.params.patchMatch 获取到的地址变成了数组
    // /a/b/c  --->  ['a','b','c']
    path: "/:patchMatch(.*)*",
    component: () => import("../pages/NotFound.vue")
  }
```

 它们的区别在于解析的时候，是否解析 /：

![image-20210830130023144](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830130023144.png)

### 路由的嵌套

**什么是路由的嵌套呢？**

- 目前我们匹配的Home、About、User等都属于底层路由(**也可以叫做一级路由**)，我们在它们之间可以来回进行切换；

- 但是呢，我们Home页面本身，也可能会在多个组件之间来回切换：
  - 比如Home中包括Product、Message，它们可以在Home内部来回切换；

- 这个时候我们就需要使用嵌套路由，在Home中也使用 router-view 来占位之后需要渲染的组件；

```js
{
    path: "/home",
    component: () => {
      return import("../pages/Home.vue");
    },
    // 嵌套路由 children 是一个数组 可以有多个子路由
    children: [
      // 子路由默认是有父级的地址了 /home/ 
      // 所以这里只需要写自己独有的地址就可以，且不需要 /
      { "path": "message", component: () => import("../pages/HomeMessage.vue") },
      { "path": "shops", component: () => import("../pages/HomeShops.vue") },
      // 这里在使用一波重定向，默认访问的是 /home/message
      // 注意，这里不是 / 这里其实是 "" 空字符
      { path: "", redirect: "/home/message" }
    ]
  }
```

![image-20210830140019909](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830140019909.png)

#### active-class、exact-active-class

active-class，只要当前的路由匹配实际地址栏的地址的一部分，也就是路由嵌套的时候，访问的是子路由地址，实际上父路由也算是被访问了。这时候active-class就会加在父路由的那个`<router-link>`上，也会加在子路由的那个标签上。

而exact-active-class只有精准匹配的时候，才会加在对应路由的那个标签上。也就是只会加在发生路由嵌套的的子路由上。

![image-20210830141039073](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830141039073.png)





### 编程式导航



#### 代码的页面跳转

有时候我们希望通过代码来完成页面的跳转，比如点击的是一个按钮：

![image-20210830142740084](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830142740084.png)

![image-20210830142801543](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830142801543.png)

**当然，我们也可以传入一个对象：**

![image-20210830142936031](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830142936031.png)



**如果是在setup中编写的代码，那么我们可以通过 useRouter 来获取：**

![image-20210830143136615](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830143136615.png)





#### query方式的参数

我们也可以通过query的方式来传递参数：

![image-20210830143700481](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830143700481.png)

在界面中通过 **$route.query** 来获取参数：

![image-20210830143858156](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830143858156.png)



#### 替换当前的位置

使用push的特点是压入一个新的页面，那么在用户点击返回时，上一个页面还可以回退，但是如果我们希望当前 页面是一个替换操作，那么可以使用replace：

![image-20210830144058660](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830144058660.png)

**简单来说就是没办法回到上个页面**

| 声明式                           | 编程式               |
| -------------------------------- | -------------------- |
| <router-link :to="地址" replace> | router.replace(地址) |



#### 页面的前进后退

当然，想要完成这个效果，首页页面的地址栏是有历史记录的地址的。只有可以前进和后退，才能完成我们需要的效果。

![image-20210830145246632](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210830145246632.png)

- router的go方法：可以完成back和forward两个方法的效果。

- router也有back：通过调用 history.back() 回溯历史。相当于 router.go(-1)；

- router也有forward：通过调用 history.forward() 在历史中前进。相当于 router.go(1)；

```js
// 前进
    const goToOneStep = () => {
      // 前进几条地址记录 二者等价 也可以是 -1 表示后退几条历史记录
      // router.go(1) 
      router.forward(1)
    }
    // 后退
    const backOneStep = () => {
      // 后退几条历史记录
      // router.back(1)
      // 使用 go(-n) 也可以达到回退历史记录的效果
      router.go(-1)
    }
```

**即使给back方法传递负数，也不会达到前进的结果。还是一样是后退。应该是内部做了转换。**



### router-link的v-slot

在vue-router3.x的时候，router-link有一个tag属性，可以决定router-link到底渲染成什么元素：

- 但是在vue-router4.x开始，该属性被移除了； 
- 而给我们提供了更加具有灵活性的v-slot的方式来定制渲染的内容；

**v-slot如何使用呢？**

- 首先，我们需要使用custom表示我们整个元素要自定义：
  - 如果不写，那么自定义的内容会被包裹在一个 a 元素中；

- 其次，我们使用v-slot来作用域插槽来获取内部传给我们的值：
  - href：解析后的 URL； 
  - route：解析后的规范化的route对象； 
  - navigate：触发导航的函数； 
  - isActive：是否匹配的状态； 
  - isExactActive：是否是精准匹配的状态；

```html
<!-- router-link组件默认渲染出来的是a元素 -->
    <!-- 当我们使用了 custom属性以后，a元素就不会在存在了 -->
    <!-- v-slot="props" -->
    <router-link to="/home" v-slot="props" custom>
      <!-- 使用默认插槽，渲染为按钮 -->
      <!-- props.navigate 导航函数 点击按钮后可以进行路由的跳转了 -->
      <button @click="props.navigate">首页home</button>
      <button>哈哈哈 我不会跳转的</button>
      <div>
        <!-- 跳转的链接 -->
        <span>{{ props.href }}</span>
        <br />
        <!-- 还可以获取当前的路由规则对象 -->
        <!-- <span>{{ props.route }}</span> -->
        <!-- navigate 获取导航函数 -->
        <!-- <span>{{props.navigate}}</span> -->
        <!-- isActive 是否处于路由激活的状态 -->
        <span>{{props.isActive}}</span><br>
        <!-- isExactActive 路由是否处于精确的激活状态，完全匹配 -->
        <span>{{props.isExactActive}}</span>
      </div>
    </router-link>
    <router-link to="/about">
      <!-- 给默认插槽传递自定义组件 -->
      <nav-bar title="关于about"></nav-bar>
    </router-link>
    <!-- 动态路由的使用，后面动态地址那部分不是不变的 -->
    <!-- /user/mao      /user/fan -->
    <router-link to="/user/mao/1">用户user</router-link>
    <!-- 路由的占位符 -->
    <router-view></router-view>
```

![image-20210831112350080](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210831112350080.png)

![image-20210831112630682](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210831112630682.png)



### router-view的v-slot

router-view也提供给我们一个插槽，可以用于 **<transition>**  和  **<keep-alive>组件来包裹你的路由组件**：

- Component：要渲染的组件； 
- route：解析出的标准化路由对象；

![image-20210831130128339](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210831130128339.png)



### 动态添加路由

某些情况下我们可能需要动态的来添加路由：

- 比如根据用户不同的权限，注册不同的路由； 
- 这个时候我们可以使用一个方法 addRoute；

如果我们是为route添加一个children路由，那么可以**传入对应的name：**

```js
// 定义好动态的路由
const categoryRoute = {
  path: "/category",
  component: () => import(/* webpackChunkName:"category-chunk" */"../pages/Category.vue")
}
// 动态添加路由 ，调用路由对象router的addRouter方法
// 该方法添加的路由为顶级路由
router.addRoute(categoryRoute);

const homeMomentRoute = {
  path: "moment",
  component: () => import("../pages/HomeMoment.vue")
}
// 添加二级路由
// 参数一：父路由的name属性值 参数二：被添加的路由
router.addRoute("home", homeMomentRoute);
```

![image-20210831133405377](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210831133405377.png)



### 动态删除路由

**删除路由有以下三种方式：**

- 方式一：**添加一个name相同的路由**； 
- 方式二：通过**removeRoute方法，传入路由的名称**(name属性值)； 
- 方式三：通过**addRoute方法的返回值回调**（返回值是函数，调用这个函数，如果路由存在就会被删除）；

```js
// 定义好动态的路由
const A = {
  path: "/category",
  component: () => import("../pages/A.vue")
}
const B = {
  path: "/category",
  component: () => import("../pages/B.vue")
}
// 添加路由
router.addRoute(A);
// 再次添加相同路径的的路由，会替换调原来的路由
router.addRoute(B);


// 删除路由 删除路由名字name为home的路由
router.removeRoute("home")


// 调用addRouter的回调函数删除路由
const removeRoute = router.addRoute(B);
removeRoute()// 删除我们添加的这个路由，如果路由存在的话
```



路由的其他方法补充：

- **router.hasRoute(routerName)：**检查路由是否存在（**路由的name属性值**）。 
- **router.getRoutes()：**获取一个包含所有路由记录的数组

![image-20210831134601499](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210831134601499.png)



### 路由导航守卫

vue-router 提供的导航守卫主要用来通过跳转或取消的方式守卫导航。

**全局的前置守卫beforeEach是在导航触发时会被回调的：**

它有两个参数：

- **to**：即将进入的路由Route对象； 
- **from**：即将离开的路由Route对象；

它有返回值：

- false：取消当前导航； 
- 不返回或者undefined：进行默认导航； 
- 返回一个路由地址：
  - 可以是一个string类型的路径； 
  - 可以是一个对象，对象中包含path、query、params等信息；

可选的**第三个参数**：**next**

- 在Vue2中我们是通过next函数来决定如何进行跳转的； 
- 但是在Vue3中我们是通过返回值来控制的，不再推荐使用next函数，这是因为开发中很容易调用多次next；

```js
// 比如用户未登录的情况下，
// 我们不应该可以去往用户界面（这个路由应该不能访问）
// beforeEach 路由前置导航守卫 路由跳转前执行
/* 
  to 和 from 参数 都是route对象 都是路由
  to: 要去的目标路由
  from: 从哪个路由跳转来的
  next: 进行路由的放行，vue3/vue-router4.x版本 中不在建议使用，所以也别传递该参数了
*/
// router.beforeEach((to, from, next) => {
router.beforeEach((to, from) => {
  console.log(to);
  // console.log(to.path);
  console.log(from);
  // next()

  // 在没有next参数的情况下，
  // 返回值为true，表示允许路由之间的跳转 进行导航
  // 返回值为false 表示不允许路由之间的跳转 不进行导航
  // 返回值为undefined或者无返回值 进行默认导航，相当于这里面的代码没有任何作用
  // 返回值为字符串（表示一个路由的路径）：去会去往这个返回的路劲
  // 返回值是一个对象，当然必须包含path属性，也可以有query，等属性
  // 比如： {path:"/home",query:{name:"毛毛"}}
  // return true;

  if(to.path.indexOf("/home")!==-1){return "/about"}
  return true;
})
```

### 其他导航守卫

Vue还提供了很多的其他守卫函数，目的都是在某一个时刻给予我们回调，让我们可以更好的控制程序的流程或者功能：

[vue-router](https://next.router.vuejs.org/zh/guide/advanced/navigation-guards.html)

我们一起来看一下完整的导航解析流程：

-  导航被触发。 
- 在失活的组件里调用 beforeRouteLeave 守卫。 
- 调用全局的 beforeEach 守卫。 
- 在重用的组件里调用 beforeRouteUpdate 守卫(2.2+)。 
- 在路由配置里调用 beforeEnter。 
- 解析异步路由组件。 
- 在被激活的组件里调用 beforeRouteEnter。 
- 调用全局的 beforeResolve 守卫(2.5+)。 
- 导航被确认。 
- 调用全局的 afterEach 钩子。 
- 触发 DOM 更新。 
- 调用 beforeRouteEnter 守卫中传给 next 的回调函数，创建好的组件实例会作为回调函数的参数传入。

![image-20210831192840706](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210831192840706.png)

