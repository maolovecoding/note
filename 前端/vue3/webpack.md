# webpack学习之旅

@[toc]
**本文大部分内容都是出自去年发布的四篇笔记，此文是对当时学习的补充，以及更新的配置做了修改。（更多配置需要参考官方文档）**
## webpack基础打包

### 认识webpack

**事实上随着前端的快速发展，目前前端的开发已经变的越来越复杂了：**

1. 比如开发过程中我们需要通过模块化的方式来开发； 
2. 比如也会使用一些高级的特性来加快我们的开发效率或者安全性，比如通过ES6+、TypeScript开发脚本逻辑， 通过sass、less等方式来编写css样式代码； 
3. 比如开发过程中，我们还希望实时的监听文件的变化来并且反映到浏览器上，提高开发的效率； 
4. 比如开发完成后我们还需要将代码进行压缩、合并以及其他相关的优化； 
5. 等等….

但是对于很多的**前端开发者**来说，并不需要思考这些问题，日常的开发中根本就没有面临这些问题：

1. 这是因为目前前端开发我们通常都会直接使用三大框架来开发：Vue、React、Angular； 
2. 但是事实上，这三大框架的创建过程我们都是借助于脚手架（CLI）的； 
3. 事实上Vue-CLI、create-react-app、Angular-CLI都是基于webpack来帮助我们支持模块化、less、 TypeScript、打包优化等的；



#### 脚手架依赖webpack

**事实上我们上面提到的所有脚手架都是依赖于webpack的：**

![image-20210726131437248](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/639eba873265c762d61686664a172cbd.png)



#### webpack是什么

