# Node





## 第三方模块

### express 模块



### mysql 模块



### Gulp 模块

#### 1. 什么是gulp

**基于node平台开发的前端构建工具**

将机械化操作编写成任务，想要执行机械化操作时，执行一个命令行命令任务就能自动执行了。用机器代替手工，提高开发效率。

#### 2. Gulp可以做什么

- 项目上线，HTML，CSS，JS文件压缩合并
- 语法转换（ES6，less，）
- 公共文件抽离
- 修改文件浏览器自动刷新

#### 3. Gulp的使用

1.  使用 `npm install gulp`  安装gulp库文件
2. 在项目根目录下建立gulpfile.js 文件
3. 重构项目的文件夹结构。src目录放置源代码文件，dict目录放置构建后文件
4. 在 gulpfile.js 文件中编写任务
5. 在命令行工具中执行 gulp 任务

#### 4. Gulp中提供的方法

- gulp.src() 获取任务要处理的文件
- gulp.dest() ：输出文件
- gulp.task()： 建立gulp任务
- gulp.watch()： 监控文件的变化

```js
// 引入 gulp
const gulp = require("gulp");
// 使用 gulp.task() 建立任务
// 参数1: 任务的名称  参数2： 任务的回调函数
gulp.task("first",()=>{
  console.log("第一个gulp任务执行了");
  // 使用 gulp.src() 获取要处理的文件
  gulp.src("./src/css/index.css")
  // pipe() 管道 要输出到执行文件夹的代码要写在这个函数里面
  .pipe(gulp.dest("./dict/css"));
});
```

**执行gulp命令：**

1. 先安装 命令行工具 npm install --global gulp-cli
2. 进入项目根目录 
3. gulp  项目名称

#### 5. Gulp插件

![image-20210424211805702](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5C%E5%89%8D%E7%AB%AF%5Cnode%5Cnode.assets%5Cimage-20210424211805702.png)

```js
// 压缩html代码
const htmlmin = require('gulp-htmlmin');
gulp.task("htmlmin",()=>{
  gulp.src("./src/*.html")
  // 压缩html文件中的代码  这个参数的意思：压缩空格
  .pipe(htmlmin({collapseWhitespace:true}))
  .pipe(gulp.dest("./dict"));
})
```

