# Vue-CLI和Vite

## Vue-CLI

### Vue CLI脚手架

**什么是Vue脚手架？**

1. 我们前面学习了如何通过webpack配置Vue的开发环境，但是在真实开发中我们不可能每一个项目从头来完成 所有的webpack配置，这样显示开发的效率会大大的降低； 
2. 所以在真实开发中，我们通常会使用脚手架来创建一个项目，Vue的项目我们使用的就是Vue的脚手架； 
3. 脚手架其实是建筑工程中的一个概念，在我们软件工程中也会将一些帮助我们搭建项目的工具称之为脚手架；

![image-20210803202535806](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803202535806.png)

**Vue的脚手架就是Vue CLI：**

1. CLI是Command-Line Interface, 翻译为命令行界面； 
2. 我们可以通过CLI选择项目的配置和创建出我们的项目； 
3. Vue CLI已经内置了webpack相关的配置，我们不需要从零来配置；

### Vue CLI 安装和使用

#### 安装Vue CLI（4.x）

这边建议安装脚手架的版本在 4.5以上。也可以实时安装最新版。**目前最新的稳定版是4.5.13**

我们是进行全局安装，这样在任何时候都可以通过vue的命令来创建项目；

```shell
npm install @vue/cli -g
```

#### 升级Vue CLI

如果是比较旧的版本，可以通过下面的命令来升级

```shell
npm update @vue/cli -g
```

![image-20210803203850993](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803203850993.png)

**安装成功则可以看见版本号。**

![image-20210803203921186](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803203921186.png)

#### 脚手架的使用

通过Vue的命令来创建项目

```shell
vue create 项目名称
```

### vue create 项目的过程

**通过方向键进行上下移动，空格表示选中，回车则进入下一项的配置。**

1. 输入 `vue create 项目名称` 来创建项目，然后回车。

   ![image-20210803204034383](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803204034383.png)

   ![image-20210803204200868](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803204200868.png)

   **如果是第一次使用脚手架，应该是下面这个样式；**

   ![image-20210803204600259](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803204600259.png)

2. 可以看见有两个default的配置。

   1. 第一个是创建基于vue2的项目。

   2. 第二个是基于vue3的项目。

   3. 第三个，也就是最后一行，选择该项表示我们需要的配置要我们自己指定。**（这里我们选择第三个，然后回车）**

      ![image-20210803204721043](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803204721043.png)

   4. 这一轮是来选择我们需要的插件或者说是特性。

   ![image-20210803205316231](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803205316231.png)

   **这里作为demo只选前两个，回车**

   ![image-20210803205641992](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803205641992.png)

5. 选择我们vue的版本，我们选择vue3，回车

   ![image-20210803205725446](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803205725446.png)

6. 配置选项的配置是否生成自己单独的配置文件，回车

   ![image-20210803205917707](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803205917707.png)

7. 是否将我们前面做的配置和选择作为预设，方便下次直接一步创建项目，**y**表示作为预设，这里我们输入 **n**，不作为预设。

   ![image-20210803210211258](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803210211258.png)

8. 接下来就会帮我们创建项目了

   ![image-20210803210318647](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803210318647.png)

9. 稍等一会就可以发现创建成功了

   ![image-20210803210403126](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803210403126.png)

### 项目的目录结构

![image-20210803211025274](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803211025274.png)

#### 配置详解

##### 适配浏览器的配置

**.browserslistrc**：该文件的作用是设置适配浏览器的范围的。

![image-20210803211344669](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803211344669.png)

```js
> 1% // 市场份额大于 1% 的浏览器
last 2 versions  // 适配最新（后）的两个版本
not dead  // 浏览器的版本还在维护，24个月更新或者维护认为dead
```

**一般情况下我们使用默认的配置就可以了。**

##### git忽略文件

**.gitignore**：提交代码时忽略的文件，文件夹等。

![image-20210803211804505](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210803211804505.png)



##### babel配置文件

```js
module.exports = {
  // vue自己写的预设
  presets: [
    '@vue/cli-plugin-babel/preset'
  ]
}
```



### vue-cli源码

1. 运行 `npm run serve/build`

![image-20210804135045444](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135045444.png)

2. 来到 `node_modules/.bin/vue-cli-service`

   ![image-20210804135222443](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135222443.png)

3. 然后会去到 `@vue/cli-service`里面

   ![image-20210804135313087](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135313087.png)

![image-20210804135333130](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135333130.png)

![image-20210804135405718](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135405718.png)

![image-20210804135439886](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135439886.png)

![image-20210804135450489](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135450489.png)

![image-20210804135510977](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135510977.png)

![image-20210804135529852](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135529852.png)

![image-20210804135545291](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135545291.png)

![image-20210804135616973](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135616973.png)

![image-20210804135633785](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135633785.png)

![image-20210804135728707](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135728707.png)

![image-20210804135822150](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135822150.png)

![image-20210804135847264](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135847264.png)

![image-20210804135906870](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804135906870.png)



## Vite

### 认识Vite

#### 什么是vite

Webpack是目前整个前端使用最多的构建工具，但是除了webpack之后也有其他的一些构建工具：