![image-20210726131238052](https://img-blog.csdnimg.cn/img_convert/1abbfe21cb0b4690953204d6806ae5ba.png)

**我们先来看一下官方的解释：**

![image-20210726131459921](https://img-blog.csdnimg.cn/img_convert/2d51786767c73132ac5dba0e0f31ec0a.png)

webpack是一个**静态的模块化打包工具**，为现代的JavaScript应用程序；

**我们来对上面的解释进行拆解：**

1. **打包bundler**：webpack可以将帮助我们进行打包，所以它是一个打包工具 
2. **静态的static**：这样表述的原因是我们最终可以将代码打包成最终的静态资源（部署到静态服务器）； 
3. **模块化module**：webpack默认支持各种模块化开发，ES Module、CommonJS、AMD等； 
4. **现代的modern**：我们前端说过，正是因为现代前端开发面临各种各样的问题，才催生了webpack的出现和发展；



#### Vue项目加载的文件有哪些

**JavaScript的打包：** 

- 将ES6转换成ES5的语法； 
- TypeScript的处理，将其转换成JavaScript；

 **Css的处理：** 

- CSS文件模块的加载、提取； 
- Less、Sass等预处理器的处理； 

**资源文件img、font：** 

-  图片img文件的加载； 
-  字体font文件的加载； 

**HTML资源的处理：** 

- 打包HTML资源文件； 
- 处理vue项目的SFC文件.vue文件；



#### webpack使用前提

 webpack的官方文档是https://webpack.js.org/ ；

webpack的中文官方文档是https://webpack.docschina.org/ ；

DOCUMENTATION：文档详情，也是我们最关注的

**Webpack的运行是依赖Node环境的，所以我们电脑上必须有Node环境**

所以我们需要先安装Node.js，并且同时会安装npm；

Node官方网站：https://nodejs.org/

**推荐安装LTS版本，也就是稳定版**

![image-20210726131906643](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/07842a8a64971a5c2a6e96b7f394e385.png)





#### webpack的安装

webpack的安装目前分为两个：webpack、webpack-cli。

前期我们多以命令行的方式使用webpack，因此webpack-cli也是必须安装的。但是在实际项目中，一般不需要安装webpack-cli。

**那么它们是什么关系呢？**

1. 执行webpack命令，会执行node_modules下的.bin目录下的webpack； 
2. webpack在执行时是依赖webpack-cli的，如果没有安装就会报错； 
3. 而webpack-cli中代码执行时，才是真正利用webpack进行编译和打包的过程； 
4. 所以在安装webpack时，我们需要同时安装webpack-cli（第三方的脚手架事实上是没有使用webpack-cli的，而是类似于自 己的vue-service-cli的东西）

![image-20210725232410531](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/22895408f053a2f0fb4209bd23ac25f6.png)



```shell
npm install webpack webpack-cli -g # -g 表示全局安装
npm install webpack webpack-cli -D # -D 表示局部安装
```

![image-20210725232640075](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/f294d9ec931519940b3564939acd9c39.png)

在命令行输入上面的命令，会默认安装最新的版本。如果没有报错则安装成功。

我们可以在命令行输入 `webpack -v`查看我们安装的版本。

![image-20210725232812911](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/da6a0ac21bf7e68b863a4d498633c6c8.png)

### webpack的打包方式



#### 1. 默认打包

我们可以通过webpack进行打包，之后运行打包之后的代码

在**项目根目录下**直接执行 webpack 命令；

```shell
webpack
```

**只要没报错，就执行成功了。警告暂时我们可以先忽略**

![image-20210726132343482](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/5d3f5bfe6e2cc0c37ef492d576cb378b.png)

我们发现：

**生成一个dist文件夹，里面存放一个main.js的文件**，就是我们打包之后的文件：

1. 这个文件中的代码被压缩和丑化了；
2. 另外我们发现代码中依然存在ES6的语法，比如箭头函数、const等，这是因为默认情况下webpack并不清楚我们打包后的文 件是否需要转成ES5之前的语法，后续我们需要通过babel来进行转换和设置；

![image-20210726132601754](https://img-blog.csdnimg.cn/img_convert/75360e8e0ab6b92375499b3027d6c8f4.png)

**我们发现是可以正常进行打包的，但是有一个问题，webpack是如何确定我们的入口的呢？**

1. 事实上，当我们运行webpack时，webpack会查找当前目录下的 src/index.js作为入口； 
2. 所以，如果当前项目中没有存在src/index.js文件，那么会报错

**当然，我们也可以通过配置来指定入口和出口**

安装好局部的webpack，但是我们又安装了全局的webpack了，这时候如果在命令行直接输入webpack命令进行打包。默认使用的还是全局安装的webpack。

**如何使用局部的webpack进行打包呢？**

可以使用 `npx webpack`**使用该命令打包的时候就会使用局部的webpack**

```shell
# 调用当前项目的webpack，并指定打包的入口文件和出口目录和文件
npx webpack --entry ./src/main.js --output-path ./build
```



#### 2. 创建局部的webpack

前面我们直接执行webpack命令使用的是全局的webpack，如果希望使用局部的可以按照下面的步骤来操作。

第一步：创建package.json文件，用于管理项目的信息、库依赖等

```shell
npm init -y # -y 表示按照默认参数进行初始化
```

第二步：安装局部的webpack

```shell
npm install webpack webpack-cli -D
```

第三步：使用局部的webpack

```shell
npx webpack # 会去调用node_moudles目录下bin里面的webpack目录
```

第四步：在package.json中创建scripts脚本，执行脚本打包即可

**虽然第三步也可以使用局部的webpack进行打包。但是我们一般很少使用**。*我们可以在`package.json`文件里面的`script`属性里面配置*

```json
"scripts": {
    "build": "webpack"
  },
```

**然后执行 `npm run build`**目录进行打包

```shell
npm run build
```

  会自动去我们的node_modules文件夹下面找命令，也就是局部的webpack



#### 3. Webpack配置文件

在通常情况下，webpack需要打包的项目是非常复杂的，并且我们需要一系列的配置来满足要求，默认配置必然 是不可以的。我们可以在根目录下创建一个**webpack.config.js**文件，来作为**webpack的配置文件**：

```js
// 配置webpack

const path = require('path');

// 导出配置必须使用commonjs规范。因为webpack是运行在nodejs环境上的
// 读取导出的配置肯定是使用nodejs默认的规范的。
// 如果想要使用ES6的规范进行导出，需要进行额外的配置。比较麻烦
module.exports = {
  // 入口 entry  打包文件时的入口文件
  entry: './src/index.js',
  // 出口 打包好文件存放的位置，路径
  output: {
    // path属性 需要指定绝对路径
    path: path.join(__dirname, './build'),
    // 打包后文件的名称
    filename: 'bundle.js'
  }
}
```

继续执行webpack命令，依然可以正常打包

```shell
npm run build
```

#### 4. 指定配置文件

但是如果我们的配置文件并不是webpack.config.js的名字，而是其他的名字呢？

1. 比如我们将webpack.config.js修改成了 wk.config.js

2. 这个时候我们可以通过 --config 来指定对应的配置文件；

   ```shell
   wepack --config wk.config.js
   ```

3. 但是每次这样执行命令来对源码进行编译，会非常繁琐，所以我们可以在package.json中增加一个新的脚本：

   ![image-20210726134001052](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/ce6989cc0af246b54482a5ecfa9045b3.png)

   ```json
   "srcipt":{
       "build":"wepack --config wk.config.js"
   }
   ```

**之后我们执行 `npm run build`来打包即可。**



#### 5. Webpack的依赖图

webpack到底是如何对我们的项目进行打包的呢？ 

1. 事实上webpack在处理应用程序时，它会根据命令或者配置文件找到入口文件； 

2. 从入口开始，会生成一个 依赖关系图，这个依赖关系图会包含应用程序中所需的所有模块（比如.js文件、css文件、图片、字 体等）； 
3. 然后遍历图结构，打包一个个模块（根据文件的不同使用不同的loader来解析）；

![image-20210726134147625](https://img-blog.csdnimg.cn/img_convert/542d81d5dc16e4ceb79ca693c32ab848.png)



### webpack打包其他文件

这里我们创建一个 `element.js` 文件，以及一个 `index.css`文件。通过JavaScript创建了一个元素，并且希望给它设置一些样式；

```js
// element.js

import '../css/index.css';
// 创建一个div
const div = document.createElement('div');
// 设置样式
div.className = 'title1';
div.className = 'title';
div.innerHTML = '<h2>你好!!!!!!</h2>';

document.body.appendChild(div);
```

```css
.title{
  color:aqua;
  font-size: 30px;
  background-color: bisque;
}
```

执行编译打包命令（`npm run build`）时：

![image-20210726134628990](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/4e954ddaaa053aa580f9699a78c27ac2.png)



#### 1. css-loader的使用

上面的错误信息告诉我们需要一个**loader来加载这个css文件**，但是loader是什么呢？

1. loader 可以用于对模块的源代码进行转换； 
2. 我们可以将css文件也看成是一个模块，我们是通过import来加载这个模块的； 
3. 在加载这个模块时，webpack其实并不知道如何对其进行加载，我们必须制定对应的loader来完成这个功能；

那么我们需要一个什么样的loader呢？ 

1. 对于加载css文件来说，我们需要一个可以读取css文件的loader； 

2. 这个loader最常用的是css-loader。

**css-loader的安装：**

```shell
npm install css-loader -D
```

##### css-loader的使用方案

如何使用这个loader来加载css文件呢？有三种方式：

1. 内联方式； 
2. CLI方式（webpack5中不再使用）； 
3. 配置方式

###### 内联方式

内联方式使用较少，因为不方便管理；

**在引入的样式前加上使用的loader，并且使用!分割**

```js
// 导入css
// 内联样式指定加载文件时使用的loader (了解)
// 语法： import 'loader!文件路径'  要注意loader后面有一个叹号 是用来分割加载器和文件的 必须有
import 'css-loader!../css/index.css';
```

###### CLI方式

1. 在webpack5的文档中已经没有了--module-bind； 
2. 实际应用中也比较少使用，因为不方便管理；

###### loader配置方式(重点)

**配置方式表示的意思是在我们的webpack.config.js文件中写明配置信息：**

1. module.rules中允许我们配置多个loader（因为我们也会继续使用其他的loader，来完成其他文件的加载）； 
2. 这种方式可以更好的表示loader的配置，也方便后期的维护，同时也让你对各个Loader有一个全局的概览；

**module.rules的配置如下：**

- rules属性对应的值是一个数组：[Rule]
- 数组中存放的是一个个的Rule，Rule是一个对象，对象中可以设置多个属性：
  - **test属性：**用于对 resource（资源）进行匹配的，通常会设置成正则表达式；
  - **use属性：**对应的值时一个数组：[UseEntry]
    - UseEntry是一个对象，可以通过对象的属性来设置一些其他属性
      - oader：必须有一个 loader属性，对应的值是一个字符串； 
      - options：可选的属性，值是一个字符串或者对象，值会被传入到loader中； Ø
      - query：目前已经使用options来替代；
    - 传递字符串（如：use: [ 'style-loader' ]）是 loader 属性的简写方式（如：use: [ { loader: 'style-loader'} ]）；
  - **loader属性**： Rule.use: [ { loader } ] 的简写。

**具体的相关配置如下展示：**

![image-20210726135426942](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/d8b2b27a0aa5f7a1b28248d1228b0087.png)



#### 2. style-loader

我们已经可以通过css-loader来加载css文件了 。但是你会发现这个css在我们的代码中并没有生效（页面没有效果）。

**这是为什么呢？**

1. 因为css-loader只是负责将.css文件进行解析，并不会将解析之后的css插入到页面中；
2. 如果我们希望再完成插入style的操作，那么我们还需要另外一个loader，就是style-loader；

![image-20210726135858149](https://img-blog.csdnimg.cn/img_convert/092dc53653bc7c1347beb4bdcba7cf51.png)

**安装style-loader**

```shell
npm i style-loader -D
```

##### 配置style-loader

那么我们应该如何使用style-loader：

1. 在配置文件中，添加style-loader； 
2. 注意：因为loader的执行顺序是从右向左（或者说从下到上，或者说从后到前的），所以我们需要将styleloader写到css-loader的前面

![image-20210726135651286](https://img-blog.csdnimg.cn/img_convert/c39bcc247cd5953317537e137bf06342.png)

重新执行编译npm run build，可以发现打包后的css已经生效了：

1. 当前目前我们的css是通过页内样式的方式添加进来的；
2. 后续我们也会讲如何将css抽取到单独的文件中，并且进行压缩等操作；

![image-20210726135948555](https://img-blog.csdnimg.cn/img_convert/110cb6ec2b9bbf934c34dddad06e311f.png)



#### 3. 如何处理less文件

在我们开发中，我们可能会使用less、sass、stylus的预处理器来编写css样式，效率会更高。

那么，如何可以让我们的环境支持这些**预处理器**呢？

首先我们需要确定，less、sass等编写的css需要通过工具转换成普通的css；

比如我们编写如下的less样式：

```less
@bgColor:#aca;
@textDecoration:underline;

.title{
  background-color: @bgColor;
  text-decoration: @textDecoration;
  user-select: none;
}
```

##### Less工具处理

我们可以使用less工具来完成它的编译转换：

```shell
npm install less -D
```

执行如下命令：

```shell
npx lessc ./src/less/index.less index.css
```

![image-20210726140253295](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/7342471204cfc25a226437c6df3ee7cb.png)



##### less-loader

但是在项目中我们会编写大量的css，它们如何可以自动转换呢？

这个时候我们就可以使用less-loader，来自动使用less工具转换less到css；

```shell
npm install less-loader -D
```

**配置webpack.config.js**

![image-20210726140357318](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/dd39990fc89d60ddb258eab926ad631a.png)

执行npm run build less就可以自动转换成css，并且页面也会生效了

![image-20210726140554154](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/f7243a1e57b004324f5c2f9ee9ec58a6.png)

#### 4. PostCSS工具

##### 1. 认识postcss

1. 什么是PostCSS呢？ 

   - PostCSS是一个通过JavaScript来转换样式的工具； 
   - 这个工具可以帮助我们进行一些CSS的转换和适配，比如自动添加浏览器前缀、css样式的重置； 
   - 但是实现这些功能，我们需要借助于PostCSS对应的插件；

   

2. 如何使用PostCSS呢？主要就是两个步骤：

   1. 第一步：查找PostCSS在构建工具中的扩展，比如webpack中的postcss-loader；
   2. 第二步：选择可以添加你需要的PostCSS相关的插件；

##### 2. 命令行使用postcss

当然，我们能不能也直接在终端使用PostCSS呢？

也是可以的，但是我们需要单独安装一个工具postcss-cli；

我们可以安装一下它们：postcss、postcss-cli：

```shell
npm install postcss postcss-cli -D
```

我们编写一个需要添加前缀的css：

1. https://autoprefixer.github.io/
2. 我们可以在上面的网站中查询一些添加css属性的样式

![image-20210726140956883](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/c37d56d727ac1936835d82171c66d86c.png)

##### 3. 插件autoprefixer

因为我们需要添加前缀，所以要安装autoprefixer：

```shell
npm install autoprefixer -D
```

直接使用使用postcss工具，并且指定使用autoprefixer

```shell
npx postcss --use autoprefixer -o end.css ./src/css/style.css
```

转化之后的css样式如下：

![image-20210726141124302](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/2f3f4463b227097139e5367c324af389.png)

##### 4. postcss-loader(重点)

真实开发中我们必然不会直接使用命令行工具来对css进行处理，而是可以借助于构建工具：

在webpack中使用**postcss**就是使用**postcss-loader**来处理的；

我们来安装postcss-loader：

```shell
npm install postcss-loader -D
```

我们修改加载less的loader:

**注意：**因为postcss需要有对应的插件才会起效果，所以我们需要配置它的plugin；

![image-20210726141409491](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/064fe605ddee6fd8964ead9c9a6ec880.png)

##### 5. 单独的postcss配置文件

当然，我们也可以将这些配置信息放到一个单独的文件中进行管理：

**在根目录下创建postcss.config.js**

```js
module.exports = {
  // 配置plugins 插件
  plugins: [
    require('autoprefixer')
  ]
}
```

![image-20210726141556045](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/b17acc952bcb1d313877df74a809708c.png)



##### 6. postcss-preset-env

事实上，在配置postcss-loader时，我们配置插件并不需要使用autoprefixer。

我们可以使用另外一个插件：postcss-preset-env

1. postcss-preset-env也是一个postcss的插件； 
2. 它可以帮助我们将一些现代的CSS特性，转成大多数浏览器认识的CSS，并且会根据目标浏览器或者运行时环境 添加所需的polyfill； 
3. 也包括会自动帮助我们添加autoprefixer（所以相当于已经内置了autoprefixer）；

首先，我们需要**安装postcss-preset-env**：

```shell
npm install postcss-preset-env -D
```

之后，我们直接修改掉之前的autoprefixer即可：

![image-20210726141730953](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/f893d248e5187979d1b0164768d58c62.png)

**注意：我们在使用某些postcss插件时，也可以直接传入字符串**

![image-20210726141811408](https://img-blog.csdnimg.cn/img_convert/730b720ae7eb64afe2d3c00edff87b89.png)



### webpack.config.js本次配置展示

```js
// 配置webpack

const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.join(__dirname, './build'),
    filename: 'bundle.js'
  },
  // 配置打包时所有模块的配置 在 module属性下配置
  // 一个文件也看成一个模块
  module: {
    // 模块打包时的规则
    // 不同文件有不同的规则，所以规则是一个数组，可以配置不同文件不同的规则
    rules: [
      // 配置打包css文件（模块）时的规则
      {
        test: /\.css$/, // 指定文件后缀  正则表达式
        // 指定loader (语法糖)
        // loader: 'css-loader'

        // 只有一个loader时可以不使用数组，直接指定就行
        // use: 'css-loader'
        // 没有其他参数时：只有loader时，直接使用数组，里面放的默认都是loader
        // use: ['css-loader']
        // 如果当前使用的loader有相关的其他配置，需要在数组里面将元素指定为对象
        //use: [
        // 多个loader就是多个对象，一个对象里面一个loader，以及loader的配置 options
        //{ loader: 'css-loader', options: { modules: true } }
        //]

        // 多个loader的加载顺序 从后往前（最先加载数组最后一个loader）
        use: [
          "style-loader",
          { loader: 'css-loader' }
        ]
      },
      // 处理less文件的配置
      // postcss 工具 增加浏览器前缀 postcss-loader
      {
        test: /\.less$/,
        use: [
          'style-loader',
          'css-loader',
          // 如果我们指定了postcss的配置文件（默认情况下叫做 postcss.config.js）
          // 则不需要再这里指定相关的配置了，直接写postcss-loader就可以了
          // 优先读取本文件里面的配置，然后去 postcss.config.js 配置文件里面读取导出的配置
          'postcss-loader',
          /* {
            loader: 'postcss-loader',
            // 配置postcss-loader的加载时的参数，选项
            options: {
              postcssOptions: {
                // 插件可以指定多个 数组形式
                plugins: [
                  // 导入使用的插件 使用require(插件)
                  require('autoprefixer')
                ]
              }
            }
          }, */
          'less-loader'
        ]
      }
    ]
  }
}
```

### postcss.config.js本次配置展示

```js
// postcss 的配置
module.exports = {
  // 配置plugins 插件
  plugins: [
    // 实际开发中，我们很少使用 autoprefixer 这个插件
    // 我们会使用 更好的插件 postcss-prefix-env 
    // require('autoprefixer')
    // 这个插件的功能更强，切也具有 autoprefixer 插件的功能 
    // 可以理解为具备了 autoprefixer 的功能
    require('postcss-preset-env')
  ]
}
```



## Webpack打包其他资源

**注意：在使用webpack5新特性的模块资源加载(asset)的时候，需要注意使用cmd引入图片资源等的时候，不需要在require()的后面使用default属性来获取真正的资源了。直接使用require获取到的就是真正需要的资源。而在file-loader等处理的时候，还是需要default的。**

**加载图片案例准备**

为了演示我们项目中可以加载图片，我们需要在项目中使用图片，比较常见的使用图片的方式是两种：

1. img元素，设置src属性； 
2. 其他元素（比如div），设置background-image的css属性；

```css
.image-bg {
  background-image: url("../image/3.jpg");
  width: 300px;
  height: 300px;
}
```

```js
// 导入背景图片的样式
import '../css/index.css'
import myImg from '../image/1.JPG'
// 设置背景图片
const bgDiv = document.createElement('div');
bgDiv.className = 'image-bg';

// 设置img标签的src属性
const img = document.createElement('img');
img.src = myImg;

document.body.appendChild(bgDiv);
document.body.appendChild(img);
```

**这个时候，打包会报错**

![image-20210728191425399](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/482b20107d8152f082a6eb39d1b4391c.png)

报错了，并且提示我们可以需要一个loader来解决图片的问题。

### file-loader

如需从 asset loader 中排除来自新 URL 处理的 asset，请添加 `dependency: { not: ['url'] }` 到 loader 配置中。

要处理jpg、png等格式的图片，我们也需要有对应的**loader：file-loader**

1. file-loader的作用就是帮助我们处理import/require()方式引入的一个文件资源，并且会将它放到我们输出的文 件夹中； 
2. 当然我们待会儿可以学习如何修改它的名字和所在文件夹；

**安装file-loader：**

```shell
npm i file-loader -D
```

配置处理图片的Rule：

```js
// 配置打包图片资源的规则
{
    test: /\.(jpg|png|jpeg|JPG)$/,
        dependency: { not: ['url'] },
    use: 'file-loader'
}
```

![image-20210728191711488](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/660ea7f59906eab1aa35909d6adff4ff.png)

我们会发现图片也被打包了。

![image-20210728191748775](https://img-blog.csdnimg.cn/img_convert/b9d511e7bc8ba231691c0fb8bb57d101.png)

![image-20210728191838557](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/22dc29cb040c1e17269272dfa7347c7c.png)

#### 文件的命名规则

有时候我们处理后的**文件名**称按照一定的规则进行显示： 

- 比如保留原来的文件名、扩展名，同时为了防止重复，包含一个hash值等；

这个时候我们可以使用PlaceHolders来完成，webpack给我们提供了大量的PlaceHolders来显示不同的内容：

1.  https://webpack.js.org/loaders/file-loader/#placeholders 
2.  我们可以在文档中查阅自己需要的placeholder；

我们这里介绍几个最常用的placeholder：

- **[ext]**： 处理文件的扩展名； 
- **[name]**：处理文件的名称； 
- **[hash]**：文件的内容，使用MD4的散列函数处理，生成的一个128位的hash值（32个十六进制）； 
- [contentHash]：在file-loader中和[hash]结果是一致的（在webpack的一些其他地方不一样，后面会讲到）； 
- **[hash:length]**：截图hash的长度，默认32个字符太长了； 
- **[path]**：文件相对于webpack配置文件的路径；

#### 设置文件的名称

那么我们可以按照如下的格式编写：**这个也是vue的写法；**

```js
{
        test: /\.(jpg|png|jpeg|JPG)$/,
        use: {
          loader: 'file-loader',
          // file-loader的参数配置
          options: {
            // 打包后图片文件所在的路径
            // outputPath: 'image',
            // 打包后生成文件的名称
            // [name] 源文件的名称（不含拓展名）
            // 我们发现不写配置时，文件的名称过长（且文件名是通过hash算法算出来的，为了防止重名）
            // [hash:6] 默认生成文件名称的hash算法，32位，取前六位
            // [ext] 使用原来文件的拓展名
            // name: '[name]_[hash:6].[ext]'

            // 当然：我们也可以把图片存放的路径和图片名称合在一起
            name: 'image/[name]-[hash:6].[ext]'
          }
        }
      }
```

![image-20210728192329730](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/9b6b8231fc2a2cf0e40a46a20baad7fc.png)

#### 设置文件的存放路径

当然，我们刚才通过 img/ 已经设置了文件夹，这个也是vue、react脚手架中常见的设置方式：

1. 其实按照这种设置方式就可以了； 
2. 当然我们也可以通过**outputPath**来设置输出的文件夹；

![image-20210728192445432](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/45f477a3541d346b36d035c618791f97.png)

### url-loader

**url-loader和file-loader**的工作方式是相似的，但是可以将较小的文件，转成base64的URI。

**安装url-loader：**

```shell
npm i url-loader -D
```

```js
{
        test: /\.(jpe?g|JPG|png)$/,
        use: {
          loader: 'url-loader',
          options: {
            name: 'image/[name]-[hash:6].[ext]',
            // 设置图片大小的限制，只有小于多少kb的图片我们才会转为base64格式
            // 如果大于这个限制 我们就不会转为base64格式。而是继续采用上面的path和name的形式打包，还是保持图片的形式不变
            limit: 100 * 1024 // 小于100kb的文件才会转为base64格式
          }
        }
      }
```

![image-20210728192652383](https://img-blog.csdnimg.cn/img_convert/3b0acf9cbd4e98b16357b2e57d5280a3.png)

**显示结果是一样的，并且图片可以正常显示；**

但是在dist文件夹中，我们会看不到 **大于100kb** 图片文件：

![image-20210728192839738](https://img-blog.csdnimg.cn/img_convert/eab577daa80e27b5c3be16c161fe3bea.png)

1. 这是因为我的两张图片的大小分别是38kb和129kb；
2. 默认情况下url-loader会将所有的图片文件转成base64编码。但是配置了limit以后就可以限制小于多少kb的图片才会转为base64格式。

**可以看见bundle.js文件里面的确有base64格式的字符串**

![image-20210728193159584](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/869a540031fb2d6044e746ee1588141f.png)

### url-loader的limit

但是开发中我们往往是小的图片需要转换，但是**大的图片直接使用图片即可**

1. 这是因为小的图片转换base64之后可以和页面一起被请求，减少不必要的请求过程； 
2. 而大的图片也进行转换，反而会影响页面的请求速度；

那么，我们如何可以**限制哪些大小的图片**转换和不转换呢？

1. url-loader有一个options属性limit，可以用于设置转换的限制； 
2. 下面的代码38kb的图片会进行base64编码，而129kb的不会；

![image-20210728193333744](https://img-blog.csdnimg.cn/img_convert/efdfc742b6660c6da5d7107689b9330d.png)

### asset module type

#### 认识asset module type

**我们当前使用的webpack版本是webpack5：**

1. 在webpack5之前，加载这些资源我们需要使用一些loader，比如**raw-loader 、url-loader、file-loader；** 
2. 在webpack5开始，我们可以直接使用**资源模块类型**（asset module type），来替代上面的这些loader；

**资源模块类型(asset module type)，通过添加 4 种新的模块类型，来替换所有这些 loader：**

1. **asset/resource** ：发送一个单独的文件并导出 URL。之前通过使用 file-loader 实现； 
2. **asset/inline** ：导出一个资源的 data URI。之前通过使用 url-loader 实现； 
3. **asset/source** ：导出资源的源代码。之前通过使用 raw-loader 实现； 
4. **asset**： 在导出一个 data URI 和发送一个单独的文件之间自动选择。之前通过使用 url-loader，并且配置资源体 积限制实现；

#### asset module type的使用

比如加载图片，我们可以使用下面的方式：

![image-20210728193636774](https://img-blog.csdnimg.cn/img_convert/7825757332e73c00a21de372621fd7d1.png)

**发现依然打包成功！**

![image-20210728193748026](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/2eee481decef15ed2a13e7245fbac15a.png)

**但是，如何可以自定义文件的输出路径和文件名呢？**

方式一：修改output，添加assetModuleFilename属性； **（了解即可）**

![image-20210728193854459](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/b13714c0516115cf3897f283ec1cf33f.png)

方式二：在Rule中，添加一个generator属性，并且设置filename；

```js
{
        test: /\.(JPG|jpe?g|png)$/,
        // 这里不在使用use属性 而是type属性
        // 使用asset/resource 来代替 file-loader
        // type:'asset/resource'

        // 比较常用的我们一般直接写成 asset
        type: 'asset',
        // 下面的配置也可以放在 output里面（了解）
        // generator属性 生成 也就是打包后的图片存放时相关的配置
        generator: {
          // 配置图片名称和图片位置 
          // 这里的 [ext] 拿到的文件拓展名包含 .
          filename: 'image/[name]-[hash:6][ext]'
        }
      }
```

![image-20210728194023352](https://img-blog.csdnimg.cn/img_convert/1f93e60cbc9d5957c0cbc2dd8e46d065.png)

#### url-loader的limit效果

我们需要两个步骤来实现：

1. 将type必须使用asset属性值；
2. 添加一个parser属性，并且制定dataUrl的条件，添加maxSize属性；

```js
{
        test: /\.(JPG|jpe?g|png)$/,
        // 比较常用的我们一般直接写成 asset
        type: 'asset',
        // 配置相关的asset参数 使用parser属性
        parser: {
          // 数据url条件
          dataUrlCondition: {
            // 最大不超过多少kb的图片 我们转为 base64格式
            maxSize: 100 * 1024
          }
        },
        generator: {
          filename: 'image/[name]-[hash:6][ext]'
        }
      }
```

![image-20210728194251709](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/193585b1e40dac77959df1ef5b6ebf72.png)

**发现打包的只有一个图片文件了。较小的那个已经转为base64格式了**

![image-20210728194347840](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/c8d87aeecd0c822ffa5ee27125320492.png)



### 加载字体文件

如果我们需要使用某些**特殊的字体或者字体图标**，那么我们会引入很多字体相关的文件，这些文件的处理也是一样 的

**首先，我从阿里图标库中下载了几个字体图标：**

![image-20210728195048166](https://img-blog.csdnimg.cn/img_convert/6d7e8f7024dbb82b14555790ed1d7aae.png)

**然后在font.js里面引入**，并设置一个 `i`标签用于显示字体图标

```js
// 引入并使用字体图标

// 加载字体图标的文件
import '../font/iconfont.css'

// i标签
const i = document.createElement('i');
i.className = 'iconfont icon-ashbin';

document.body.appendChild(i);
```

**然后我们开始打包**

![image-20210728195219912](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/335595ae7031324d1cc7c3787bf5356b.png)

**毫无疑问，必然报错，因为我们使用了webpack并不认识的模块。并且webpack也提醒我们需要使用一个loader来解决。**

#### 字体的打包

这个时候打包会报错，因为**无法正确的处理eot、ttf、woff等文件**：

我们可以选择使用**file-loader**来处理，也可以选择直接使用**webpack5的资源模块类型**来处理；

```js
// 配置字体和字体图标等文件打包时的规则
      {
        test: /\.(eot|ttf|woff2?)$/,
        use:{
          loader: 'file-loader',
          options:{
            name: 'font/[name]-[hash:6].[ext]'
          }
        }
      }
```

**打包之后，发现不会报错，字体图标正常显示。**

![image-20210728200328258](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/4e40aa3818442b5f8bb6e8131c6ac805.png)

**使用webpack5提供的资源模块类型也可以。**

```js
// 也可以使用webpack提供的资源模块类型
{
    test: /\.(eot|ttf|woff2?)$/,
    type: 'asset/resource',
    generator:{
        filename: 'font/[name]-[hash:6][ext]'
    }
}
```



### webpack的plugin

#### 认识plugin

Webpack的另一个核心是Plugin，官方有这样一段对Plugin的描述：

**While loaders are used to transform certain types of modules, plugins can be leveraged to perform a  wider range of tasks like bundle optimization, asset management and injection of environment  variables.**

上面表达的含义翻译过来就是：

1. Loader是用于**特定的模块类型进行转换**； 
2. Plugin可以用于**执行更加广泛的任务**，比如打包优化、资源管理、环境变量注入等；

![image-20210728205205354](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/879a121456ee62eca595c7d876c76795.png)

#### CleanWebpackPlugin

前面我们演示的过程中，每次修改了一些配置，重新打包时，都需要手动删除dist文件夹： 

我们可以借助于一个插件来帮助我们完成，这个插件就是**CleanWebpackPlugin；**

**首先，我们先安装这个插件：**

```shell
npm i clean-webpack-plugin -D
```

之后在插件中配置：

```js
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
module.exports = {
  // 提供 plugins属性来配置插件
  // plugins属性是数组，数组元素是一个个的插件对象
  plugins: [
    new CleanWebpackPlugin()
  ]
}
```

![image-20210728205459482](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/c5f7c9adc355a98c41bc0381dda213c4.png)

**然后可以发现每次都会帮我们把上次打包的文件夹先删除，然后在进行重新打包。**

#### HtmlWebpackPlugin

另外还有一个**不太规范**的地方：

1. 我们的HTML文件是编写在根目录下的，而最终打包的dist文件夹中是没有index.html文件的。 
2. 在进行项目部署的时，必然也是需要有对应的入口文件index.html； 
3. 所以我们也需要对index.html进行打包处理；

对HTML进行打包处理我们可以使用另外一个插件：**HtmlWebpackPlugin；**

```shell
npm i html-webpack-plugin -D
```

![image-20210728205744529](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/4d901bcf6f21258eb6129ba444b1d386.png)

![image-20210728205754385](https://img-blog.csdnimg.cn/img_convert/a5fffaf54641c46a7ebf5fe6f85f384c.png)

##### 生成index.html分析

我们会发现，现在自动在build文件夹中，生成了一个**index.html**的文件：

该文件中也自动添加了我们打包的bundle.js文件；

![image-20210728210054115](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/c5bcd744ad553001d998ca51ee8ad478.png)

![image-20210728205905823](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/e686663e3585693f07ce89078b5cab36.png)

**这个文件是如何生成的呢？**

1. **默认情况**下是根据**ejs的一个模板**来生成的； 
2. 在html-webpack-plugin的源码中，有一个default_index.ejs模块；

##### 自定义HTML模板

如果我们想在自己的模块中加入一些比较特别的内容：

1. 比如添加一个noscript标签，在用户的JavaScript被关闭时，给予响应的提示；

2. 比如在开发vue或者react项目时，我们需要一个可以挂载后续组件的根标签 

   ；

**这个我们需要一个属于自己的index.html模块：**

**下面是模板代码（使用Vue-cli脚手架开发时的默认的模板就是这个）**

```html
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

**但是：使用这个模板直接进行打包的时候：会发现打包失败！！（使用html-webpack-plugin插件的时候需要指定该模板）**

![image-20210728211538093](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/b342cc66118245b2b26d05618232ef6b.png)

![image-20210728212035161](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/32702c6b9af49440c84dbcbc91b6676d.png)

##### 自定义模板数据填充

上面的代码中，会有一些类似这样的语法**<% 变量 %>**，这个是EJS模块填充数据的方式。

在配置HtmlWebpackPlugin时，我们可以添加如下配置：

1. **template**：指定我们要使用的模块所在的路径； 
2. **title**：在进行htmlWebpackPlugin.options.title读取时，就会读到该信息；

![image-20210728212132479](https://img-blog.csdnimg.cn/img_convert/60f31162e196ac9a1e0fd54b6eb724b7.png)

**但是，我们会发现，即使配置了模板啊，标题啊，仍然打包还是报错。**

不要着急，我们可以使用webpack内置的一个插件来进行解决。



#### DefinePlugin

##### DefinePlugin的介绍

我们上面指定了模板以后，发现仍然还是报错。报错的原因是什么呢？

**因为在我们的模块中还使用到一个BASE_URL的常量：**

![image-20210728212410783](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/c1c186f40b6c02484ce1dbacfa239bd9.png)

这是因为在编译template模块时，有一个**BASE_URL**：

```html
<link rel="icon" href="<%= BASE_URL %>favicon.ico">
```

但是我们并没有设置过这个常量值，所以会出现没有定义的错误；

这个时候我们可以使用**DefinePlugin插件**；



##### DefinePlugin的使用

DefinePlugin允许在编译时创建配置的**全局常量**，是一个webpack内置的插件（不需要单独安装）：

![image-20210728213427918](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/ad3c65faac9c5a9d39d4fefdd94bfd4a.png)

![image-20210728213438757](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/faadb74252901c40a093432ad804fdb4.png)

```js
// 导入 DefinePlugin 插件 解决常量 BASE_URL 的问题
const { DefinePlugin } = require('webpack');
module.exports = {
  // 提供 plugins属性来配置插件
  // plugins属性是数组，数组元素是一个个的插件对象
  plugins: [
    // 使用 DefinePlugin 插件来定义 常量
    new DefinePlugin({
      BASE_URL: "'./'"
    })
  ]
```

这个时候，编译template就可以正确的编译了，会读取到**BASE_URL**的值；

##### DefinePlugin的注意点

```js
new DefinePlugin({
      // BASE_URL 会根据后面给的属性值 去本文件上下文中找这个变量
      // 比如我这里给的是字符串 "filepath"
      // 也会去上下文中找叫做 filepath 的变量
      // 如果我们想直接给一个字符串的值
      // 我们需要在双引号里面嵌套单引号赋值
      // 当然 单引号里面嵌套双引号赋值也是可以的
      // BASE_URL: "filepath",
      BASE_URL: "'./'"
    })
```



### CopyWebpackPlugin

在vue的打包过程中，如果我们将一些文件放到public的目录下，那么这个目录会被复制到dist文件夹中。这个复制的功能，我们可以使用CopyWebpackPlugin来完成；

**安装CopyWebpackPlugin插件：**

```shell
npm i copy-webpack-plugin -D
```

**接下来配置CopyWebpackPlugin即可：**

1. 复制的规则在patterns中设置； 
2. **from**：设置从哪一个源中开始复制； 
3. **to**：复制到的位置，可以省略，会默认复制到打包的目录下； 
4. **globOptions**：设置一些额外的选项，其中可以编写需要忽略的文件：
   - .DS_Store：mac目录下回自动生成的一个文件； 
   - index.html：也不需要复制，因为我们已经通过HtmlWebpackPlugin完成了index.html的生成；

```js
const CopyWebpackPlugin = require('copy-webpack-plugin');
module.exports = {
    plugins:[
        // 其他省略
        new CopyWebpackPlugin({
            patterns: [
        {
          // 复制的来源
          from: 'public',
          to: '',
          // 全局配置  globOptions
          globOptions: {
              // 想要忽略掉指定的文件 需要在前面加上 ** ，这样才能保证我们真的忽略掉指定文件夹下的该指定文件
            ignore: '**/index.html'
          }
        }
      ]
        })
    ]
}
```

**可以看到效果和我们的配置的一样。**

![image-20210728215659702](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/3e8ca454b21e6ecb9bfa7bf8c84821ef.png)



#### 配置注意事项

需要把`CopyWebpackPlugin` 插件在插件选项中的配置位置，在`HtmlWebpackPlugin`插件前面书写。

```js
// 拷贝 public文件夹下的内容到打包文件夹下面
    new CopyWebpackPlugin({
      // 需要匹配的对象 可以是多个文件夹
      patterns: [
        {
          from: resolve("public"),
          // to: resolve("build"),// 可以不写 会自动打包到该目录下
          // 需要忽略的该文件夹下的内容
          globOptions: {
            dot: true,
            gitignore: true,
            ignore: [
              "**/index.html",
              "**/test.txt"
            ]
          }
        }
      ]
    }),
    new CleanWebpackPlugin(),
    // 可以配置 标题等属性
    new HtmlWebpackPlugin({
      filename: "index.html",
      title: "mao webpack",
      // 自定义模板
      template: "./public/index.html",
    }),
    // 注入模板引擎需要的 BASE_URL变量 注入的变量在项目中是全局的变量
    new DefinePlugin({
      BASE_URL: "'./'" // 注入的变量值需要加引号的
    }),
  ]
```



### Mode配置

**前面我们一直没有讲mode。**

Mode配置选项，可以告知webpack使用**响应模式**的内置优化：

1. 默认值是production（什么都不设置的情况下）；
2. 可选值有：'none' | 'development' | 'production'；

|    选项     |                             描述                             |
| :---------: | :----------------------------------------------------------: |
| development | 会将 `DefinePlugin` 中 `process.env.NODE_ENV` 的值设置为 `development`. 为模块和 chunk 启用有效的名。 |
| production  | 会将 `DefinePlugin` 中 `process.env.NODE_ENV` 的值设置为 `production`。为模块和 chunk 启用确定性的混淆名称，`FlagDependencyUsagePlugin`，`FlagIncludedChunksPlugin`，`ModuleConcatenationPlugin`，`NoEmitOnErrorsPlugin` 和 `TerserPlugin` 。 |
|    none     |                    不使用任何默认优化选项                    |

**如果没有设置，webpack 会给 `mode` 的默认值设置为 `production`。**

如果 `mode` 未通过配置或 CLI 赋值，CLI 将使用可能有效的 `NODE_ENV` 值作为 `mode`。当然这是后话了。我们学习CLI的时候再来说。

![image-20210728221004362](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/ef0d0815fd247c928b6bece95dd42a76.png)

#### Mode配置代表更多

**只要设置了mode属性，webpack就会默认帮我们设置很多属性。不需要我们手动再去配置。**

![image-20210728221656536](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/893cde507ad786dcfa1c288c86db3269.png)

![image-20210728221716634](https://img-blog.csdnimg.cn/img_convert/1def9736638004439a50e3c29c8a5945.png)

### devtool配置

**这里顺手补充一波devtool**。

**devtool**也是和**mode**同级别的配置。默认值是eval。*设置开发时的工具*

此选项控制是否生成，以及如何生成 **source map**。

##### eval

在默认值的情况下：*默认会使用 eval函数对源代码进行包裹*

```js
// devtool 属性 ：设置开发时的工具 默认值是 eval
// 默认会使用 eval函数对源代码进行包裹
devtool: 'eval'
```

![](https://img-blog.csdnimg.cn/img_convert/b91bbb5fa4670f7daf63350370053eab.png)

![image-20210728223158431](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/907c917714faaa6aa72da3e015b4d0df.png)

**发现我们的打包后的代码全都被eval函数包裹**

##### source-map

**把属性设置为source-map**。出现错误就可以定位源码。

```js
// 如果想查看源码：可以设置值为 source-map
// 设置为这个属性 会生成对应的打包后的代码和源码映射的文件
// 出现错误可以映射到我们书写的源代码
devtool: 'source-map'
```

![image-20210728223248505](https://img-blog.csdnimg.cn/img_convert/1d769eb561d47a4aabec725d06d88843.png)

![image-20210728223306610](https://img-blog.csdnimg.cn/img_convert/0db0fb05e8f4b8fac5ceeef3e1263614.png)



##### 两种方式出现错误后的对比

###### eval配置代码出现错误时

![image-20210728223827751](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/dc617acc89ab9ba93c6e00da43f4250c.png)

![image-20210728223842559](https://img-blog.csdnimg.cn/img_convert/99aafd31e58c8bff6e4d8a0a329c8e15.png)

**发现代码并不是我们自己写的源码。不好辨别具体是那个文件出现错误。**



###### source-map配置代码出现错误时

![image-20210728223957836](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/2f551c450b320b84e4d528f255e996e0.png)

![image-20210728224005593](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/8dd566a10fafacecfc174419cc8c7e05.png)

**出现错误，可以直接定位到具体是哪一个源文件。很nice。**

##### 附上devtool的各种配置

| devtool                                    | performance                              | production | quality        | comment                                                      |
| :----------------------------------------- | :--------------------------------------- | :--------- | :------------- | :----------------------------------------------------------- |
| (none)                                     | **build**: fastest  **rebuild**: fastest | yes        | bundle         | Recommended choice for production builds with maximum performance. |
| **`eval`**                                 | **build**: fast  **rebuild**: fastest    | no         | generated      | Recommended choice for development builds with maximum performance. |
| `eval-cheap-source-map`                    | **build**: ok  **rebuild**: fast         | no         | transformed    | Tradeoff choice for development builds.                      |
| `eval-cheap-module-source-map`             | **build**: slow  **rebuild**: fast       | no         | original lines | Tradeoff choice for development builds.                      |
| **`eval-source-map`**                      | **build**: slowest  **rebuild**: ok      | no         | original       | Recommended choice for development builds with high quality SourceMaps. |
| `cheap-source-map`                         | **build**: ok  **rebuild**: slow         | no         | transformed    |                                                              |
| `cheap-module-source-map`                  | **build**: slow  **rebuild**: slow       | no         | original lines |                                                              |
| **`source-map`**                           | **build**: slowest  **rebuild**: slowest | yes        | original       | Recommended choice for production builds with high quality SourceMaps. |
| `inline-cheap-source-map`                  | **build**: ok  **rebuild**: slow         | no         | transformed    |                                                              |
| `inline-cheap-module-source-map`           | **build**: slow  **rebuild**: slow       | no         | original lines |                                                              |
| `inline-source-map`                        | **build**: slowest  **rebuild**: slowest | no         | original       | Possible choice when publishing a single file                |
| `eval-nosources-cheap-source-map`          | **build**: ok  **rebuild**: fast         | no         | transformed    | source code not included                                     |
| `eval-nosources-cheap-module-source-map`   | **build**: slow  **rebuild**: fast       | no         | original lines | source code not included                                     |
| `eval-nosources-source-map`                | **build**: slowest  **rebuild**: ok      | no         | original       | source code not included                                     |
| `inline-nosources-cheap-source-map`        | **build**: ok  **rebuild**: slow         | no         | transformed    | source code not included                                     |
| `inline-nosources-cheap-module-source-map` | **build**: slow  **rebuild**: slow       | no         | original lines | source code not included                                     |
| `inline-nosources-source-map`              | **build**: slowest  **rebuild**: slowest | no         | original       | source code not included                                     |
| `nosources-cheap-source-map`               | **build**: ok  **rebuild**: slow         | no         | transformed    | source code not included                                     |
| `nosources-cheap-module-source-map`        | **build**: slow  **rebuild**: slow       | no         | original lines | source code not included                                     |
| `nosources-source-map`                     | **build**: slowest  **rebuild**: slowest | yes        | original       | source code not included                                     |
| `hidden-nosources-cheap-source-map`        | **build**: ok  **rebuild**: slow         | no         | transformed    | no reference, source code not included                       |
| `hidden-nosources-cheap-module-source-map` | **build**: slow  **rebuild**: slow       | no         | original lines | no reference, source code not included                       |
| `hidden-nosources-source-map`              | **build**: slowest  **rebuild**: slowest | yes        | original       | no reference, source code not included                       |
| `hidden-cheap-source-map`                  | **build**: ok  **rebuild**: slow         | no         | transformed    | no reference                                                 |
| `hidden-cheap-module-source-map`           | **build**: slow  **rebuild**: slow       | no         | original lines | no reference                                                 |
| `hidden-source-map`                        | **build**: slowest  **rebuild**: slowest | yes        | original       | no reference. Possible choice when using SourceMap only for error reporting purposes. |

### 如何使用source-map

1. 根据源文件，生成 source-map文件，webpack在打包时，可以通过配置生成 source-map文件。
2. 在转换后的代码中，最后一行添加注释，指向source-map文件

```js
//# sourceMappingURL=bundle.js.map
```

浏览器会根据我们的注释，查找响应的source-map，并根据source-map还原我们的源代码，便于快速调试和定位问题所在位置。

一般浏览器都会自动开启过支持source-map的选项。



### devtool各种值的区别(支持26个不同值)

#### false

配置devtool的值为 **`false`**即可。构建速度最快，不会生成 source-map。也不会使用eval函数，也就是直接执行代码片段，都打包到bundle.js一个文件里面，一旦出现错误，难以调试。



#### none

在构建速度上最快。不会生成 source-map，但只能在mode模式为生产模式才能设置。是**生产模式**的默认值（没有这个配置项），不需要显示的写出来。（报错）none值的意思就是没有这个devtool配置项，不需要手动配置，不写这个配置项即可。





#### eval

在构建速度上仅仅次于none，也非常快。是**开发模式**的默认值。不生成 source-map文件。

开发模式默认值使用eval函数，将一些代码片段都转为字符串进行执行。因为在使用eval函数执行字符串代码片段的时候，可以在最后加上source-map的注释，指向源代码的位置。方便出现错误进行调试。

```js
eval("module.exports = {\r\n  formatTime(str) {\r\n    return str + \"格式化时间！\"\r\n  },\r\n  formatPrice(num) {\r\n    return parseInt(num);\r\n  }\r\n}\n\n//# sourceURL=webpack://webpack-02-plugin/./src/cmd/format.js?");

```

不需要看打包后的结果的情况下，使用eval是合适的。





#### source-map

该值会生成source-map的文件。浏览器可借助此map文件还原源代码，方便定位。是生成最完整的sourcemap





#### eval-source-map

也会生成 source-map文件，但是map文件是以DataURl的形式添加到eval函数值的后面的。

不会生成独立的source-map文件了，每个执行的代码片段后面都有一个指向source-map的注释，后面跟着的就是采用base64编码后的源source-map代码。出现错误也一样可以快速定位，不需要多发一次请求获取source-map文件了。



```js
eval("module.exports = {\r\n  formatTime(str) {\r\n    return str + \"格式化时间！\"\r\n  },\r\n  formatPrice(num) {\r\n    return parseInt(num);\r\n  }\r\n}//# sourceURL=[module]\n//# sourceMappingURL=data:application/json;charset=utf-8;base64,eyJ2ZXJzaW9uIjozLCJmaWxlIjoiLi9zcmMvY21kL2Zvcm1hdC5qcy5qcyIsIm1hcHBpbmdzIjoiQUFBQTtBQUNBO0FBQ0E7QUFDQSxHQUFHO0FBQ0g7QUFDQTtBQUNBO0FBQ0EiLCJzb3VyY2VzIjpbIndlYnBhY2s6Ly93ZWJwYWNrLTAyLXBsdWdpbi8uL3NyYy9jbWQvZm9ybWF0LmpzPzE5YTAiXSwic291cmNlc0NvbnRlbnQiOlsibW9kdWxlLmV4cG9ydHMgPSB7XHJcbiAgZm9ybWF0VGltZShzdHIpIHtcclxuICAgIHJldHVybiBzdHIgKyBcIuagvOW8j+WMluaXtumXtO+8gVwiXHJcbiAgfSxcclxuICBmb3JtYXRQcmljZShudW0pIHtcclxuICAgIHJldHVybiBwYXJzZUludChudW0pO1xyXG4gIH1cclxufSJdLCJuYW1lcyI6W10sInNvdXJjZVJvb3QiOiIifQ==\n//# sourceURL=webpack-internal:///./src/cmd/format.js\n");

```



#### inline-source-map

和eval-source-map基本一致，区别就是，这里生成的source-map是在打包后文件的最后一行显示，所有的source-map一起进行base64压缩。

```js
//# sourceMappingURL=data:application/json;charset=utf-8;base64,eyJ2ZXJzaW9uIjozLCJmaWxlIjoiYnVuZGxlLmpzIiwibWFwcGluZ3MiOiI7Ozs7Ozs7OztBQUFBO0FBQ0E7QUFDQTtBQUNBLEdBQUc7QUFDSDtBQUNBO0FBQ0E7QUFDQTs7Ozs7Ozs7Ozs7Ozs7OztBQ1BBO0FBQ0E7QUFDQTtBQUNBO0FBQ0E7QUFDQTtBQUNBO0FBQ2tDOzs7Ozs7O1VDUGxDO1VBQ0E7O1VBRUE7VUFDQTtVQUNBO1VBQ0E7VUFDQTtVQUNBO1VBQ0E7VUFDQTtVQUNBO1VBQ0E7VUFDQTtVQUNBO1VBQ0E7O1VBRUE7VUFDQTs7VUFFQTtVQUNBO1VBQ0E7Ozs7O1dDdEJBO1dBQ0E7V0FDQTtXQUNBO1dBQ0E7V0FDQSxpQ0FBaUMsV0FBVztXQUM1QztXQUNBOzs7OztXQ1BBO1dBQ0E7V0FDQTtXQUNBO1dBQ0EseUNBQXlDLHdDQUF3QztXQUNqRjtXQUNBO1dBQ0E7Ozs7O1dDUEE7Ozs7O1dDQUE7V0FDQTtXQUNBO1dBQ0EsdURBQXVELGlCQUFpQjtXQUN4RTtXQUNBLGdEQUFnRCxhQUFhO1dBQzdEOzs7Ozs7Ozs7Ozs7OztBQ05BLFlBQVksMEJBQTBCO0FBQ3RDLGVBQWUsbUJBQU8sQ0FBQyx5Q0FBYztBQUNEO0FBQ3BDO0FBQ0E7QUFDQSxZQUFZLDhEQUFxQjtBQUNqQyxZQUFZLDZEQUFvQixhIiwic291cmNlcyI6WyJ3ZWJwYWNrOi8vd2VicGFjay0wMi1wbHVnaW4vLi9zcmMvY21kL2Zvcm1hdC5qcyIsIndlYnBhY2s6Ly93ZWJwYWNrLTAyLXBsdWdpbi8uL3NyYy9lc20vZm9ybWF0LmpzIiwid2VicGFjazovL3dlYnBhY2stMDItcGx1Z2luL3dlYnBhY2svYm9vdHN0cmFwIiwid2VicGFjazovL3dlYnBhY2stMDItcGx1Z2luL3dlYnBhY2svcnVudGltZS9jb21wYXQgZ2V0IGRlZmF1bHQgZXhwb3J0Iiwid2VicGFjazovL3dlYnBhY2stMDItcGx1Z2luL3dlYnBhY2svcnVudGltZS9kZWZpbmUgcHJvcGVydHkgZ2V0dGVycyIsIndlYnBhY2s6Ly93ZWJwYWNrLTAyLXBsdWdpbi93ZWJwYWNrL3J1bnRpbWUvaGFzT3duUHJvcGVydHkgc2hvcnRoYW5kIiwid2VicGFjazovL3dlYnBhY2stMDItcGx1Z2luL3dlYnBhY2svcnVudGltZS9tYWtlIG5hbWVzcGFjZSBvYmplY3QiLCJ3ZWJwYWNrOi8vd2VicGFjay0wMi1wbHVnaW4vLi9zcmMvaW5kZXguanMiXSwic291cmNlc0NvbnRlbnQiOlsibW9kdWxlLmV4cG9ydHMgPSB7XHJcbiAgZm9ybWF0VGltZShzdHIpIHtcclxuICAgIHJldHVybiBzdHIgKyBcIuagvOW8j+WMluaXtumXtO+8gVwiXHJcbiAgfSxcclxuICBmb3JtYXRQcmljZShudW0pIHtcclxuICAgIHJldHVybiBwYXJzZUludChudW0pO1xyXG4gIH1cclxufSIsImZ1bmN0aW9uIGZvcm1hdFRpbWUoc3RyKSB7XHJcbiAgcmV0dXJuIHN0ciArIFwi5qC85byP5YyW5pe26Ze077yBXCJcclxufVxyXG5mdW5jdGlvbiBmb3JtYXRQcmljZShudW0pIHtcclxuICByZXR1cm4gcGFyc2VJbnQobnVtKTtcclxufVxyXG5cclxuZXhwb3J0IHsgZm9ybWF0VGltZSwgZm9ybWF0UHJpY2UgfVxyXG4iLCIvLyBUaGUgbW9kdWxlIGNhY2hlXG52YXIgX193ZWJwYWNrX21vZHVsZV9jYWNoZV9fID0ge307XG5cbi8vIFRoZSByZXF1aXJlIGZ1bmN0aW9uXG5mdW5jdGlvbiBfX3dlYnBhY2tfcmVxdWlyZV9fKG1vZHVsZUlkKSB7XG5cdC8vIENoZWNrIGlmIG1vZHVsZSBpcyBpbiBjYWNoZVxuXHR2YXIgY2FjaGVkTW9kdWxlID0gX193ZWJwYWNrX21vZHVsZV9jYWNoZV9fW21vZHVsZUlkXTtcblx0aWYgKGNhY2hlZE1vZHVsZSAhPT0gdW5kZWZpbmVkKSB7XG5cdFx0cmV0dXJuIGNhY2hlZE1vZHVsZS5leHBvcnRzO1xuXHR9XG5cdC8vIENyZWF0ZSBhIG5ldyBtb2R1bGUgKGFuZCBwdXQgaXQgaW50byB0aGUgY2FjaGUpXG5cdHZhciBtb2R1bGUgPSBfX3dlYnBhY2tfbW9kdWxlX2NhY2hlX19bbW9kdWxlSWRdID0ge1xuXHRcdC8vIG5vIG1vZHVsZS5pZCBuZWVkZWRcblx0XHQvLyBubyBtb2R1bGUubG9hZGVkIG5lZWRlZFxuXHRcdGV4cG9ydHM6IHt9XG5cdH07XG5cblx0Ly8gRXhlY3V0ZSB0aGUgbW9kdWxlIGZ1bmN0aW9uXG5cdF9fd2VicGFja19tb2R1bGVzX19bbW9kdWxlSWRdKG1vZHVsZSwgbW9kdWxlLmV4cG9ydHMsIF9fd2VicGFja19yZXF1aXJlX18pO1xuXG5cdC8vIFJldHVybiB0aGUgZXhwb3J0cyBvZiB0aGUgbW9kdWxlXG5cdHJldHVybiBtb2R1bGUuZXhwb3J0cztcbn1cblxuIiwiLy8gZ2V0RGVmYXVsdEV4cG9ydCBmdW5jdGlvbiBmb3IgY29tcGF0aWJpbGl0eSB3aXRoIG5vbi1oYXJtb255IG1vZHVsZXNcbl9fd2VicGFja19yZXF1aXJlX18ubiA9IChtb2R1bGUpID0+IHtcblx0dmFyIGdldHRlciA9IG1vZHVsZSAmJiBtb2R1bGUuX19lc01vZHVsZSA/XG5cdFx0KCkgPT4gKG1vZHVsZVsnZGVmYXVsdCddKSA6XG5cdFx0KCkgPT4gKG1vZHVsZSk7XG5cdF9fd2VicGFja19yZXF1aXJlX18uZChnZXR0ZXIsIHsgYTogZ2V0dGVyIH0pO1xuXHRyZXR1cm4gZ2V0dGVyO1xufTsiLCIvLyBkZWZpbmUgZ2V0dGVyIGZ1bmN0aW9ucyBmb3IgaGFybW9ueSBleHBvcnRzXG5fX3dlYnBhY2tfcmVxdWlyZV9fLmQgPSAoZXhwb3J0cywgZGVmaW5pdGlvbikgPT4ge1xuXHRmb3IodmFyIGtleSBpbiBkZWZpbml0aW9uKSB7XG5cdFx0aWYoX193ZWJwYWNrX3JlcXVpcmVfXy5vKGRlZmluaXRpb24sIGtleSkgJiYgIV9fd2VicGFja19yZXF1aXJlX18ubyhleHBvcnRzLCBrZXkpKSB7XG5cdFx0XHRPYmplY3QuZGVmaW5lUHJvcGVydHkoZXhwb3J0cywga2V5LCB7IGVudW1lcmFibGU6IHRydWUsIGdldDogZGVmaW5pdGlvbltrZXldIH0pO1xuXHRcdH1cblx0fVxufTsiLCJfX3dlYnBhY2tfcmVxdWlyZV9fLm8gPSAob2JqLCBwcm9wKSA9PiAoT2JqZWN0LnByb3RvdHlwZS5oYXNPd25Qcm9wZXJ0eS5jYWxsKG9iaiwgcHJvcCkpIiwiLy8gZGVmaW5lIF9fZXNNb2R1bGUgb24gZXhwb3J0c1xuX193ZWJwYWNrX3JlcXVpcmVfXy5yID0gKGV4cG9ydHMpID0+IHtcblx0aWYodHlwZW9mIFN5bWJvbCAhPT0gJ3VuZGVmaW5lZCcgJiYgU3ltYm9sLnRvU3RyaW5nVGFnKSB7XG5cdFx0T2JqZWN0LmRlZmluZVByb3BlcnR5KGV4cG9ydHMsIFN5bWJvbC50b1N0cmluZ1RhZywgeyB2YWx1ZTogJ01vZHVsZScgfSk7XG5cdH1cblx0T2JqZWN0LmRlZmluZVByb3BlcnR5KGV4cG9ydHMsICdfX2VzTW9kdWxlJywgeyB2YWx1ZTogdHJ1ZSB9KTtcbn07IiwiLy8gaW1wb3J0IHsgZm9ybWF0VGltZSwgZm9ybWF0UHJpY2UgfSBmcm9tIFwiLi9lc20vZm9ybWF0XCJcclxuY29uc3QgZm9ybWF0ID0gcmVxdWlyZShcIi4vZXNtL2Zvcm1hdFwiKTtcclxuaW1wb3J0IGNtZEZvcm1hdCBmcm9tICcuL2NtZC9mb3JtYXQnXHJcbmNvbnNvbGUubG9nKGZvcm1hdC5mb3JtYXRQcmljZSgxMDAuMSkpO1xyXG5jb25zb2xlLmxvZyhmb3JtYXQuZm9ybWF0VGltZShcIjEyOjAwOjAwXCIpKVxyXG5jb25zb2xlLmxvZyhjbWRGb3JtYXQuZm9ybWF0UHJpY2UoMTAwLjEpKTtcclxuY29uc29sZS5sb2coY21kRm9ybWF0LmZvcm1hdFRpbWUoXCIxMjowMDowMFwiKSkiXSwibmFtZXMiOltdLCJzb3VyY2VSb290IjoiIn0=
```



#### cheap-source-map

低开销的source-map，性能比source-map更高一点。出现错误可以定位到错误所在文件的行，不能定位到列。



#### cheap-module-source-map

类似于cheap-source-map，但是对于**源自loader的source-map处理会更好**。

主要是源于babel等loader对代码进行处理后，会出现报错代码和源代码位置对不上等情况，此时使用这个值能够准确的帮我们定位。



#### hidden-source-map

也会生成source-map文件，但是打包后的文件没有和map文件关联，出现错误是无法定位到源文件的。

当然想要定位到原文件错误的地方，只需要加上那个特殊注释指向该map文件即可。





#### nosources-source-map

会生成 source-map文件，但是生成的map文件只有错误信息的提示，不会生成源代码文件。



#### 多个值的组合

实际上，webpack提供了26个值，是组合产生的。

组合规则：

1. inline- | hidden- | eval-: 三个值选一
2. nosources：可选值
3. cheap：可选值 ，且后面可以在跟上module
4. \[inline-|hidden-|eval-]\[nosources-][cheap-[module-]\]source-map



#### 最佳实践

开发阶段：推荐使用 `source-map`或者 `cheap-module-source-map`。这是vue和react脚手架锁使用的值，可以获取调试信息，方便快速开发。

 测试阶段：也推荐使用开发阶段的值，方便调试，以及方便获取错误提示等。

生产(发布/上线)阶段：false，或者不配该选项。











### 本次使用过的配置代码展示

#### plugin以前的配置

```js
const path = require('path');

module.exports = {
  entry: path.join(__dirname, "./src/index.js"),
  output: {
    path: path.join(__dirname, "./build"),
    filename: 'bundle.js',
    // 使用asset打包的图片不转为base64格式的时候，图片存放的位置以及名称
    // assetModuleFilename: 'image/[name]-[hash:6][ext]'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          'css-loader',
          'postcss-loader'
        ]
      },
      {
        test: /\.less$/,
        use: [
          'style-loader',
          'css-loader',
          'postcss-loader',
          'less-loader'
        ]

      },
      // 配置打包图片资源的规则
      // {
      //   test: /\.(jpg|png|jpeg|JPG)$/,
      //   use: 'file-loader'
      // }
      /* {
        test: /\.(jpg|png|jpeg|JPG)$/,
        use: {
          loader: 'file-loader',
          // file-loader的参数配置
          options: {
            // 打包后图片文件所在的路径
            // outputPath: 'image',
            // 打包后生成文件的名称
            // [name] 源文件的名称（不含拓展名）
            // 我们发现不写配置时，文件的名称过长（且文件名是通过hash算法算出来的，为了防止重名）
            // [hash:6] 默认生成文件名称的hash算法，32位，取前六位
            // [ext] 使用原来文件的拓展名
            // name: '[name]_[hash:6].[ext]'

            // 当然：我们也可以把图片存放的路径和图片名称合在一起
            name: 'image/[name]-[hash:6].[ext]'
          }
        }
      } , */

      // 注意： 使用file-loader就不要使用url-loader
      // 使用url-loader也不需要使用file-loader
      // 配置url-loader 设置图片大小的限制，小于某个范围的图片转为base64格式
      // {
      //   test: /\.(jpe?g|JPG|png)$/,
      //   use: {
      //     loader: 'url-loader',
      //     options: {
      //       name: 'image/[name]-[hash:6].[ext]',
      //       // 设置图片大小的限制，只有小于多少kb的图片我们才会转为base64格式
      //       // 如果大于这个限制 我们就不会转为base64格式。而是继续采用上面的path和name的形式打包，还是保持图片的形式不变
      //       limit: 100 * 1024 // 小于100kb的文件才会转为base64格式
      //     }
      //   }
      // }


      // webpack5开始 可以不在使用file-loader和url-loader来进行打包图片了
      // 我们直接使用 asset module type 资源模块类型 来替换上面loader
      // 注意：asset是webpack5自带的
      {
        test: /\.(JPG|jpe?g|png)$/,
        // 这里不在使用use属性 而是type属性
        // 使用asset/resource 来代替 file-loader
        // type:'asset/resource'

        // 比较常用的我们一般直接写成 asset
        type: 'asset',
        // 配置相关的asset参数 使用parser属性
        parser: {
          // 数据url条件
          dataUrlCondition: {
            // 最大不超过多少kb的图片 我们转为 base64格式
            maxSize: 100 * 1024
          }
        },
        // 下面的配置也可以放在 output里面（了解）
        // generator属性 生成 也就是打包后的图片存放时相关的配置
        generator: {
          // 配置图片名称和图片位置 
          // 这里的 [ext] 拿到的文件拓展名包含 .
          filename: 'image/[name]-[hash:6][ext]'
        }
      },
      // 配置字体和字体图标等文件打包时的规则
      // {
      //   test: /\.(eot|ttf|woff2?)$/,
      //   use:{
      //     loader: 'file-loader',
      //     options:{
      //       // 这里是name属性 不是filename
      //       name: 'font/[name]-[hash:6].[ext]'
      //     }
      //   }
      // },

      // 也可以使用webpack提供的资源模块类型
      {
        test: /\.(eot|ttf|woff2?)$/,
        type: 'asset/resource',
        generator: {
          // 这里使用的是filename属性 别忘了！！！！！
          filename: 'font/[name]-[hash:6][ext]'
        }
      }
    ]
  }
}
```

#### plugin开始及以后的配置

```js
const path = require('path');

// 注意：所有的插件都需要导入的，loader不需要
// 导入 clean-webpack-plugin 插件
// 该插件导出的是一个对象，我们需要从对象里面按需获取 CleanWebpackPlugin 这个类
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

// 导入 html-webpack-plugin插件 这个插件导出的就是一个类 不需要解构
const HtmlWebpackPlugin = require('html-webpack-plugin')

// 导入 DefinePlugin 插件 解决常量 BASE_URL 的问题
const { DefinePlugin } = require('webpack');

// 导入 CopyWebpackPlugin 插件 该插件导出的也是一个类
// 用来完成文件的复制工作
const CopyWebpackPlugin = require('copy-webpack-plugin');

module.exports = {

  // 模式配置 mode
  // development 生产模式 打包的代码不会压缩 易于阅读
  mode:"development",
  // 开发模式 代码会被压缩 减小体积
  // mode:"production",

  // devtool 属性 ：设置开发时的工具 默认值是 eval
  // 默认会使用 eval函数对源代码进行包裹
  // devtool: 'eval',
  // 如果想查看源码：可以设置值为 source-map
  // 设置为这个属性 会生成对应的打包后的代码和源码映射的文件
  // 出现错误可以映射到我们书写的源代码
  devtool: 'source-map',

  // 提供 plugins属性来配置插件
  // plugins属性是数组，数组元素是一个个的插件对象
  // 插件是书写没有先后顺序！！！！！！！！！！
  plugins: [
    // 使用 CleanWebpackPlugin 插件对象 
    // 因为 CleanWebpackPlugin 是一个class类，我们需要 new 实例化成对象
    // CleanWebpackPlugin 插件会删除我们生成的打包后的那么文件夹，然后重新打包
    new CleanWebpackPlugin(),
    // 使用 HtmlWebpackPlugin 插件
    // new HtmlWebpackPlugin(),

    // 使用 HtmlWebpackPlugin 插件 并指定html的模板
    new HtmlWebpackPlugin({
      // 按照这个html文件的模板来进行打包，生成的index.html和这个模板一样
      template: './public/index.html',
      title: 'webpack-plugin 的学习'
    }),
    // 使用 DefinePlugin 插件来定义 常量
    new DefinePlugin({
      // BASE_URL 会根据后面给的属性值 去本文件上下文中找这个变量
      // 比如我这里给的是字符串 "filepath"
      // 也会去上下文中找叫做 filepath 的变量
      // 如果我们想直接给一个字符串的值
      // 我们需要在双引号里面嵌套单引号赋值
      // 当然 单引号里面嵌套双引号赋值也是可以的
      // BASE_URL: "filepath",
      BASE_URL: "'./'"
    }),

    // 使用 CopyWebpackPlugin 插件
    new CopyWebpackPlugin({
      // patterns属性： 匹配规则 数组
      patterns: [
        {
          // 复制的来源
          from: 'public',
          // 复制后放到哪里
          // 不写，或者给空字符串 最后都是把拷贝的文件放在打包后的根目录下
          to: '',
          // 全局配置  globOptions
          globOptions: {
            // 忽略那些文件
            // ignore: ['**/index.html'],
            // 只有一个忽略的规则时 可以直接写一个字符串
            ignore: '**/index.html'
          }
        }
      ]
    })
  ],
  entry: path.join(__dirname, "./src/index.js"),
  output: {
    path: path.join(__dirname, "./build"),
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          'css-loader',
          'postcss-loader'
        ]
      },
      {
        test: /\.less$/,
        use: [
          'style-loader',
          'css-loader',
          'postcss-loader',
          'less-loader'
        ]
      },
      // webpack5开始 可以不在使用file-loader和url-loader来进行打包图片了
      // 我们直接使用 asset module type 资源模块类型 来替换上面loader
      // 注意：asset是webpack5自带的
      {
        test: /\.(JPG|jpe?g|png)$/,
        // 这里不在使用use属性 而是type属性
        // 使用asset/resource 来代替 file-loader
        // type:'asset/resource'

        // 比较常用的我们一般直接写成 asset
        type: 'asset',
        // 配置相关的asset参数 使用parser属性
        parser: {
          // 数据url条件
          dataUrlCondition: {
            // 最大不超过多少kb的图片 我们转为 base64格式
            maxSize: 100 * 1024
          }
        },
        // 下面的配置也可以放在 output里面（了解）
        // generator属性 生成 也就是打包后的图片存放时相关的配置
        generator: {
          // 配置图片名称和图片位置 
          // 这里的 [ext] 拿到的文件拓展名包含 .
          filename: 'image/[name]-[hash:6][ext]'
        }
      },

      // 也可以使用webpack提供的资源模块类型
      {
        test: /\.(eot|ttf|woff2?)$/,
        type: 'asset/resource',
        generator: {
          // 这里使用的是filename属性 别忘了！！！！！
          filename: 'font/[name]-[hash:6][ext]'
        }
      }
    ]
  }
}
```



## Babel深入解析

**Babel基础下面**有所记录。（先看下面的21年笔记）

**Babel**是一个工具链，核心是 `@babel/core`,也就是常说的 **微内核架构**

babel可用于：语法转换，源码转换，polyfill实现目标环境缺少的功能等。



### polyfill

#### polyfill是什么

直译就是一种用于衣物，床上用品等的聚酯填充材料，使物品更加舒适。

在JS中：可以理解为一个补丁，可以帮助我们更好的使用JavaScript。

**为什么我们需要polyfill**

在开发时：我们会使用ES的新特性，而新特性一般浏览器不会及时的就支持。（Promise，async，Generator等），浏览器不支持这些语法的时候就会报错，这时候我们就需要使用polyfill来填充或者说是打补丁，让其支持该特性。



### 如何使用polyfill

在babel7.4之前，可以使用 `@babel/polyfill`包来打补丁，但是在后续版本已经不推荐使用了。

所以我们会选择单独引入 `core-js`和`regenerator-runtime`来完成polyfill的使用：

```shell
npm i core-js regenerator-runtime --save # 生产时也会使用
```

安装好这两个polyfill的包之后，只需要在预设的地方进行配置即可，babel会根据我们的browserslist设置的目标浏览器进行打补丁。

```js
module.exports = {
  presets: [
    [
      "@babel/preset-env",
      {
        // false 表示不使用 polyfill 即使安装过也不使用
        // usage 表示按需引入需要使用的polyfill
        // entry 表示只要是目标浏览器需要的，全都引入，不管在项目中是否使用
        useBuiltIns: "usage"
      }
    ]
  ],
}

```



#### useBuiltIns的注意事项

如果我们使用了值 `usage`和`entry`，直接使用的时候，是有可能报错，打包失败。

因为可能我们项目中的第三方的包，已经进行了polyfill，这时候我们这里的polyfill再次打包进去，就可能报错了。

所以我们需要配置babel-loader在进行处理的时候，不处理第三方包的文件，

```js
// 加载js文件
{
    test: /\.js$/,
        exclude: /node_modules/,
            use: [
                {
                    // 配置单独抽离到配置文件中了
                    loader: "babel-loader",
                }
            ]
}
```







![image-20220325180727088](https://img-blog.csdnimg.cn/img_convert/0eee8728912ef445f8730aa47aedcbc3.png)

 

然后还是像上面截图报错，是因为babel-preset-env预设，默认使用的是core-js的2.x版本，此时可能产生冲突等报错。所以需要指定corejs的版本为3.

**useBuiltIns：**进行polyfill的方式，且polyfill的特性都是放到全局中。

```js
module.exports = {
  presets: [
    [
      "@babel/preset-env",
      {
        // false 表示不使用 polyfill 即使安装过也不使用
        // usage 表示按需引入需要使用的polyfill
        // entry 表示只要是目标浏览器需要的，全都引入，不管在项目中是否使用
        useBuiltIns: "usage",
        // 指定 corejs的版本 默认不指定的情况下使用的是2版本
        corejs: 3
      }
    ]
  ],
}

```



##### 设置值为entry

设置预设polyfill为entry，默认情况下是不会生效的。我们需要在入口处引入 **“core-js/stable”**

也就是已经成为标准的那些js特性，还需要引入 **regenerator-runtime/runtime**. babel会把目标浏览器需要的特性，而且暂时不支持的那些，进行polyfill，并不是说把引入的文件全都打包进去。

```js
import "core-js/stable"
import "regenerator-runtime/runtime"
```



### 插件 plugin-transform-runtime(了解)

在前面使用的pollfill，默认情况下都是添加特性到全局中：

1. 如果我们是编写工具库，这个工具库需要使用polyfill
2. 别人在使用我们工具时，工具库通过polyfill添加的特性，可能会污染他人的代码
3. 所以在编写工具库时，babel推荐我们使用该插件来完成我们当前库中需要的polyfill功能

为了在进行polyfill的时候，不污染全局的ES代码，所以在编写第三方包的时候，都推荐使用该插件，如上面所说：useBuiltIns进行polyfill是把特性放到全局中。

所以 `useBuiltIns`和`此插件`二者是相互独立的。二者在使用的时候，只选其一。



在使用该插件的时候，由于我们使用的是corejs3版本，所以还需要安装一下一个库

```shell
npm i @babel/runtime-corejs3
```

否则会报错，如果使用的corejs2版本，那么安装该插件的2版本即可。







### React的jsx支持

编写react代码的时候，使用的是jsx。jsx是可以直接用babel进行转换的。

需要依赖如下几个插件：

```shell
npm i @babel/plugin-syntax-jsx -D
npm i @babel/plugin-transform-react-jsx -D
npm i @babel/plugin-transform-display-name -D
```

一个个安装比较麻烦：所以babel提供的有一个react的预设

安装预设： `@babel/preset-react`。且需要在webpack.config.js配置文件中，配置我们处理文件的后缀名为js或者jsx。

```js
module.exports = {
  presets: [
    [
      "@babel/preset-env",
      {
        useBuiltIns: "usage",
        corejs: 3
      }
    ],
    [
      // 支持 react-jsx
      "@babel/preset-react"
    ]
  ],
}

```



### Typescript的编译

#### 使用ts-loader

对于ts文件，需要使用 `ts-loader`来进行处理。因为需要处理所有的ts文件，所以需要一个ts的配置文件,使用命令 `tsc --init`生成tsconfig.json配置文件。

该loader本质上还依赖于ts提供的编译器，也就是 `typescript compiler`，也是我们平时用的tsc命令。

但是该loader不会对目标浏览器所不具备的es特性进行polyfill。

```js
// ts
{
    test: /\.ts$/,
        use: [
            
            { loader: "ts-loader" }
        ]
}
```



#### babel-loader使用babel的预设处理ts

```shell
npm install --save-dev @babel/preset-typescript
```

在babel的配置文件中配置一下ts的预设：

```js
module.exports = {
  presets: [
    [
      "@babel/preset-env",
      {
        useBuiltIns: "usage",
        corejs: 3
      }
    ],
    [
      // 支持 react-jsx
      "@babel/preset-react"
    ],
    [
      // 支持 ts
      "@babel/preset-typescript"
    ]
  ],
}

```

使用babel的预设，直接处理ts，是不需要安装上面的ts-loader和typescript编译器的。



#### ts-loader和babel-loader的选择

babel-loader的优点：会进行polyfill。

babel-loader的缺点：不会对ts进行类型校验，类型错误打包也会显示成功。

而ts-loader和babel-loader的优缺点刚好相反，所以我们希望把二者优点集合起来。

**也就是说用babel来进行转化和polyfill，然后让tsc进行检查。**

也就是说可以在打包之前，先让`tsc`对代码进行一遍检测，然后使用babel进行打包。

写上一个脚本：

```json
"script": {
    "type-check": "tsc --noEmit", // 不生成编译后的js文件
    "build": "npm run type-check && webpack --config webpack.config.js",
    "type-check-watch":"tsc --noEmit --watch", // 实时监测
}
```



## ES-lint的使用

### 认识es-lint

ES-Lint：是一个静态代码分析工具。在没有任何程序执行的情况下，对代码进行分析是否合理。（是依赖js编译器的）。

**该工具可以帮助我们在项目中建立统一的代码规范，保证正确，统一的代码风格，提高代码可读和可维护性**

且es-lint的规则是可配置的，我们也可以自定义属于自己的规则。

#### 安装

```shell
npm install eslint --save-dev
```

#### 创建配置文件

```shell
npx eslint --init 
```

选择自己需要的配置信息。

**.eslintrc.js**

```js
module.exports = {
	"env": {
		"browser": true,
		"commonjs": true,
		"es2022": true
	},
	"extends": [
		"eslint:recommended",
		"plugin:@typescript-eslint/recommended"
	],
	"parser": "@typescript-eslint/parser",
	"parserOptions": {
		"ecmaVersion": "latest"
	},
	"plugins": [
		"@typescript-eslint"
	],
	"rules": {
		"indent": [
			"error",
			"space"
		],
		"linebreak-style": [
			"error",
			"windows"
		],
		"quotes": [
			"error",
			"double"
		],
		"semi": [
			"error",
			"always"
		]
	}
};

```



#### eslint-loader

使用该loader在运行代码之前进行校验，看是否符合要求。

```shell
npm i eslint-loader -D
```

把这个loader在babel-loader之前使用即可。























## Babel和Vue的sfc(21年学习vue所记录)

### Babel

#### 为什么需要babel？

事实上，在开发中我们很少**直接去接触babel**，但是babel对于前端开发来说，目前是不可缺少的一部分： 

1. 开发中，我们想要使用**ES6+**的语法，想要使用**TypeScrip**t，开发**React**项目，它们都是离不开Babel的； 
2. 所以，**学习Babel**对于我们理解代码从编写到线上的转变过程至关重要；

**那么，Babel到底是什么呢？**

Babel是一个**工具链**，主要用于旧浏览器或者环境中将ECMAScript 2015+代码转换为向后兼容版本的 JavaScript；

**包括：语法转换、源代码转换等；**

#### Babel命令行使用

babel本身可以作为一个**独立的工具**（和postcss一样），不和webpack等构建工具配置来单独使用。

如果我们希望在命令行尝试使用babel，需要安装如下库：

- @babel/core：babel的核心代码，必须安装；
- @babel/cli：可以让我们在命令行使用babel；

```shell
npm i @babel/core @babel/cli -D
```

**使用babel来处理我们的源代码：**

- src：是源文件的目录；也可以直接指定文件
- --out-dir：指定要输出的文件夹dist；

```shell
npx babel src --out-dir dict
```

#### 插件的使用

比如我们需要转换**箭头函数**，那么我们就可以使用箭头函数转换相关的插件：

```shell
npm install @babel/plugin-transform-arrow-functions -D

npx babel src --out-dir dist --plugins=@babel/plugin-transform-arrow-functions
```

![image-20210731084446775](https://img-blog.csdnimg.cn/img_convert/f4c0719e4e760ccb6a3c0e2a9126fd90.png)

**源代码：**

```js
const name = "毛毛";
const arr = [1,2,3];
const [a,b,c]= [...arr];
arr.forEach(item=>console.log(item));
```

**目标代码：**

```js
const name = "毛毛";
const arr = [1, 2, 3];
const [a, b, c] = [...arr];
arr.forEach(function (item) {
  return console.log(item);
});
```

![image-20210731084619271](https://img-blog.csdnimg.cn/img_convert/034473af6aefd9dee5093af0c98ab252.png)

查看转换后的结果：我们会发现 **const** 并没有转成 **var**

- 这是因为 plugin-transform-arrow-functions，并没有提供这样的功能；
- 我们需要使用 plugin-transform-block-scoping 来完成这样的功能；

```shell
npm install @babel/plugin-transform-block-scoping -D

npx babel src --out-dir dist --plugins=@babel/plugin-transform-block-scoping
,@babel/plugin-transform-arrow-functions

```

![image-20210731084842846](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/d3805dd09147e10b114d7c59da07f026.png)

![image-20210731084854742](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/575349dc839048033a742abd9852f2ad.png)

#### Babel的预设preset

但是如果要转换的内容过多，一个个设置是比较麻烦的，我们可以使用预设（preset）：**后面我们再具体来讲预设代表的含义；**

安装@babel/preset-env预设：

```shell
npm install @babel/preset-env -D
```

**执行如下命令：**

```shell
npx babel src --out-dir dist --presets=@babel/preset-env
```

![image-20210731085152844](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/c78d8a80f0220fdea04e2e83d973ea9c.png)

![image-20210731085201827](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/eb76b8c575d27d20350e0eef37146c55.png)



#### Babel的底层原理

babel是如何做到将我们的一段代码（**ES6、TypeScript、Reac**t）转成另外一段代码（ES5）的呢？

从一种源代码（原生语言）转换成另一种源代码（目标语言），这是什么的工作呢？

**就是编译器，事实上我们可以将babel看成就是一个编译器**；Babel编译器的作用就是将我们的源代码，转换成浏览器可以直接识别的另外一段源代码；

**Babel也拥有编译器的工作流程：**

1. 解析阶段（Parsing） -> AST 抽象语法树
2. 转换阶段（Transformation） -> 字节码
3. 生成阶段（Code Generation）

#### Babel编译器执行原理

##### Babel的执行阶段

![image-20210731085400687](https://img-blog.csdnimg.cn/img_convert/bab04065ac55d9823f9a2cc6ca366f24.png)

**当然，这只是一个简化版的编译器工具流程，在每个阶段又会有自己具体的工作：**

![image-20210731085426851](https://img-blog.csdnimg.cn/img_convert/b519c6dc3b6cef20312546db074c727d.png)

![image-20210731085436745](https://img-blog.csdnimg.cn/img_convert/1c233a0372c0cd2d7611a5b2cb3ea846.png)

#### babel-loader

在实际开发中，我们通常会在构建工具中通过配置babel来对其进行使用的，比如在webpack中

那么我们就需要去安装相关的依赖：如果之前已经安装了@babel/core，那么这里不需要再次安装；

```shell
npm install babel-loader @babel/core -D
```

我们可以设置一个规则，在加载js文件时，使用我们的babel：

![image-20210731094048189](https://img-blog.csdnimg.cn/img_convert/12c36c73b1c0d3ba6b85059f8f4861e6.png)

##### 指定使用的插件

我们必须指定使用的插件才会生效:

![image-20210731094141175](https://img-blog.csdnimg.cn/img_convert/b5fc554f27421be5fc71910cc66b6fb7.png)

##### babel-preset

如果我们一个个去安装使用插件，那么需要手动来管理大量的babel插件，我们可以直接给webpack提供一个 preset，webpack会根据我们的预设来加载对应的插件列表，并且将其传递给babel

**比如常见的预设有三个：**

1. env
2. react
3. TypeScript

**安装preset-env：**

```shell
npm install @babel/preset-env
```

![image-20210731094336858](https://img-blog.csdnimg.cn/img_convert/b61e542f53c1661d0f800a3a6b3efae9.png)

```js
// 对js代码进行转换
      {
        test: /\.js$/,
        use: {
          loader: "babel-loader",
          // loader的选项
          options: {
            // 使用babel-loader的时候，babel需要的插件
            plugins:[
              "@babel/plugin-transform-arrow-functions",
              "@babel/plugin-transform-block-scoping"
            ],

            // 使用预设
            presets:[
              // 没有参数直接这样写预设，
              "@babel/preset-env",
              // 有参数时：
              ["@babel/preset-env",{
                  // 配置目标浏览器 且这里配置的优先级更高 但是不建议这里写
                  // targets: ["chrome 88"]
              }]
            ]
          }
        }
      }
```

##### Babel的配置文件

像之前一样，我们可以将babel的配置信息放到一个独立的文件中，babel给我们提供了两种配置文件的编写：

1. babel.config.json（或者.js，.cjs，.mjs）文件；
2. .babelrc.json（或者.babelrc，.js，.cjs，.mjs）文件；

它们两个有什么区别呢？目前很多的项目都采用了多包管理的方式（babel本身、element-plus、umi等）；

- .babelrc.json：早期使用较多的配置方式，但是对于配置Monorepos项目是比较麻烦的；
- babel.config.json（babel7）：可以直接作用于Monorepos项目的子包，更加推荐；

**我这里采取的babel.config.js的方式**

```js
// babel 配置文件
module.exports = {
  presets:[
    "@babel/preset-env"
  ],
  // plugins:[]
}
```

### Vue

#### Vue源码的打包

我们主要是学习Vue的，那么我们应该包含Vue相关的代码：

**安装vue**

```shell
npm i vue@next # 安装的是vue3，且是生产依赖
```

**书写使用vue的代码**

```js
// 使用vue

import {createApp} from 'vue';

const app = createApp({
  template:'<h2>我是vue渲染出来的！</h2>',
  data(){
    return {
      title:"啊哈哈！"
    }
  }
});
app.mount("#app");
```

**使用webpack进行打包**

发现：**界面上是没有效果的：**

并且我们查看运行的控制台，会发现如下的警告信息；

![image-20210731095804576](https://img-blog.csdnimg.cn/img_convert/a003a33c793ccde31b81850ee3bf3fa7.png)

#### Vue打包后不同版本解析

##### **vue(.runtime).global(.prod).js：**

1. 通过浏览器中的 `<srcipt src="...">`直接引入
2. 我们之前通过CDN引入和下载的Vue版本就是这个版本；
3. 会暴露一个全局的Vue来使用；

##### vue(.runtime).esm-browser(.prod).js：

用于通过原生 ES 模块导入使用 (在浏览器中通过 

##### vue(.runtime).esm-bundler.js：

1. 用于 **webpack，rollup 和 parcel** 等构建工具； 
2. 构建工具中默认是**vue.runtime.esm-bundler.js**； 
3. 如果我们需要解析模板template，那么需要手动指定vue.esm-bundler.js；

##### vue.cjs(.prod).js：

1. 服务器端渲染使用； 
2. 通过require()在Node.js中使用；

#### 运行时+编译器 vs 仅运行时

在Vue的开发过程中我们有三种方式来编写DOM元素：

- 方式一：template模板的方式（之前经常使用的方式）； 
- 方式二：render函数的方式，使用h函数来编写渲染的内容； 
- 方式三：通过.vue文件中的template来编写模板；

它们的模板分别是如何处理的呢？

**方式二**中的h函数可以直接返回一个虚拟节点，也就是Vnode节点；

**方式一和方式三**的template都需要有**特定的代码**来对其进行解析： 

- 方式三.vue文件中的template可以通过在vue-loader对其进行编译和处理； 
- 方式一种的template我们必须要通过源码中一部分代码来进行编译；

所以，Vue在让我们选择版本的时候分为 **运行时+编译器 vs 仅运行时**

1. 运行时+编译器包含了对template模板的编译代码，更加完整，但是也更大一些；
2. 仅运行时没有包含对template版本的编译代码，相对更小一些；

**所以我们引入vue3应使用如下方式：**

```js
import {createApp} from 'vue/dist/vue.esm-bundler';
```

![image-20210731102206823](https://img-blog.csdnimg.cn/img_convert/03428f9e2383ae5c13ececb215431727.png)

#### 全局标识的配置

**我们会发现控制台还有另外的一个警告：**

![image-20210731101951525](https://img-blog.csdnimg.cn/img_convert/4fcff011ddba27b03ee90f99925f027a.png)

在GitHub上的文档中我们可以找到说明：

![image-20210731102019738](https://img-blog.csdnimg.cn/img_convert/51e6db4e09f0e52f4c445d1b2f42c67d.png)

这是两个特性的标识，一个是使用Vue的Options，一个是Production模式下是否支持devtools工具；

虽然他们都有默认值，但是强烈建议我们手动对他们进行配置；

**可以使用DefinePlugin 插件配置这两个常量**

```js
new DefinePlugin({
      BASE_URL: "'./'",
      __VUE_OPTIONS_API__:true,
      __VUE_PROD_DEVTOOLS__:false
    })
```

#### 编写App.vue代码

在前面我们提到过，真实开发中多数情况下我们都是使用**SFC（ single-file components (单文件组件) ）**。

```vue
<template lang="">
  <h2>你好 vue3 哈哈哈哈 sfc</h2>
  <h3>{{message}}</h3>
</template>
<script>
export default {
  data(){
    return {
      message:"哈哈"
    }
  }
}
</script>
<style scoped>
  h2,h3{
    color:aquamarine;
  }
</style>
```

```js
// 使用 .vue单文件组件的方式
// 可以直接导入vue就可以了。不需要上面的那种导入方式，来解决template模板的解析问题
// 因为现在已经没有template了。而vue文件里面的template会被vue-loader解析
import { createApp } from 'vue';

import App from "../vue/App.vue"

const app = createApp(App);
app.mount("#app")
```

##### App.vue的打包过程

我们对代码打包会报错：提示我们需要合适的Loader来处理文件。

![image-20210731110600146](https://img-blog.csdnimg.cn/img_convert/d285ac30382f08615ef1698af02b22e7.png)

**这个时候我们需要使用vue-loader：**

```shell
npm install vue-loader@next -D
```

**在webpack的模板规则中进行配置：**

![image-20210731110659522](https://img-blog.csdnimg.cn/img_convert/75909a15d100ae35588a7a58e57fd41f.png)

```js
// 配置 .vue文件的加载打包规则
      {
        test: /\.vue$/,
        loader: "vue-loader"
      }
```

##### @vue/compiler-sfc

打包依然会报错，这是因为我们必须添加**@vue/compiler-sfc**来对template进行解析：

```shell
npm install @vue/compiler-sfc -D
```

另外我们需要配置对应的Vue插件：

```js
// 引入Vue-loader的插件 帮助loader做一些事情
const { VueLoaderPlugin } = require("vue-loader/dist/index");
module.exports = {
    plugins:[
        new VueLoaderPlugin()
    ]
}
```

**重新打包即可支持App.vue的写法**

另外，我们也可以编写其他的.vue文件来编写自己的组件；







## webpack-devServer和Vue-CLI

### devServer(热部署)

**目前我们开发的代码，为了运行需要有两个操作：**

**操作一**：npm run build，编译相关的代码； 

**操作二**：通过live server或者直接通过浏览器，打开index.html代码，查看效果；

这个过程经常操作会影响我们的开发效率，我们希望可以做到，当文件发生变化时，可以自动的完成 **编译 和 展示**；

**为了完成自动编译，webpack提供了几种可选的方式：**

1. webpack watch mode； 
2. webpack-dev-server（常用）； 
3. webpack-dev-middleware；

#### Webpack watch

webpack给我们提供了**watch模式**： 

1. 在该模式下，webpack依赖图中的所有文件，只要有一个发生了更新，那么代码将被重新编译； 
2. 我们不需要手动去运行 **npm run build**指令了；

##### **如何开启watch呢？两种方式：**

方式一：在导出的配置中，添加 watch: true；

方式二：在启动webpack的命令中，添加 **--watch的标识**；

![image-20210802172245108](https://img-blog.csdnimg.cn/img_convert/de139d72103f009820a2a5b29e43c514.png)

![image-20210802172200638](https://img-blog.csdnimg.cn/img_convert/43f718fbc2bd1be1a83cb5f4dea11439.png)

**方式二**是在package.json的 scripts 中添加一个 watch 的脚本。

#### webpack-dev-server

上面的方式可以监听到文件的变化，但是事实上它本身是没有自动刷新浏览器的功能的：

1. 当然，目前我们可以在VSCode中使用**live-server**来完成这样的功能； 
2. 但是，我们希望在不使用live-server的情况下，可以具备**live reloading**（**实时重新加载**）的功能；

且，这种开发模式，**效率并不是特别高**：

1. 每次文件发生了一点点改动，都会对所有的源代码进行重新编译
2. 编译成功后，都会生成新的文件（文件操作 file system）
3. live-server属于编译器的插件（vscode插件）-》不属于webpack提供的解决方案！
4. live-server每次都会刷新整个页面

#### **安装webpack-dev-server** (WDS)

```shell
npm i webpack-dev-server -D
```

修改配置文件，告知 dev server，从什么位置查找文件：

```js
module.exports = {
    // 其他配置均省略
  
  // 热部署（热更新）的配置 实时重载 更改文件后也是刷新整个页面
  devServer: {
    // 如果需要的资源没有在webpack里面加载到，会去contentBase指定的文件夹里面寻找
    contentBase: "./public",
  },
  // 打包的是node环境 还是 web 环境
  target: "web"
}
```

dev-server只是实时重载，在修改代码的同时，保存后就会立刻编译，然后刷新页面。

**dev-server**，并不依赖我们的打包文件，因为这是在内存中进行编译打包，直接运行在浏览器上的。（编译后的结果暂存在内存中，浏览器加载快很多）。

暂时来说：dev-server也是一样会编译所有文件，哪怕文件没有发生修改。但是并没有打包生成新的文件。

**dev-serve**是如何把编译后的结果暂存在内存中的？

在很久之前， `webpack-dev-server`插件使用的是 `memory-fs`库，这个库是webpack官方维护的。

但是现在很久不维护的。

现在使用的是`memfs`库。使用这个库来实现的

```json
"script":{
    "serve":"webpack serve" // webpack5以后 直接使用该命令就会会找我们的dev-server插件然后启动一个服务器
}
```

`webpack-dev-server`插件本身已经实现了模块热更新(hot module replacement)(HMR)。但是需要手动开启配置。



![image-20210802172713684](https://img-blog.csdnimg.cn/img_convert/f3ef0fd40604323aeca50a2044bce6eb.png)

![image-20210802172725403](https://img-blog.csdnimg.cn/img_convert/8d9d2f9e58e32c90ef4c60eed1942e18.png)

![image-20210802172828915](https://img-blog.csdnimg.cn/img_convert/8c48aec191266a98e9c5e258049286d9.png)



#### webpack-dev-middleware(了解)

webpack-dev-server本质是在本地开启一个服务，来跑我们的页面程序。其内部使用的express框架。如果我们想开启服务时，使用koa框架等其他操作，那么我们就会用到webpack-dev-middleware。

需要更高的自由度，可以使用该中间件。

但是，react和vue使用的都是`webpack-dev-server`，平时用的很少。

##### 安装

```shell
npm i webpack-dev-middleware express -D
npm i webpack-dev-middleware koa -D
```







#### 认识模块热替换

**什么是HMR呢？**

1. HMR的全称是Hot Module Replacement，翻译为模块热替换； 
2. 模块热替换是指在 应用程序运行过程中，替换、添加、删除模块，而无需重新刷新整个页面；

**HMR通过如下几种方式，来提高开发的速度：**

1. 不重新加载整个页面，这样可以保留某些应用程序的状态不丢失；
2. 只更新需要变化的内容，节省开发的时间； 
3. 修改了css、js源代码，会立即在浏览器更新，相当于直接在浏览器的devtools中直接修改样式；

**如何使用HMR呢？**

1. 默认情况下，`webpack-dev-server`已经支持HMR，我们只需要开启即可； 
2. 在不开启HMR的情况下，当我们修改了源代码之后，整个页面会自动刷新，使用的是live reloading；

##### 开启HMR

**修改webpack的配置：**

![image-20210802173037587](https://img-blog.csdnimg.cn/img_convert/c866da88da62c684aca92a88b3454f90.png)

**浏览器可以看到如下效果：**

![image-20210802173057967](https://img-blog.csdnimg.cn/img_convert/4c5eeefcb12e7a83cc0020f8f4215c36.png)

但是你会发现，当我们修改了某一个模块的代码时，依然是刷新的整个页面：

这是因为我们需要去**指定哪些模块**发生更新时，**进行HMR**；

```js
// 还是需要导入的 不然会报错
import { add } from "./module/utils";
// 开启模块热更新
if (module.hot) {
  // 依赖可以是一个  也可以是数组 多个
  // accept([...依赖], 更新后的回调函数)
  // 更新的模块会被重新执行一次 然后执行回调函数
  module.hot.accept("./module/utils.js", () => {
    console.log("utils热更新");
  });
}
```



![image-20210802173232460](https://img-blog.csdnimg.cn/img_convert/fc6a342eeb9e66c3b0e72e81d569a830.png)

#### 框架的HMR

**有一个问题：在开发其他项目时，我们是否需要经常手动去写入 module.hot.accpet相关的API呢？**

1. 比如开发Vue、React项目，我们修改了组件，希望进行热更新，这个时候应该如何去操作呢？ 
2. 事实上社区已经针对这些有很成熟的解决方案了； 
3. 比如vue开发中，我们使用vue-loader，此loader支持vue组件的HMR，提供开箱即用的体验； 
4. 比如react开发中，有React Hot Loader，实时调整react组件（目前React官方已经弃用了，改成使用react-refresh）；

#### 手动配置react的热模块替换

##### 安装

```shell
npm i react react-dom 
npm i @babel/preset-env @babel/preset-react -D
npm install -D @pmmmwh/react-refresh-webpack-plugin react-refresh # 热模块替换
```

##### 配置webpack.config.js

```js
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { DefinePlugin } = require("webpack");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const ReactRefreshWebpackPlugin = require("@pmmmwh/react-refresh-webpack-plugin");
module.exports = {
  entry: "./src/index.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist"),
  },
  mode: "development",
  devtool: "cheap-module-source-map",
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        use: "babel-loader",
      },
    ],
  },
  plugins: [
    new ReactRefreshWebpackPlugin(),
    new CleanWebpackPlugin(),
    new DefinePlugin({
      BASE_URL: "'./'",
    }),
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      title: "mao webpack",
    })
  ],
  // 配置dev-server 实时重载
  devServer: {
    // 开启热更新 还需要在模块中进行判断 module.hot
    hot: true,
  },
};

```



##### babel的配置

```js
module.exports = {
  presets: [["@babel/preset-env"], ["@babel/preset-react"]],
  plugins: [
    // react 开启自动热模块替换插件
    ["react-refresh/babel"],
  ],
};
```



#### Vue的HMR

Vue的加载需要使用vue-loader，而vue-loader加载组件的默认会帮助我们进行HMR的处理。

##### 安装

```shell
npm i vue 
npm i vue-loader -D
npm i @vue/complier-sfc -D
```

##### webpack配置

```js
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { DefinePlugin } = require("webpack");
// const CopyWebpackPlugin = require("copy-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const ReactRefreshWebpackPlugin = require("@pmmmwh/react-refresh-webpack-plugin");
// vue-loader的插件
const { VueLoaderPlugin } = require("vue-loader/dist/index");
module.exports = {
  entry: "./src/index.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist"),
  },
  mode: "development",
  devtool: "cheap-module-source-map",
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        use: "babel-loader",
      },
      {
        test: /\.vue$/,
        // 还需要处理css
        use: "vue-loader",
      },
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader", "postcss-loader"],
      },
    ],
  },
  plugins: [
    new VueLoaderPlugin(),
    new ReactRefreshWebpackPlugin(), // 生产环境不可用
    new CleanWebpackPlugin(),
    new DefinePlugin({
      BASE_URL: "'./'",
    }),
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      title: "mao webpack",
    })
  ],
  // 配置dev-server 实时重载
  devServer: {
    // 开启热更新 还需要在模块中进行判断 module.hot
    hot: true,
  },
};

