# webpack优化

## 代码分离

### 认识代码分离

代码分离（Code Splitting）是webpack一个非常重要的特性：

- 它主要的目的是将代码分离到不同的bundle中，之后我们可以按需加载，或者并行加载这些文件； 
- 比如默认情况下，所有的JavaScript代码（业务代码、第三方依赖、暂时没有用到的模块）在首页全部都加载， 就会影响首页的加载速度； 
- 代码分离可以分出出更小的bundle，以及控制资源加载优先级，提供代码的加载性能；

Webpack中常用的代码分离有三种：

- 入口起点：使用entry配置手动分离代码； 
- 防止重复：使用Entry Dependencies或者SplitChunksPlugin去重和分离代码； 
- 动态导入：通过模块的内联函数调用来分离代码；

### 多入口起点

入口起点的含义非常简单，就是配置多入口：

- 比如配置一个index.js和main.js的入口；

- 他们分别有自己的代码逻辑；

![image-20220330110011953](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220330110011953.png)

### Entry Dependencies(入口依赖)

假如我们的index.js和main.js都依赖两个库：lodash、dayjs

- 如果我们单纯的进行入口分离，那么打包后的两个bunlde都有会有一份lodash和dayjs；

- 事实上我们可以对他们进行共享；

![image-20220330110124845](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220330110124845.png)

配置很灵活，import也可以是再次多入口，是多个文件。



### SplitChunks

另外一种分包的模式是splitChunk，它是使用SplitChunksPlugin来实现的：

