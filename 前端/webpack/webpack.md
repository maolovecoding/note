# webpack学习之旅



## 邂逅webpack

### 前端开发的复杂化

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



### 前端三个框架的脚手架



#### 脚手架依赖webpack

**事实上我们上面提到的所有脚手架都是依赖于webpack的：**

![image-20210726131437248](https://gitee.com/maolovecoding/picture/raw/master/images/web/webpack/image-20210726131437248.png)



#### webpack是什么

![image-20210726131238052](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210726131238052.png)

**我们先来看一下官方的解释：**

![image-20210726131459921](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210726131459921.png)

webpack是一个**静态的模块化打包工具**，为现代的JavaScript应用程序；

**我们来对上面的解释进行拆解：**

1. **打包bundler**：webpack可以将帮助我们进行打包，所以它是一个打包工具 
2. **静态的static**：这样表述的原因是我们最终可以将代码打包成最终的静态资源（部署到静态服务器）； 
3. **模块化module**：webpack默认支持各种模块化开发，ES Module、CommonJS、AMD等； 
4. **现代的modern**：我们前端说过，正是因为现代前端开发面临各种各样的问题，才催生了webpack的出现和发展；