```





#### HMR的原理

**那么HMR的原理是什么呢？如何可以做到只更新一个模块中的内容呢？**

1. webpack-dev-server会创建两个服务：提供静态资源的服务（express）和Socket服务（net.Socket）； 
2. express server负责直接提供静态资源的服务（打包后的资源直接被浏览器请求和解析）；

**HMR Socket Server，是一个socket的长连接：**

1. 长连接有一个最好的好处是建立连接后双方可以通信（服务器可以直接发送文件到客户端）； 
2. 当服务器监听到对应的模块发生变化时，会生成两个文件.json（manifest文件）和.js文件（update chunk）； 
3. 通过长连接，可以直接将这两个文件主动发送给客户端（浏览器）； 
4. 浏览器拿到两个新的文件后，通过HMR runtime机制，加载这两个文件，并且针对修改的模块进行更新；

##### HMR的原理图

![image-20210802173450624](https://img-blog.csdnimg.cn/img_convert/69bd48d8adf602bcbc2ba5fca1bb777c.png)



#### output的publicPath

webpack配置文件的output属性到现在都不陌生了，filename和path属性都已经使用过。

该output对象还有个`publicPath`属性。

path属性主要是为了告诉webpack，打包之后的文件放到那个文件夹中。（输出目录）

`publicPath`是指定index.html文件打包引用的一个**基本路径**：

1. 默认值是一个空字符串，所以我们打包后引入js文件时，路径是bundle.js.
2. 开发中，我们也将其设置文件 /,路径是 /bundle.js，那么浏览器会根据所在的域名 + 路径去请求对应的资源。
3. 如果希望在本地直接打开打包后的html文件也能直接运行，需要设置为 ./，此时路径就是 ./budle.js。可以根据相对路径查找资源。

```js
output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist"),
    // vue-cli配置的该属性值是 / 默认值是空字符串 ""
    // 部署的时候 需要配置为 /
    publicPath: "/",
  }