比如rollup、parcel、gulp、vite等等

**什么是vite呢？ 官方的定位：下一代前端开发与构建工具；**

![image-20210804140326263](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804140326263.png)

**如何定义下一代开发和构建工具呢？**

1. 我们知道在实际开发中，我们编写的代码往往是不能被浏览器直接识别的，比如ES6、TypeScript、Vue文件等 等； 
2. 所以我们必须通过构建工具来对代码进行转换、编译，类似的工具有webpack、rollup、parcel； 
3. 但是随着项目越来越大，需要处理的JavaScript呈指数级增长，模块越来越多； 
4. 构建工具需要很长的时间才能开启服务器，HMR也需要几秒钟才能在浏览器反应出来； 
5. 所以也有这样的说法：天下苦webpack久矣；

**Vite (法语意为 "快速的"，发音 /vit/) 是一种新型前端构建工具，能够显著提升前端开发体验。**

#### Vite的构造

**它主要由两部分组成：**

1. 一个开发服务器，它基于原生ES模块提供了丰富的内建功能，HMR的速度非常快速； 
2. 一套构建指令，它使用rollup打开我们的代码，并且它是预配置的，可以输出生成环境的优化过的静态资源；

#### vite前景

**目前是否要大力学习vite？vite的未来是怎么样的？**

1. 我个人非常看好vite的未来，也希望它可以有更好的发展； 
2. 但是，目前vite虽然已经更新到2.0，依然并不算非常的稳定，并且比较少大型项目（或框架）使用vite来进行 构建； 
3. vite的整个社区插件等支持也还不够完善； 
4. 包括vue脚手架本身，目前也还没有打算迁移到vite，而依然使用webpack（虽然后期一定是有这个打算的）； 
5. 所以vite看起来非常的火热，在面试也可能会问到，但是实际项目中应用的还比较少；

### 浏览器原生支持模块化

![image-20210804142321245](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804142321245.png)

![image-20210804142329557](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804142329557.png)

![image-20210804142341357](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804142341357.png)

![image-20210804142350221](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804142350221.png)

但是如果我们不借助于其他工具，直接使用ES Module来开发有什么问题呢？

1. 首先，我们会发现在使用loadash时，加载了上百个模块的js代码，对于浏览器发送请求是巨大的消耗；
2. 其次，我们的**代码中如果有TypeScript、less、vue等代码时，浏览器并不能直接识别；**

**事实上，vite就帮助我们解决了上面的所有问题。**

**注意：安装一下lodash-es（工具包），来帮助我们管理es模块化。**

```shell
npm install lodash-es 
```

#### lodash-es的使用

直接进行使用，发现**浏览器报错**

![image-20210804143222638](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804143222638.png)

![image-20210804143236443](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804143236443.png)

浏览器并不支持这种直接书写node的模块的语法。

**必须直接导入对应的js文件才可以。**

![image-20210804143420451](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804143420451.png)

![image-20210804143431065](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804143431065.png)

#### 弊端

上面那种导入模块的方式弊端很大。我们可以发现，导入一个第三方模块发起了很多的请求。

![image-20210804143619817](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804143619817.png)

因为lodash-es这个模块的文件依赖了很多其他的文件，所以也会被浏览器请求给加载。**这样是很消耗浏览器性能的。**



### Vite的安装和使用

**注意：Vite本身也是依赖Node的，所以也需要安装好Node环境**

并且Vite要求**Node版本是大于12**版本的；

**首先，我们安装一下vite工具**

```shell
npm install vite –g # 全局安装
npm install vite –D # 局部安装
```

**通过vite来启动项目：**

局部安装启动命令：

```shell
npx vite
```

![image-20210804144910851](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804144910851.png)

**vite 会创建一个本地服务器。**

![image-20210804144944185](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804144944185.png)

**现在导入模块可以不加后缀名了，也可以直接导入第三方模块了。**

![image-20210804145206590](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804145206590.png)

**可以看见浏览器正常支持**

![image-20210804145230838](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804145230838.png)

**也可以发现明显加载的请求资源变少了，意味着不会那么消耗浏览器的性能，并且加载速度也会明显提升。**

![image-20210804145330594](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804145330594.png)





### vite对第三方的支持

#### 1. Vite对css的支持

vite可以直接支持css的处理，直接导入css即可；

![image-20210804145642817](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804145642817.png)

![image-20210804145657089](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804145657089.png)

**vite可以直接支持css预处理器，比如less**

![image-20210804150224428](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804150224428.png)

![image-20210804150209543](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804150209543.png)

1. 直接导入less； 发现报错了。

   ![image-20210804150240951](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804150240951.png)

   ![image-20210804150312741](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804150312741.png)

2. 之后安装less编译器；

   ```shell
   npm install less -D
   ```

   ![image-20210804150713422](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804150713422.png)

**vite直接支持postcss的转换：**

只需要安装postcss，并且配置 postcss.config.js 的配置文件即可；

```shell
npm install postcss postcss-preset-env -D
```

![image-20210804151320335](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804151320335.png)

![image-20210804151325968](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804151325968.png)