- 因为该插件webpack已经默认安装和集成，所以我们并不需要单独安装和直接使用该插件； 
- 只需要提供[SplitChunksPlugin](https://webpack.docschina.org/plugins/split-chunks-plugin/)相关的配置信息即可；

Webpack提供了SplitChunksPlugin默认的配置，我们也可以手动来修改它的配置：

比如默认配置中，chunks仅仅针对于异步（async）请求，我们可以设置为initial或者all；

#### chunks

1. async：当设置chunks的值为async时，只有在异步加载模块的时候，才会进行分包处理该模块
2. initial：同步加载模块的时候，也会进行分包处理。
3. all：同步异步都会进行分包处理

-  默认值是async 
- 另一个值是initial，表示对通过的代码进行处理 
-  all表示对同步和异步代码都进行处理**（最为常用）**



### 其他的splitChunks属性(很少手动配置)

#### minSize和maxSize

**minSize的优先级高于maxSize，**

- minSize：拆分出的包的大小，至少为该minSize的值，默认值是20000B大小，如果需要拆分的包的大小不足该值的大小，是不会进行分包的。
  - 拆分包的大小, 至少为minSize； 
  - 如果一个包拆分出来达不到minSize,那么这个包就不会拆分；
- maxSize：将大于maxSize的包，再次拆分为大于minSize，但是不会大于maxSize的包。



#### minChunks

在模块中，使用（import，require）等关键字引入其他模块的时候，只有引入次数大于等于该值时，才会进行分包。

- 至少被引入的次数，默认是1； 
- 如果我们写一个2，但是引入了一次，那么不会被单独拆分



#### cacheGroups

缓存组，出现在缓存组中的模块，不会直接分包，而是在所有模块加载完毕以后再根据缓存组配置的内容进行分包处理。

- 用于对拆分的包就行分组，比如一个lodash在拆分之后，并不会立即打包，而是会等到有没有其他符合规则的包一起来打 包
- test属性：匹配符合规则的包；
- name属性：拆分包的name属性；
- filename属性：拆分包的名称，可以自己使用placeholder属性；

```js
// 优化
  optimization: {
    // 代码压缩操作
    minimizer: [
      new TerserPlugin({
        // 去除注释信息
        extractComments: false,
      }),
    ],
    splitChunks: {
      chunks: "all",
      // chunks:"async" // 默认值 模块存在异步加载操作(import("文件")) 进行分离
      // 最小值 默认值：20000B ~ 拆分出来的最小的包的大小是 大概20KB
      minSize: 20,
      maxSize: 40000,
      minChunks: 1,
      cacheGroups: {
        // 第三方库 将匹配到的 node_modules下加载的库 都打包到vendors下面
        vendors: {
          test: /[\\\/]node_modules[\/\\]/,
          filename: "[id]_vendors.js",
        },
        // 将自己的 utils文件夹下的文件 打包
        format:{
          test:/[\\\/]utils[\/\\]/,
          filename:"[id]_utils_format.js"
        }
      },
    },
  },
```

![image-20220330111038018](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220330111038018.png)



#### 默认缓存组

```js
cacheGroups: {
        // 第三方库 将匹配到的 node_modules下加载的库 都打包到vendors下面
        vendors: {
          test: /[\\\/]node_modules[\/\\]/,
          filename: "[id]_vendors.js",
        },
        // 默认缓存组 当一个文件被引入超过两次的时候 也分包成一个文件
        default: {
          minChunks: 2,
          filename:"[id]_default.js"
        },
      }
```

![image-20220330120836620](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220330120836620.png)



#### 缓存组优先级

如果一个模块同时满足多个缓存组，那么就将模块分包到优先级高的缓存组中。优先级可以为负数。

如图所示：

![image-20220330121308508](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220330121308508.png)

```js
cacheGroups: {
        // 第三方库 将匹配到的 node_modules下加载的库 都打包到vendors下面
        vendors: {
          test: /[\\\/]node_modules[\/\\]/,
          filename: "[id]_vendors.js",
        },
        // 将自己的 utils文件夹下的文件 打包
        format: {
          test: /[\\\/]utils[\/\\]/,
          filename: "[id]_utils_format.js",
          priority: -30,
        },
        // 默认缓存组 当一个文件被引入超过两次的时候 也分包成一个文件
        default: {
          minChunks: 2,
          filename: "[id]_default.js",
          priority: -20,
        },
      }
```

**整个项目中，只要某个模块的引用次数超过2次（可以等于），也就是说多入口的引入也是一样，都会打包到默认中。**





#### maxAsyncRequests

最大的异步请求数量。默认值 20



#### name

设置拆包的名称：

- 可以设置一个名称，也可以设置为false
- 设置为false，则需要在cacheGroups中设置名称



#### chunkIds

告诉webpack，配置分包的时候，生成的分包文件的`id`采用什么算法。

optimization.chunkIds配置用于告知webpack模块的id采用什么算法生成。

- 有三个比较常见的值：
  - natural：按照数字的顺序使用id； 
  - named：development下的默认值，一个可读的名称的id； 
  - deterministic：确定性的，在不同的编译中不变的短数字id 。确定的文件名一定有确定的短数字id。
    - **在webpack4中是没有这个值**的； 
    - 那个时候如果使用natural，那么在一些编译发生变化时，就会有问题

- 最佳实践：
  - 开发过程中，我们推荐使用named； 
  - 打包过程中，我们推荐使用deterministic；

```js
optimization:{
 	// 采用自然数   
    chunkIds:"natural"
}
```

![image-20220330130702001](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220330130702001.png)

实际开发中很少使用：

因为：

1. 不见名知意
2. 不利于浏览器缓存
3. 如果我们有很多文件，但是有一天删除了生成自然数为1的那个源文件，重新打包，后面的所有文件名都会发生改变，本来并没有修改的代码，因为文件名发生改变，浏览器需要重新请求，无法利用之前的请求缓存。



**而named属性值用的较多。在开发环境中很常见**

![image-20220330131019769](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220330131019769.png)

默认情况下，不配该属性值，打包环境的值就是 `deterministic`

![image-20220330131425484](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220330131425484.png)



#### optimization. runtimeChunk

配置runtime相关的代码是否抽取到一个单独的chunk中：

- runtime相关的代码指的是在运行环境中，对模块进行解析、加载、模块信息相关的代码； 
- 比如我们的component、bar两个通过import函数相关的代码加载，就是通过runtime代码完成的；

抽离出来后，有利于浏览器缓存的策略：

- 比如我们修改了业务代码（main），那么runtime和component、bar的chunk是不需要重新加载的；

- 比如我们修改了component、bar的代码，那么main中的代码是不需要重新加载的；

**设置的值：**

1. true/multiple：针对每个入口打包一个runtime文件； 
2. single：打包一个runtime文件； 
3. 对象：name属性决定runtimeChunk的名称；

```js
optimization:{
    chunkIds:"deterministic",
        runtimeChunk:{
            name:"runtime"
        }
}
```

1. 每个动态模块单独打包 ture/“multiple”

![image-20220331140809500](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220331140809500.png)

2. 将所有的动态模块打包到一个文件 “single”

![image-20220331141025781](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220331141025781.png)

3. 设置为一个对象，使用name属性来决定打包文件的名称（就是预占位的[name]的名字）不包含后缀等

![image-20220331141249536](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220331141249536.png)

通过将 `optimization.runtimeChunk` 设置为 `object`，对象中可以设置只有 `name` 属性，其中属性值可以是名称或者返回名称的函数，用于为 runtime chunks 命名。

默认值是 `false`：每个入口 chunk 中直接嵌入 runtime。

```js
module.exports = {
  //...
  optimization: {
    runtimeChunk: {
      // name: "runtime.module", // single的别名配置
      // name: (entrypoint) => "runtime.module" // single的别名配置
      name: (entrypoint) => `runtime.module-${entrypoint.name}`, // true multiple 的别名配置
    }
  },
};
```

![image-20220331141959288](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220331141959288.png)





## 动态导入(dynamic import)

同步代码的分包，一般我们最多分成四个文件：

- main.bundle.js
- 第三方库.bundle.js(vendor.chunk.js)
- 多次引入的模块，都打包为 common.chunks.js
- runtime.js



对于异步导入的模块，不管你设置什么样的值，chunks的值为什么，webpack都会在打包时帮我们进行分离。

即使chunks的值为initial，也只是表示splitChunks不对异步代码做分包，webpack依然会帮我们分包。

另外一个代码拆分的方式是动态导入时，webpack提供了两种实现动态导入的方式：

- 第一种，使用ECMAScript中的 import() 语法来完成，也是目前推荐的方式； 
- 第二种，使用webpack遗留的 require.ensure，目前已经不推荐使用；

比如我们有一个模块 bar.js：

- 该模块我们希望在代码运行过程中来加载它（比如判断一个条件成立时加载）； 
- 因为我们并不确定这个模块中的代码一定会用到，所以最好拆分成一个独立的js文件； 
- 这样可以保证不用到该内容时，浏览器不需要加载和处理该文件的js代码； 
- 这个时候我们就可以使用动态导入；

**注意：使用动态导入bar.js：**

- 在webpack中，通过动态导入获取到一个对象； 
- 真正导出的内容，在该对象的**default属性**中，所以我们需要做一个简单的解构；

![image-20220330125726688](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220330125726688.png)

异步导入的模块，不管文件的大小，都会进行分包处理的。



### 动态导入的文件命名

动态导入的文件命名：

- 因为动态导入通常是一定会打包成独立的文件的，所以并不会再cacheGroups中进行配置； 

- 那么它的命名我们通常会在output中，通过 chunkFilename 属性来命名；(设置异步加载的打包文件名)

  ```js
  output:{
      chunkFilename:"[name].chunk.js"
  }
  ```

  ![image-20220330131726055](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220330131726055.png)

```js
output: {
        // 异步 分离打包的文件名称
        // 默认情况下：这里的占位name就是我们chunkIds生成的 id
        chunkFilename: "[name].chunk.js",
        // 以入口文件名称作为打包后文件名称前缀
        filename: "[name].bundle.js",
        path: path.resolve(__dirname, "dist"),
      }
```

- 你会发现默认情况下我们获取到的 [name] 是和id的名称保持一致的，如果我们希望修改name的值，可以通过**magic comments（魔法注释）**的方式；

  ![image-20220330194428824](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220330194428824.png)

```js
// 魔法注释： name的值需要加引号
import(/* webpackChunkName:"bar" */ "./bar").then((res) => {
  console.log(res, res.default);
});
```



#### 代码懒加载

动态import使用最多的一个场景是懒加载（比如路由懒加载）：

- 封装一个component.js，返回一个component对象；
- 我们可以在一个按钮点击时，加载这个对象；

![image-20220331133000083](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220331133000083.png)

![image-20220331133028694](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220331133028694.png)

这种方法解决了首屏页面暂时用不到的js文件的获取，是加快首屏页面渲染速度的方式之一：

但是也有弊端：

- 代码的懒加载，导致了只有在我们用到该文件的时候才会获取
- 如果文件特别大，那么发起请求获取文件也是比较耗时的
- 当浏览器获取到文件后还需要进行解析
- 可能导致用户在某个操作后，导致很长时间无法看见效果，对用户体验不好

我们如何避免这种情况？

我们可以让首屏渲染完毕后，在浏览器空闲的时候，提前帮我们把需要懒加载的一些文件提前下载好，在用户执行某些操作后，就不需要再次发起请求，直接解析代码即可。

**在webpack中，做这种效果很简单：**

我们只需要使用魔法注释：webpack就会在浏览器的空闲时间帮我们下载好：

#### 魔法注释

##### 魔法注释：prefetch预获取

只需要在需要提前懒加载的文件前面使用魔法注释：`webpackPrefetch:true`，webpack就会帮我们做好。

```js
btn.addEventListener("click", async () => {
  const { default: div } = await import(
    /* webpackChunkName: "component" */
    /* webpackPrefetch: true */
    "./components/component"
  );
  document.body.appendChild(div);
});
```



![image-20220331134756013](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220331134756013.png)



![image-20220331135014528](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220331135014528.png)

可以发现该组件是**从预获取的缓存**中获取的，而不是再次发起请求。



##### webpackPreload 预加载

- 预加载会和当前懒加载所在的模块一起，以并行的方式开始加载。而预获取是当父模块加载结束后开始加载。

- preload是中等优先级，且立即开始下载。prefetch chunk 在浏览器闲置时下载。

- preload chunk 会在父 chunk 中立即请求，用于当下时刻。prefetch chunk 会用于未来的某个时刻。

- 浏览器支持程度不同。

**推荐组件等的懒加载，使用prefetch**



## CDN

### 什么是CDN

CDN称之为内容分发网络（Content Delivery Network或Content Distribution Network，缩写：CDN）

1. 它是指通过相互连接的网络系统，利用最靠近每个用户的服务器； 
2. 更快、更可靠地将音乐、图片、视频、应用程序及其他文件发送给用户； 
3. 来提供高性能、可扩展性及低成本的网络内容传递给用户；

![image-20220331143410588](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220331143410588.png)

在开发中，我们使用CDN主要是两种方式：

- 方式一：打包的所有静态资源，放到CDN服 务器，用户所有资源都是通过CDN服务器加 载的；

- 方式二：一些第三方资源放到CDN服务器上；







## MiniCssExtractPlugin

MiniCssExtractPlugin可以帮助我们将css提取到一个独立的css文件中，本插件基于 webpack v5 的新特性构建，并且需要 **webpack 5** 才能正常工作。

本插件会将 CSS 提取到单独的文件中，为每个包含 CSS 的 JS 文件创建一个 CSS 文件，并且支持 CSS 和 SourceMaps 的按需加载。

**普通打包方式，webpack是不会把css抽离出来的。**

![image-20220401145135677](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220401145135677.png)

首先，我们需要安装 mini-css-extract-plugin：

```shell
npm install mini-css-extract-plugin -D
```



配置rules和plugins：

```js
module: {
        rules: [
          {
            test: /\.css$/,
            use: [
              // 开发环境才使用 style-loader
              !isProd
                ? {
                    loader: "style-loader",
                  }
                : // 生产环境使用 该loader
                  MinCssExtractPlugin.loader,
              "css-loader",
            ],
          },
        ],
      }
        
plugins: [
    new CleanWebpackPlugin(),
    new MinCssExtractPlugin({
      filename: "css/[name]-[hash:4].css",
    }),
  ]
```

![image-20220401150234263](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220401150234263.png)

![image-20220401150247548](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220401150247548.png)



## DLL库

DLL是什么呢？

- DLL全程是动态链接库（Dynamic Link Library），是为软件在Windows中实现共享函数库的一种实现方式； 
- 那么webpack中也有内置DLL的功能，它指的是我们可以将可以共享，并且不经常改变的代码，抽取成一个共 享的库； 
- 这个库在之后编译的过程中，会被引入到其他项目的代码中；



DLL库的使用分为两步:

- 第一步：打包一个DLL库； 
- 第二步：项目中引入DLL库

注意：在升级到webpack4之后，React和Vue脚手架都移除了DLL库（下面的vue作者的回复）

![image-20220401190249580](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220401190249580.png)





### 打包一个DLL库

如何打包一个DLLPlugin？（创建一个新的项目）

webpack帮助我们内置了一个DllPlugin可以帮助我们打包一个DLL的库文件；



## Terser

### Terser介绍和安装

什么是Terser呢？

- Terser是一个JavaScript的解释（Parser）、Mangler（绞肉机）/Compressor（压缩机）的工具集；
- 早期我们会使用 uglify-js来压缩、丑化我们的JavaScript代码，但是目前已经不再维护，并且不支持ES6+的 语法；
- Terser是从 uglify-es fork 过来的，并且保留它原来的大部分API以及适配 uglify-es和uglify-js@3等；

也就是说，Terser可以帮助我们压缩、丑化我们的代码，让我们的bundle变得更小。

因为Terser是一个独立的工具，所以它可以单独安装：

```shell
npm i terser -g
npm i terser -D
```

### Compress和Mangle的options

Compress option：

- arrows：class或者object中的函数，转换成箭头函数； 
-  arguments：将函数中使用 arguments[index]转成对应的形参名称； 
- dead_code：移除不可达的代码（tree shaking）； 
- 其他属性可以查看文档

Mangle option

- toplevel：默认值是false，顶层作用域中的变量名称，进行丑化（转换）； 
- keep_classnames：默认值是false，是否保持依赖的类名称； 
- keep_fnames：默认值是false，是否保持原来的函数名称； 
- 其他属性可以查看文档；

### Terser在webpack中配置

真实开发中，我们不需要手动的通过terser来处理我们的代码，我们可以直接通过webpack来处理：

- 在webpack中有一个minimizer属性，在production模式下，默认就是使用TerserPlugin来处理我们的代码的；
- 如果我们对默认的配置不满意，也可以自己来创建TerserPlugin的实例，并且覆盖相关的配置；

首先，我们需要打开minimize，让其对我们的代码进行压缩（默认production模式下已经打开了）

其次，我们可以在minimizer创建一个TerserPlugin：

- extractComments：默认值为true，表示会将注释抽取到一个单独的文件中(在开发中，我们不希望保留这个注释时，可以设置为false；)

- parallel：使用多进程并发运行提高构建的速度，默认值是true，并发运行的默认数量： os.cpus().length - 1；(我们也可以设置自己的个数，但是使用默认值即可；)

- terserOptions：设置我们的terser相关的配置:
  - compress：设置压缩相关的选项； 
  - mangle：设置丑化相关的选项，可以直接设置为true； 
  - toplevel：底层变量是否进行转换； 
  - keep_classnames：保留类的名称； 
  - keep_fnames：保留函数的名称；



#### 优化 optimization

#### minimize

```js
boolean = true
```

告知 webpack 使用 [TerserPlugin](https://webpack.docschina.org/plugins/terser-webpack-plugin/) 或其它在 [`optimization.minimizer`](https://webpack.docschina.org/configuration/optimization/#optimizationminimizer)定义的插件压缩 bundle。

**设置为ture，表示使用terser进行压缩js，fasle表示不使用**

#### minimizer

允许你通过提供一个或多个定制过的 [TerserPlugin](https://webpack.docschina.org/plugins/terser-webpack-plugin/) 实例，覆盖默认压缩工具(minimizer)。



##### paraller

使用多进程并发运行以提高构建速度。 并发运行的默认数量： `os.cpus().length - 1` 。

> 
> 并发运行可以显著提高构建速度，因此**强烈建议添加此配置** 。

> 如果你使用 **Circle CI** 或任何其他不提供 CPU 实际可用数量的环境，则需要显式设置 CPU 数量，以避免 `Error: Call retries were exceeded`

```js
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        parallel: true,
      }),
    ],
  },
};
```

![image-20220402114856546](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402114856546.png)





## CSS的压缩

另一个代码的压缩是CSS：

- CSS压缩通常是去除无用的空格等，因为很难去修改选择器、属性的名称、值等； 
- CSS的压缩我们可以使用另外一个插件：css-minimizer-webpack-plugin； 
- css-minimizer-webpack-plugin是使用cssnano工具来优化、压缩CSS（也可以单独使用）；

**安装 css-minimizer-webpack-plugin：**

```shell
 npm i -D css-minimizer-webpack-plugin
```

**在optimization.minimizer中配置**

![image-20220402115813663](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402115813663.png)

设置 `paraller:true`，可以多线程并行压缩，加快速度。



## Scope Hoisting

什么是Scope Hoisting呢？

- Scope Hoisting从webpack3开始增加的一个新功能； 
- 功能是对作用域进行提升，并且让webpack打包后的代码更小、运行更快；

默认情况下webpack打包会有很多的函数作用域，包括一些（比如最外层的）IIFE：

- 无论是从最开始的代码运行，还是加载一个模块，都需要执行一系列的函数；
- Scope Hoisting可以将函数合并到一个模块中来运行；

使用Scope Hoisting非常的简单，webpack已经内置了对应的模块：

- **在production模式下，默认这个模块就会启用；**

- 在development模式下，我们需要自己来打开该模块

![image-20220402120624555](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402120624555.png)







# Tree Shaking以及其他优化



## Tree Shaking

### 什么是Tree Shaking

什么是Tree Shaking呢？

- Tree Shaking是一个术语，在计算机中表示消除死代码（dead_code）； 
- 最早的想法起源于LISP，用于消除未调用的代码（纯函数无副作用，可以放心的消除，这也是为什么要求我们在进 行函数式编程时，尽量使用纯函数的原因之一）； 
- 后来Tree Shaking也被应用于其他的语言，比如JavaScript、Dart；



JavaScript的Tree Shaking：

1. 对JavaScript进行Tree Shaking是源自打包工具rollup（后面我们也会讲的构建工具）； 
2. 这是因为Tree Shaking依赖于ES Module的静态语法分析（不执行任何的代码，可以明确知道模块的依赖关系）； 
3. webpack2正式内置支持了ES2015模块，和检测未使用模块的能力； 
4. 在webpack4正式扩展了这个能力，并且通过 package.json的 sideEffects属性作为标记，告知webpack在编译时， 哪里文件可以安全的删除掉； 
5. webpack5中，也提供了对部分CommonJS的tree shaking的支持； 
6. https://github.com/webpack/changelog-v5#commonjs-tree-shaking



### webpack实现Tree Shaking

事实上webpack实现Tree Shaking采用了两种不同的方案：

- usedExports：通过标记某些函数是否被使用，之后通过Terser来进行优化的； 
- sideEffects：跳过整个模块/文件，直接查看该文件是否有副作用；

![image-20220402125841040](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402125841040.png)

#### usedExports

将mode设置为development模式：

- 为了可以看到 usedExports带来的效果，我们需要设置为 development 模式
- 因为在 production 模式下，webpack默认的一些优化会带来很大额影响。

设置usedExports为true和false对比打包后的代码：

- 在usedExports设置为true时，会有一段注释：unused harmony export mul；
-  这段注释的意义是什么呢？告知Terser在优化时，可以删除掉这段代码；

这个时候，我们讲 minimize设置true：

- usedExports设置为false时，mul函数没有被移除掉；
- usedExports设置为true时，mul函数有被移除掉；

所以，usedExports实现Tree Shaking是结合Terser来完成的。

```js
optimization: {
    // 不使用 terser
    minimize: false,
    // tree shaking 方式一 开启
    /** 
     * 开启该属性的作用：是为了标注打包后的代码中，那些函数是未使用过的
     * 使用注释 unused harmony export xxx函数  ---进行标注
     * 然后此时在打开 terser  ：minimize: true
     * terser会根据注释 进行 tree shaking 
    */
    usedExports: true,
}
```

![image-20220402131910394](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402131910394.png)

**结合terser：**

```js
optimization: {
    // 不使用 terser false
    minimize: true,
    // tree shaking 方式一 开启
    /**
     * 开启该属性的作用：是为了标注打包后的代码中，那些函数是未使用过的
     * 使用注释 unused harmony export xxx函数  ---进行标注
     * 然后此时在打开 terser  ：minimize: true
     * terser会根据注释 进行 tree shaking
     */
    usedExports: true,
    // 代码压缩操作
    minimizer: [
      new CssMiniMizerWebpackPlugin({
        parallel: true,
      }),
      // 由 terser 将未使用的函数 从代码中删除
      new TerserWebpackPlugin({
        parallel: true,
        extractComments: false,
        terserOptions: {
          compress: {
            arguments: false,
            keep_classnames: true,
            keep_fnames: true,
            dead_code: true,
          },
          toplevel: true,
          mangle: true,
        },
      }),
    ]
}
```

![image-20220402132535735](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402132535735.png)

可以看见，mul函数是直接删除了。



#### sideEffects

sideEffects用于告知webpack compiler哪些模块时有副作用的：

- 副作用的意思是这里面的代码有执行一些特殊的任务，不能仅仅通过export来判断这段代码的意义；
- 副作用的问题，在讲React的纯函数时是有讲过的；

在package.json中设置sideEffects的值：

- 如果我们将sideEffects设置为false，就是告知webpack可以安全的删除未用到的exports； 
- 如果有一些我们希望保留，可以设置为数组；

比如我们有一个format.js、style.css文件：

- 该文件在导入时没有使用任何的变量来接受（import “format.js”）；
- 那么打包后的文件，不会保留format.js、style.css相关的任何代码；

**如果设置sideEffects的值为true，表示所有模块都有副作用，设置为false，表示所有模块都没有副作用。**

也可以设置为数组，指定那些模块有副作用。

```json
{
"sideEffects":[
    "./src/format.js",
    "**.css" // 表示所有的css都有副作用
]
}
```

**当然，我们实际开发过程中，建议编写纯模块的代码。那么，也就是说只有css文件才有副作用了。**如果只是因为css文件，就在这里设置sideEffects，似乎有点大材小用。

**所以webpack提供的有其他方式：**

我们可以在配置loader的时候，使用 `sideEffects`。

```js
module: {
        rules: [
          {
            test: /\.css$/,
            use: [
              // 开发环境才使用 style-loader
              !isProd
                ? {
                    loader: "style-loader",
                  }
                : // 生产环境使用 该loader
                  MinCssExtractPlugin.loader,
              "css-loader",
            ],
            // css 文件都有副作用
            sideEffects: true,
          },
        ],
      }
```



### Webpack中tree shaking的设置

所以，如何在项目中对JavaScript的代码进行TreeShaking呢（生成环境）？

1. 在optimization中配置usedExports为true，来帮助Terser进行优化； 
2. 在package.json中配置sideEffects，直接对模块进行优化；





## CSS实现Tree Shaking

上面我们学习的都是关于JavaScript的Tree Shaking，那么CSS是否也可以进行Tree Shaking操作呢？

- CSS的Tree Shaking需要借助于一些其他的插件； 
- 在早期的时候，我们会使用PurifyCss插件来完成CSS的tree shaking，但是目前该库已经不再维护了（最新更 新也是在4年前了）； 
- 目前我们可以使用另外一个库来完成CSS的Tree Shaking：PurgeCSS，也是一个帮助我们删除未使用的CSS 的工具；

**安装PurgeCss的webpack插件：**

```shell
npm i -D purgecss-webpack-plugin
```



### 配置PurgeCss

配置这个插件（生成环境）：

- paths：表示要检测哪些目录下的内容需要被分析，这里我们可以使用glob；
- 默认情况下，Purgecss会将我们的html标签的样式移除掉，如果我们希望保留，可以添加一个safelist的属性；

- purgecss也可以对less文件进行处理（所以它是对打包后的css进行tree shaking操作）；

![image-20220402151254354](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402151254354.png)

配置的paths路径是绝对路径，这里使用了glob库，webpack已经内置了，用来解析路径。

```js
new PurgeCssWebpackPlugin({
      // 匹配当前项目根目录src开始下的所有文件夹内的所有文件
      paths: glob.sync(`${path.resolve(process.cwd(), "./src")}/**/*`, {
        nodir: true,
      }),
      // 白名单 出现在这里的元素不会被删除 貌似html,body会被删除
      safelist: () => ({ standard: ["body"] }),
    })
```



## webpack-压缩

### 什么是HTTP压缩

HTTP压缩是一种内置在 服务器 和 客户端 之间的，以改进传输速度和带宽利用率的方式

HTTP压缩的流程什么呢？

1. HTTP数据在服务器发送前就已经被压缩了；（可以在webpack中完成）

2. 兼容的浏览器在向服务器发送请求时，会告知服务器自己支持哪些压缩格式；
3. 服务器在浏览器支持的压缩格式下，直接返回对应的压缩后的文件，并且在响应头中告知浏览器



### 目前的压缩格式

目前的压缩格式非常的多：

- compress – UNIX的“compress”程序的方法（历史性原因，不推荐大多数应用使用，应该使用gzip或 deflate）； 
- deflate – 基于deflate算法（定义于RFC 1951）的压缩，使用zlib数据格式封装； 
- gzip – GNU zip格式（定义于RFC 1952），是目前使用比较广泛的压缩算法； 
- br – 一种新的开源压缩算法，专为HTTP内容的编码而设计；



### Webpack对文件压缩

webpack中相当于是实现了HTTP压缩的第一步操作，我们可以使用CompressionPlugin。

安装：

```shell
npm i -D compression-webpack-plugin
```

**使用CompressionPlugin**

```js
    new CompressWebpackPlugin({
      // 文件大小超过多少时 才会进行压缩 默认0
      // 但是实际上文件太小也不会压缩
      threshold: 0,
      // 匹配那些文件需要压缩
      test: /\.js|css$/,
      // 设置最小的压缩比例 如果压缩比例达不到 则不进行压缩了
      minRatio: 0.99,
      // 采用的压缩算法
      algorithm:"gzip",
      // include
      // exclude
    }),
```

![image-20220402153830593](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402153830593.png)



### HTML文件中代码的压缩

我们之前使用了HtmlWebpackPlugin插件来生成HTML的模板，事实上它还有一些其他的配置：

- inject：设置打包的资源插入的位置

![image-20220402154245505](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402154245505.png)

- cache：设置为true，只有当文件改变时，才会生成新的文件（默认值也是true）

- minify：默认会使用一个插件html-minifier-terser

  ![image-20220402154450190](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402154450190.png)

  ```js
  // 默认值：
  {
    collapseWhitespace: true,
    keepClosingSlash: true,
    removeComments: true,
    removeRedundantAttributes: true,
    removeScriptTypeAttributes: true,
    removeStyleLinkTypeAttributes: true,
    useShortDoctype: true
  }
  ```

  



```js
new HtmlWebpackPlugin({
          template: "./public/index.html",
          title: "mao webpack",
          // 注入打包后的 css js 的位置 
          // inject: true,
          // 是否使用缓存 当文件没有发生任何改变时 直接使用之前的缓存
          cache: true,
          // false 不对html模板做压缩了
          minify: false,
        }),
```

![image-20220402155001270](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402155001270.png)

```js
new HtmlWebpackPlugin({
          template: "./public/index.html",
          title: "mao webpack",
          // 注入打包后的 css js 的位置
          // inject: true,
          // 是否使用缓存 当文件没有发生任何改变时 直接使用之前的缓存
          cache: true,
          // false 不对html模板做压缩了
          // minify: false,
          minify: {
            // 移除注释
            removeComments: true,
            // 移除多余的属性 比如input的type类型默认就是text 是没必要写上去的
            removeRedundantAttributes: true,
            // 移除空属性 id="" 就会被移除
            removeEmptyAttributes: true,
            // 移除空行
            removeTagWhitespace: true,
            // 内联css压缩
            minifyCSS: true,
            // 对直接在script标签书写的js进行压缩
            // minifyJS:true,
            minifyJS: {
              // 使用terser插件丑化压缩
              mangle: {
                toplevel: true,
              },
            },
          },
        }),
```





### InlineChunkHtmlPlugin

另外有一个插件，可以辅助将一些chunk出来的模块，内联到html中:

- 比如runtime的代码，代码量不大，但是是必须加载的； 
- 那么我们可以直接内联到html中
- 也就是说，直接把这部分代码内嵌到html文件中，这样在请求html的时候，直接一次请求就获取成功了

这个插件是在react-dev-utils中实现的，所以我们可以安装一下它：

```shell
npm install react-dev-utils -D
```

**在production的plugins中进行配置**

**默认情况下，runtime文件被单独打包了，这是因为我们在optimization中配置了runtimeChunk**

![image-20220402162411704](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402162411704.png)

![image-20220402162420122](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402162420122.png)

**配置后：**

![image-20220402162829956](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402162829956.png)

![image-20220402162904665](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402162904665.png)

虽然，我们还能看见生成了runtime-的文件，但html模板并没有对其还产生引用。



### 封装Library

webpack可以帮助我们打包自己的库文件，比如我们需要打包一个mao-utils的一个库

![image-20220402173759854](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20220402173759854.png)





## 打包分析

### 分析一：打包的时间分析

如果我们希望看到每一个loader、每一个Plugin消耗的打包时间，可以借助于一个插件：**speed-measure-webpack-plugin**

- 注意：该插件在最新的webpack版本中存在一些兼容性的问题（和部分Plugin不兼容） 
- 截止2021-3-10日，但是目前该插件还在维护，所以可以等待后续是否更新； 
- 我这里暂时的做法是把不兼容的插件先删除掉，也就是不兼容的插件不显示它的打包时间就可以了；

安装speed-measure-webpack-plugin插件

```shell
npm i -D speed-measure-webpack-plugin
```

使用speed-measure-webpack-plugin插件

- 创建插件导出的对象 SpeedMeasurePlugin；

- 使用 smp.wrap 包裹我们导出的webpack配置；

```js
const SpeedMeasurePlugin = require("speed-measure-webpack-plugin")
const smp = new SpeedMeasurePlugin
module.exports = smp({
    // ... 配置
})
```



### 分析二：打包后文件分析

方案一：生成一个stats.json的文件

```json
{
    "script":{
        "buiebpack ld:stats": "w--config ./config/webpack.common.js --env production --profile --json=stats.json"
    }
}
```

通过执行npm run build:status可以获取到一个stats.json的文件：

- 这个文件我们自己分析不容易看到其中的信息；

- 可以放到 http://webpack.github.com/analyse，进行分析



#### 方案二：使用webpack-bundle-analyzer工具

另一个非常直观查看包大小的工具是**webpack-bundle-analyzer**。

**我们可以直接安装这个工具**

```shell
npm install webpack-bundle-analyzer -D
```

我们可以在webpack配置中使用该插件

```js
const BundleAnalyzerPlugin = require("webpack-bundle-analyzer")
module.exports = {
    plugins:[
        //...
        new BundleAnalyzerPlugin()
    ]
}
```

在打包webpack的时候，这个工具是帮助我们打开一个8888端口上的服务，我们可以直接的看到每个包的大小:

- 比如有一个包时通过一个Vue组件打包的，但是非常的大，那么我们可以考虑是否可以拆分出多个组件，并且对其进 行懒加载
- 比如一个图片或者字体文件特别大，是否可以对其进行压缩或者其他的优化处理；