```



#### devServer的publicPath(webpack5使用 devServer.static.publicPath)

该属性是指定本地服务所在的文件夹：

- 默认值是 / 也就是我们直接访问端口，即可访问其中的资源
- 如果将其设置为 /abc，那么需要通过 http://localhost:8080/abc 才能访问到对应的打包后的资源
- 并且这个时候，我们其中的 bundle.js，直接通过域名http://localhost:8080/bundle.js 也是无法访问的：
  - 解决方式就是：
    - 必须将 output.publicPath也设置为 /abc
    - 官方其实有所提到，建议 `devServer.publicPath`与`outputPath`相同

```js
// 开发过程中 开启一个本地服务 是开发环境中使用的
  // 配置dev-server 实时重载
  devServer: {
    // 开启热更新 还需要在模块中进行判断 module.hot
    hot: true,
    // 指定开启服务后本地资源的文件夹
    // 想要查看跑起来的服务的内容 需要在域名端口后面拼接该路径
    publicPath: "",
  },
```



#### devServer的contentBase(webpack5使用devServer.static.directory)

devServer中contentBase对于我们直接访问打包后的资源其实并没有太大的作用，它的主要作用是如果我们打包后的资源，又依赖于其他的一些资源，那么就需要指定从哪里来查找这个内容:

- 比如在index.html中，我们需要依赖一个abc.,js文件，这个文件我们存放在public文件中;
- 在index.html中，我们应该如何去引入这个文件呢?
  - 比如代码是这样的:<script src="./public/abc.js"></script> ;
  - 但是这样打包后浏览器是无法通过相对路径去找到这个文件夹的;
  - 所以代码是这样的:<script src="/abc.js"></script>;
  - 但是我们如何让它去查找到这个文件的存在呢?设置contentBase即可;

当我们设置了contentBase，且打包后有引入其他目录下的资源等，会去contentBase设置的目录下查找；（值一般是绝对路径）



```js
// contentBase 默认值是当前项目的根目录
```



##### static.watch

设置静态资源文件发生改变，会刷新页面。默认是启用。









##### hotOnly、host配置

在启用热模块替换功能，在构建失败时不刷新页面作为回退，使用 `hot: 'only'`

也就是说：当我们编译某个默认出现问题，然后该模块问题被修复的时候，不会刷新整个页面，而是只刷新该模块的内容。

```js
module.exports = {
  //...
  devServer: {
    hot: 'only',
  },
};
```



###### **host设置主机地址：**

![image-20220328170907663](https://img-blog.csdnimg.cn/img_convert/380570c211cfdb3f4181cb0ad8467253.png)

默认值是localhost；如果希望其他地方也可以访问，可以设置为 0.0.0.0；

###### **localhost 和 0.0.0.0 的区别**：

1. localhost：本质上是一个域名，通常情况下会被解析成127.0.0.1;
2. 127.0.0.1：回环地址(Loop Back Address)，表达的意思其实是我们主机自己发出去的包，直接被自己接收;
   - 正常的数据库包经常 应用层 - 传输层 - 网络层 - 数据链路层 - 物理层 ; 
   - 而回环地址，是在网络层直接就被获取到了，是不会经常数据链路层和物理层的; 
   - 比如我们监听 127.0.0.1时，在同一个网段下的主机中，通过ip地址是不能访问的;
3. 0.0.0.0：监听IPV4上所有的地址，再根据端口找到不同的应用程序；比如我们监听 0.0.0.0时，在同一个网段下的主机中，通过ip地址是可以访问的;

![image-20210802174005433](https://img-blog.csdnimg.cn/img_convert/c4bfe395e89debafe836180b24801358.png)

##### port、open、compress

###### prot

**port设置监听的端口，默认情况下是8080**

###### open

**open是否打开浏览器：**

1. 默认值是false，设置为true会打开浏览器； 
2. 也可以设置为类似于 Google Chrome等值；

###### compress

**compress是否为静态文件开启gzip compression：**

默认值是false，可以设置为true；

一般压缩效率大概是50%。

![image-20210802174040312](https://img-blog.csdnimg.cn/img_convert/e7c247e6aca01b34a4dd0a132c7c7fda.png)

![image-20210802174054174](https://img-blog.csdnimg.cn/img_convert/117c8d8dad9e0d30bd7f455737f4d078.png)



#### Proxy代理

**proxy是我们开发中非常常用的一个配置选项，它的目的设置代理来解决跨域访问的问题：**

比如我们的一个api请求是 http://localhost:8888，但是本地启动服务器的域名是 http://localhost:8000，这 个时候发送网络请求就会出现跨域的问题；

那么我们可以将请求先发送到一个代理服务器，代理服务器和API服务器没有跨域的问题，就可以解决我们的跨 域问题了；

**我们可以进行如下的设置**

**target**：表示的是代理到的目标地址，比如 /api-hy/moment会被代理到 http://localhost:8888/api-mhy/moment

**pathRewrite**：默认情况下，我们的 /api-hy 也会被写入到URL中，如果希望删除，可以使用pathRewrite；

**secure**：默认情况下不接收转发到https的服务器上，如果希望支持，可以设置为false；

**changeOrigin**：它表示是否更新代理后请求的headers中host地址

![image-20210802174327012](https://img-blog.csdnimg.cn/img_convert/12c6fb87149f960a13de75b96f046732.png)

```js
module.exports = {
    devServer:{
        // 配置 proxy 代理 解决跨域问题
    // 只适用于开发阶段
    // 相当于请求交给本地服务器发送给远程的服务器
    // 然后请求到的数据在给页面的请求
    proxy: {
      // 配置映射关系
      // 相当于在项目中请求 /api地址 就是请求后面配置的地址
      "/api": {
        target: "http://www.baidu.com",
        // 路径重写 将 /api开头的请求地址的 /api 给替换为 "" 空字符串
        pathRewrite: {
          "^/api": ""
        },
        // 默认不支持 转发到https的服务器上，如果需要，设置 secure为false
        secure: false,
        // 是否更新代理后请求的headers中的host地址
        changeOrigin: true
      }
    },
    }
}
```

#### changeOrigin的解析

这个 changeOrigin官方说的非常模糊，通过查看源码我发现其实是要修改代理请求中的headers中的host属性：

1. 因为我们真实的请求，其实是需要通过 http://localhost:8888来请求的； 
2. 但是因为使用了代码，默认情况下它的值时 http://localhost:8000； 
3. 如果我们需要修改，那么可以将changeOrigin设置为true即可；

使用webpack代理请求之后，可以解决跨域问题，发起的请求默认是从本地开发服务器域名下发起的请求，虽然请求到了数据，但是并不是实际服务器的域名。如果服务器对我们请求做了限制，必须要求请求的是源api接口地址的请求才会返回数据，需要用到该配置项。



#### historyApiFallback

historyApiFallback是开发中非常常见的属性，主要是为了解决SPA页面在路由跳转过程后，进行页面刷新时，返回404的错误。

设置该属性的值为 `true`，则出现404请求错误时直接返回首页。

```js
historyApiFallback: ture
```

还属性值还可以是一个对象：根据不同的路径匹配不同的页面

```js
historyApiFallback:{
    rewrites:[
        {from:/^\/$/, to:"/login.html"}
    ]
}
```











### resolve模块解析

**resolve用于设置模块如何被解析：**

1. 在开发中我们会有各种各样的模块依赖，这些模块可能来自于自己编写的代码，也可能来自第三方库； 
2. resolve可以帮助webpack从每个 require/import 语句中，找到需要引入到合适的模块代码； 
3. webpack 使用 enhanced-resolve 来解析文件路径；

**webpack能解析三种文件路径：**

**绝对路径** :由于已经获得文件的绝对路径，因此不需要再做进一步解析。

**相对路径**

1. 在这种情况下，使用 import 或 require 的资源文件所处的目录，被认为是上下文目录； 
2. 在 import/require 中给定的相对路径，会拼接此上下文路径，来生成模块的绝对路径；

**模块路径**

在 resolve.modules中指定的所有目录检索模块； 

1. **默认值是 ['node_modules']**，所以默认会从node_modules中查找文件； 
2. 我们可以通过设置别名的方式来替换初识模块路径，具体后面讲解alias的配置；

```js
module.exports = {
    //...
    resolve:{
        modules:["node_modules"]
    }
}
```





##### 确实文件还是文件夹

如果是一个文件： 

1. 如果文件具有扩展名，则直接打包文件； 
2. 否则，将使用 resolve.extensions选项作为文件扩展名解析；

```js
// extensions的默认值 会按照先后顺序进行匹配
module.exports = {
    //...
    resolve:{
        extentions: ["wasm", ".mjs", ".js", ".json "]
    }
}
```



**如果是一个文件夹：**

会在文件夹中根据 resolve.mainFiles配置选项中指定的文件顺序查找； 

1. resolve.mainFiles的**默认值是 ['index']**； 所以一般最先匹配的都是文件夹下的index.xxx文件
2. 再根据 **resolve.extensions**来解析扩展名

#### **extensions和alias配置**

##### extentions

extensions是解析到文件时自动添加扩展名：

1. 默认值是 **['.wasm', '.mjs', '.js', '.json']**； 
2. 所以如果我们代码中想要添加加载 **.vue 或者 jsx 或者 ts** 等文件时，我们必须自己写上扩展名；

##### alias

**另一个非常好用的功能是配置别名alias**

1. 特别是当我们项目的目录结构比较深的时候，或者一个文件的路径可能需要 ../../../这种路径片段；
2. 我们可以给某些常见的路径起一个别名；
3. 路径别名对应的路径一般都是绝对路径。

![image-20210802174852517](https://img-blog.csdnimg.cn/img_convert/7ad72f29d88dac44dece72039302f3b3.png)





## webpack环境分离和代码分离





### 如何区分开发环境

**目前我们所有的webpack配置信息都是放到一个配置文件中的：webpack.config.js**

1. 当配置越来越多时，这个文件会变得越来越不容易维护； 
2. 并且某些配置是在开发环境需要使用的，某些配置是在生成环境需要使用的，当然某些配置是在开发和生成环 境都会使用的； 
3. 所以，我们最好对配置进行划分，方便我们维护和管理；

那么，在启动时如何可以区分不同的配置呢？

1. **方案一**：编写两个不同的配置文件，开发和生成时，分别加载不同的配置文件即可； 

   ![image-20220329200041639](https://img-blog.csdnimg.cn/img_convert/52ba4d832eaee93350f93a7a47f5fe0a.png)

2. **方式二**：使用相同的一个入口配置文件，通过设置参数来区分它们；

![image-20210802175011294](https://img-blog.csdnimg.cn/img_convert/fd060b6deef6270de7fa720c9947a429.png)

如果我们采用的是指定开发环境的形式，也就是通过命令启动时：传入开发还是生产环境:

那么我们可以在配置文件中，导出一个函数，函数的返回值是真正的配置(对象)，然后改函数可以接收到我们传入的env环境配置。

因此：我们可以根据环境的不同，导出不同的配置。

![image-20220329200335680](https://img-blog.csdnimg.cn/img_convert/1c3753fded4e238554f8dd3857477a42.png)

#### 入口文件解析

我们之前编写入口文件的规则是这样的：./src/index.js，但是如果我们的配置文件所在的位置变成了 config 目录， 我们是否应该变成 ../src/index.js呢

1. 如果我们这样编写，会发现是报错的，依然要写成 ./src/index.js； 
2. 这是因为入口文件其实是和另一个属性时有关的 context



##### context上下文

基础目录，**绝对路径**，用于从配置中解析入口点(entry point)和 加载器(loader)。

默认**使用 Node.js 进程的当前工作目录**，但是推荐在配置中传入一个值。这使得你的配置独立于 CWD(current working directory, 当前工作目录)。

context的作用是用于解析入口（entry point）和加载器（loader）： 

官方说法：默认是当前路径（但是经过我测试，**默认应该是webpack的启动目录**） 

另外推荐在配置中传入一个值；

![image-20210802175152242](https://img-blog.csdnimg.cn/img_convert/18c6befd63ac76fab2fa42b2050c8c2a.png)

![image-20210802175158275](https://img-blog.csdnimg.cn/img_convert/250361b40a2d27412ec70c49a3f56c03.png)

![image-20220329202806187](https://img-blog.csdnimg.cn/img_convert/0f98825febd973051b5393c3f72ac790.png)

##### 解决相对路径转绝对路径

```js
const path = require("path");