![image-20210804151339979](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804151339979.png)



#### 2. Vite对TypeScript的支持

**注意：ts我会在后面的博客中推出的。这里只是简单使用**

vite对TypeScript是原生支持的，它会直接使用ESBuild来完成编译：

**只需要直接导入即可；**

如果我们查看浏览器中的请求，会发现请求的依然是ts的代码：

1. 这是因为vite中的服务器Connect会对我们的请求进行转发； 
2. 获取ts编译后的代码，给浏览器返回，浏览器可以直接进行解析；

**注意：在vite2中，已经不再使用Koa了，而是使用Connect来搭建的服务器**

![image-20210804151707985](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804151707985.png)

![image-20210804151947452](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804151947452.png)

![image-20210804151956647](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804151956647.png)

![image-20210804152011050](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804152011050.png)

vite直接就支持ts文件的书写和直接导入使用。

**也可以发现请求的是ts文件。浏览器的确是不能解析ts的，但是vite帮我们做了请求转发，中间解析了ts**

![image-20210804152122557](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804152122557.png)

![image-20210804153141517](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804153141517.png)

![image-20210804153222121](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804153222121.png)



#### 3. Vite对vue的支持

**vite对vue提供第一优先级支持：**

1. Vue 3 单文件组件支持：@vitejs/plugin-vue 
2. Vue 3 JSX 支持：@vitejs/plugin-vue-jsx 
3. Vue 2 支持：underfin/vite-plugin-vue2

![image-20210804154430894](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804154430894.png)

**页面上需要有id为app的盒子。**

![image-20210804154506453](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804154506453.png)

**直接运行，发现报错，提示我们缺少插件**

![image-20210804154417082](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804154417082.png)

**安装支持vue的插件：**

```shell
npm install @vitejs/plugin-vue -D
```

**在vite.config.js中配置插件：**

```js
const vue = require('@vitejs/plugin-vue')
module.exports = {
  plugins:[
    vue()
  ]
}
```

**此时仍然缺少插件，vue单文件的编译插件**

![image-20210804154937792](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804154937792.png)

```shell
cnpm i @vue/compiler-sfc -D
```

**成功运行**

![image-20210804155433188](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804155433188.png)



#### 4. Vite打包项目

**其实：vite在每次运行项目时，都会进行预打包。**

![image-20210804160344336](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804160344336.png)

我们可以直接通过vite build来完成对当前项目的打包工具：

```shell
npx vite build
```

![image-20210804160021478](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804160021478.png)

![image-20210804160034400](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804160034400.png)

我们可以通过**preview**的方式，开启一个本地服务来预览打包后的效果：

```shell
npx vite preview
```

![image-20210804160129791](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804160129791.png)

**有毛病吗，没有任何毛病！**

![image-20210804160210226](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804160210226.png)

#### 5. ESBuild解析

**ESBuild的特点：**

1. 超快的构建速度，并且不需要缓存； 
2. 支持ES6和CommonJS的模块化； 
3. 支持ES6的Tree Shaking； 
4. 支持Go、JavaScript的API； 
5. 支持TypeScript、JSX等语法编译； 
6. 支持SourceMap； 
7. 支持代码压缩； 
8. 支持扩展其他插件；

##### ESBuild的构建速度

ESBuild的构建速度和其他构建工具速度对比：

![image-20210804160421489](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804160421489.png)

![image-20210804161237324](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804161237324.png)

ESBuild为什么这么快呢？

1. 使用Go语言编写的，可以直接转换成机器代码，而无需经过字节码； 
2. ESBuild可以充分利用CPU的多内核，尽可能让它们饱和运行； 
3. ESBuild的所有内容都是从零开始编写的，而不是使用第三方，所以从一开始就可以考虑各种性能问题； 
4. 等等...

### Vite脚手架工具

在开发中，我们不可能所有的项目都使用vite从零去搭建，比如一个react项目、Vue项目；

这个时候vite还给我们提供了对应的脚手架工具；

**所以Vite实际上是有两个工具的：**

1. vite：相当于是一个构件工具，类似于webpack、rollup； 
2. @vitejs/create-app：类似vue-cli、create-react-app；

**如何使用脚手架工具呢？**

```shell
npm init @vitejs/app
```

**上面的做法相当于省略了安装脚手架的过程：**

```shell
npm install @vitejs/create-app -g

create-app
```



#### vite-cli脚手架创建过程

1. 创建项目，指定项目名称

```shell
create-app 项目名称
```

2. 选择我们需要的框架，vite也提供了多种选择，这里我们选vue，然后回车

   ![image-20210804162300875](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804162300875.png)

3. 这里就先选择不带ts的。

   ![image-20210804162339440](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804162339440.png)

4. 然后就创建成功了

   ![image-20210804162402677](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804162402677.png)

5. 进入项目文件夹

6. 安装依赖（默认是没有帮我们安装依赖的）

   ```shell
   npm install
   ```

7. 启动项目

   ```shell
   npm run dev
   ```

   ![image-20210804163549269](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804163549269.png)

![image-20210804163727684](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210804163727684.png)