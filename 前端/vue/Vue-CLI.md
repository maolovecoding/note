<h1 style='color:red' align='center'>Vue-CLI脚手架</h1>

## Vue-CLI 脚手架

官方称为 命令行工具。俗称 **脚手架。目前最新的版本 4.5.12**

### 脚手架的安装

#### 1. Vue-CLI 3.x版本的安装

**全局安装脚手架（3.x及其以上）**

```shell
npm i @vue/cli -g
```

#### 2. **全局安装脚手架可以使用2.x版本构建项目的包**

```shell
npm i @vue/cli-init -g
```

**脚手架 2.x 构建项目的方式**

```shell
vue init webpack 项目名称
```

![image-20210503154715830](F:/MarkDown/%E7%AC%94%E8%AE%B0/%E5%89%8D%E7%AB%AF/vue/Vue-CLI.assets/image-20210503154715830.png)

#### Runtime-Compiler和Runtime-only的区别

![image-20210503163304240](F:/MarkDown/%E7%AC%94%E8%AE%B0/%E5%89%8D%E7%AB%AF/vue/Vue-CLI.assets/image-20210503163304240.png)

如果在之后的开发中，你依然使用template，就需要选择Runtime-Compiler

如果你之后的开发中，使用的是.vue文件夹开发，那么可以选择Runtime-only

#### render函数 和 template的区别

**Runtime-Compiler 和 Runtime-only**

![image-20210503163415076](F:/MarkDown/%E7%AC%94%E8%AE%B0/%E5%89%8D%E7%AB%AF/vue/Vue-CLI.assets/image-20210503163415076.png)

为什么存在这样的差异呢？

我们需要先理解Vue应用程序是如何运行起来的

Vue中的模板如何最终渲染成真实DOM。

![image-20210503163451612](F:/MarkDown/%E7%AC%94%E8%AE%B0/%E5%89%8D%E7%AB%AF/vue/Vue-CLI.assets/image-20210503163451612.png)

![image-20210503163504123](F:/MarkDown/%E7%AC%94%E8%AE%B0/%E5%89%8D%E7%AB%AF/vue/Vue-CLI.assets/image-20210503163504123.png)

![image-20210503163510843](F:/MarkDown/%E7%AC%94%E8%AE%B0/%E5%89%8D%E7%AB%AF/vue/Vue-CLI.assets/image-20210503163510843.png)

![image-20210503163516749](F:/MarkDown/%E7%AC%94%E8%AE%B0/%E5%89%8D%E7%AB%AF/vue/Vue-CLI.assets/image-20210503163516749.png)

```js
import Cpn from './Cpn.vue'
new Vue({
    el:"#app",
    render:function(createElement){
        // 普通用法
        return createElement('标签名',{'标签的属性'},['标签里面的内容']);
        return createElement('h2',{class:'box'},['哈哈',createElement('button',{class;"btn"},['按钮'])])
        // 高级用法
    return createElement(组件名)
        return createElement(C)
    }
    
})
```