// 获取当前程序的启动时所在的目录
const appDir = process.cwd();
/**
 * 解决相对路径转为 绝对路径问题
 * @param  {...any} relative 相对路径
 * @returns
 */
const resolveApp = (...relative) => path.resolve(appDir, ...relative);
module.exports = resolveApp;
```

配置文件也可以根据开发环境还是生产环境进行配置：

在加载webpack.config.js配置文件的时候，向程序注入是生产环境还是开发环境：

```js
const path = require("path");
// 在命令的地方 使用 --env指定的环境变量 会传给该函数 作为参数
module.exports = function (env) {
  // { WEBPACK_SERVE: true, development: true } 开发阶段
  // { WEBPACK_BUNDLE: true, WEBPACK_BUILD: true, production: true } 生产阶段
  console.warn("---------------------", env);
  const isProd = env.production; // 是否是生产环境
  process.env.production = isProd;
  return {
      //...
  };
};

```

![image-20220329211746903](https://img-blog.csdnimg.cn/img_convert/1af0dd21cba0840c78e80ce5bab37bfb.png)

```js
module.exports = {
  presets: [["@babel/preset-env"], ["@babel/preset-react"]],
  plugins: [
    // 可以区分开发环境和生产环境
    // process.env.production 加载配置文件时注入的
    // ["react-refresh/babel"],
  ],
};

```

![image-20220329211119272](https://img-blog.csdnimg.cn/img_convert/104b398e9f4894927d9f63d90fe4bc2f.png)



#### 区分开发和生成环境配置

**这里我们创建三个文件：**

1. webpack.common.js
2. webpack.prod.js
3. webpack.dev.js

![image-20210802175252457](https://img-blog.csdnimg.cn/img_convert/b5926db698bec5f3f5e8908943595e72.png)

**将公共代码和生产环境的配置以及开发时的配置分开，然后使用webpack-merge插件进行配置文件的合并。**

##### 安装merge合并配置

```shell
npm i webpack-merge -D
```

**使用方式：**

```js
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { DefinePlugin } = require("webpack");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const { merge } = require("webpack-merge");
// 在命令的地方 使用 --env指定的环境变量 会传给该函数 作为参数
module.exports = function (env) {
  // { WEBPACK_SERVE: true, development: true } 开发阶段
  // { WEBPACK_BUNDLE: true, WEBPACK_BUILD: true, production: true } 生产阶段
  console.warn("---------------------", env);
  const isProd = env.production; // 是否是生产环境
  process.env.production = isProd ?? false; // 防止 undefined值，会把该属性变成字符串 undefined
  return merge(isProd?require("xxx配置"):require("xxx配置"), {
      entry:"",
      output:{}
  })
  };
};

```



**webpack.common.js**

```js
// 公共的环境

const path = require("path");
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { DefinePlugin } = require("webpack");
// 引入Vue-loader的插件 帮助loader做一些事情
const { VueLoaderPlugin } = require("vue-loader/dist/index");
module.exports = {
  entry: './src/index.js',
  output: {
    path: path.join(__dirname, '../build'),
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          "style-loader",
          "css-loader",
          "postcss-loader"
        ]
      },
      {
        test: /\.less$/,
        use: [
          "style-loader",
          "css-loader",
          "postcss-loader",
          "less-loader"
        ]
      },
      // 处理图片
      {
        test: /\.(png|jpe?g|JPG)$/,
        type: "asset",
        generator: {
          filename: "image/[name]-[hash:6][ext]"
        },
        parser: {
          dataUrlCondition: {
            maxSize: 40 * 1024
          }
        }
      },
      {
        test: /\.(eot|ttf|woff2?)$/,
        type: 'asset/resource',
        generator: {
          // 这里使用的是filename属性 别忘了！！！！！
          filename: 'font/[name]-[hash:6][ext]'
        }
      },
      // 对js代码进行转换
      {
        test: /\.js$/,
        loader: "babel-loader",
      },
      // 配置 .vue文件的加载打包规则
      {
        test: /\.vue$/,
        loader: "vue-loader"
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      // 要注意：相对路径是从项目根路径出发的，！！！！！！！！！！！
      template: "./public/index.html",
      title: "babel 学习！"
    }),
    new DefinePlugin({
      BASE_URL: "'./'",
      __VUE_OPTIONS_API__: true,
      __VUE_PROD_DEVTOOLS__: false
    }),
    new VueLoaderPlugin()
  ],
  // resolve 模块解析
  // 用于设置模块如何被解析
  resolve: {
    // 默认值就是 node_modules
    modules: ["node_modules"],
    // 文件拓展名 默认值有js mjs json wasm
    extensions: [".js", ".ts", ".json", ".vue"],
    // 如果加载的模块是一个文件夹 会自动去文件夹里面的 index.xx 文件来加载
    mainFiles: ["index"],
    // 配置路径的别名 
    alias: {
      // 在路径的地方使用的js，就代表后面的这个路径
      "js": path.resolve(__dirname, "../src/js")
    }
  },
  // 打包的是node环境 还是 web 环境
  target: "web"
}

```

**webpack.prod.js**

```js
// 生产环境
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const CopyPlugin = require("copy-webpack-plugin");
// 合并当前环境和公共的common环境 使用 webpack-merge插件
const { merge } = require('webpack-merge')
// 导入 公共环境的配置
const commonConfig = require('./webpack.common');
module.exports = merge(commonConfig, {
  mode: 'production',
  devtool: 'eval',
  plugins: [
    new CleanWebpackPlugin(),
    new CopyPlugin({
      patterns: [
        {
          from: "public",
          globOptions: {
            ignore: "**/index.html"
          }
        }
      ]
    }),
  ]
}
)
```

**webpack.dev.js**

```js
// 开发环境

// 合并当前环境和公共的common环境 使用 webpack-merge插件
const { merge } = require('webpack-merge')
// 导入 公共环境的配置
const commonConfig = require('./webpack.common');
module.exports = merge(commonConfig, {
  mode: 'development',
  devtool: 'source-map',

  // 热部署（热更新）的配置
  devServer: {
    // 如果需要的资源没有在webpack里面加载到，会去contentBase指定的文件夹里面寻找
    contentBase: "../public",
    // 开启HMR 热模块替换 (最好配置一下target属性，跟DevServer同级)
    hot: true,
    // 设置ip地址 主机
    host: "127.0.0.1",
    // 端口号
    port: 8800,
    // 是否在服务启动时打开浏览器
    open: true,
    // 开启 gzip压缩 传输速率提高(一般html这种文件不会压缩)
    compress: true,

    // 配置 proxy 代理 解决跨域问题
    // 只适用于开发阶段
    // 相当于请求交给本地服务器发送给远程的服务器
    // 然后请求到的数据在给页面的请求
    proxy: {
      // 配置映射关系
      // 相当于在项目中请求 /api地址 就是请求后面配置的地址
      "/api": {
        target: "http://www.baidu.com",
        // 路径重写 将 /api开头的请求地址的 /api 给替换为 "" 空字符串
        pathRewrite: {
          "^/api": ""
        },
        // 默认不支持 转发到https的服务器上，如果需要，设置 secure为false
        secure: false,
        // 是否更新代理后请求的headers中的host地址
        changeOrigin: true
      }
    },
  }

}
)
```

