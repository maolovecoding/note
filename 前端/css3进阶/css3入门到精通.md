# HTML强化

## HTML5新增内容

- 新区块标签(主要是下面四个)

- section --》 区块，定义文档中的节。节（section）是有主题的内容组，通常具有标题。可以将网站首页划分为简介、内容、联系信息等节。

  ```html
  <section>
     <h1>WWF</h1>
     <p>The World Wide Fund for Nature (WWF) is....</p>
  </section> 
  ```

- article --》 区块，规定独立的自包含内容。文档有其自身的意义，并且可以独立于网站其他内容进行阅读。**用以相对完整的区块。**

  ![image-20210823213711580](https://gitee.com/mao0826/picture/raw/master/images/web/wei_chat/image-20210823213711580.png)

- nav --》导航,元素定义导航链接集合。<nav> 元素旨在定义大型的导航链接块。不过，并非文档中所有链接都应该位于 <nav> 元素中！

- aside --》<aside> 元素页面主内容之外的某些内容（比如侧栏）。aside 内容应该与周围内容相关。**一般来说可以理解为放置不重要的内容。比如友情链接？**

![image-20210823213338863](https://gitee.com/mao0826/picture/raw/master/images/web/wei_chat/image-20210823213338863.png)

**HTML5 中的语义元素**

| 标签         | 描述                                               |
| :----------- | :------------------------------------------------- |
| <article>    | 定义文章。                                         |
| <aside>      | 定义页面内容以外的内容。                           |
| <details>    | 定义用户能够查看或隐藏的额外细节。                 |
| <figcaption> | 定义 <figure> 元素的标题。                         |
| <figure>     | 规定自包含内容，比如图示、图表、照片、代码清单等。 |
| <footer>     | 定义文档或节的页脚。                               |
| <header>     | 规定文档或节的页眉。                               |
| <main>       | 规定文档的主内容。                                 |
| <mark>       | 定义重要的或强调的文本。                           |
| <nav>        | 定义导航链接。                                     |
| <section>    | 定义文档中的节。                                   |
| <summary>    | 定义 <details> 元素的可见标题。                    |
| <time>       | 定义日期/时间。                                    |

### 表单增强

- 日期，时间，搜索
- 表单验证
- Placeholder默认内容  自动聚焦

- header/footer  头/尾
- section/article 区域
- nav 导航
- aside 不重要内容
- em/strong 强调
- i icon

### HTML元素分类

#### 按默认样式分

- 块级 block --》 元素独占一行

- 行内 inline  --》 元素可以和其他的元素同处于一行。元素的位置还可能是不规整的。

  ![image-20210823215443839](https://gitee.com/mao0826/picture/raw/master/images/web/wei_chat/image-20210823215443839.png)

- inline-block 行内块级元素

#### 按内容分

![image-20210823215556483](https://gitee.com/mao0826/picture/raw/master/images/web/wei_chat/image-20210823215556483.png)

### HTML的嵌套规则

- 块级元素可以包含行内元素
- **块级元素不一定能包含块级元素**
- 行内元素**一般**不能包含块级元素

**问题来了，什么叫一般？**

在HTML5中，a标签是可以包含块级元素的。比如 a > div 就是合法的。

按照html4.0的规范，a标签是不能包含块级元素的。但是很多浏览器是支持的。比如：点图片就会跳转到其他链接地址。在HTML5中，做了合法的规范化。

**块级元素不一定能包含块级元素**

比如下列代码：

```html
<p><a href="#"><div>P &gt; A &gt; DIV</div></a></p>
```

![image-20210823220821656](https://gitee.com/mao0826/picture/raw/master/images/web/wei_chat/image-20210823220821656.png)

**因为这样做不符合要求，浏览器知道我们写错了，会自动帮我们纠正，也就是浏览器的容错机制。浏览器会猜测我们想怎样做，然后进行修正。**

#### 综述

**a > div为什么可以合法，也可以不合法？**

如果这个a标签外面是div这种，那么就是合法的，如果是像p标签，span这种，就是不合法的。在实际的嵌套过程中，会把a标签拿掉进行判断。



## HTML面试题

### 1. doctype的意义

- 让浏览器以表中模式渲染
- 让浏览器知道元素的合法性

### HTML5有什么变化

- 新的语义化元素
- 表单增强
- 新的API（离线，音视频，图形，实时通信，本地存储，设备能力）

- 分类和嵌套变更

### em和i有什么区别

- em是语义化标签，表强调，有斜体样式
- i是纯样式的标签，表斜体
- HTML5不推荐使用i标签，i标签现在一般用作图标



### 语义化的意义

- 开发者容易理解
- 机器容易理解结构（搜索，读屏软件）
- 有助于SEO搜索优化
- semantic microdata

### 那些元素可以自闭合

- 表单元素 input
- 图片 img
- br hr
- meta link

### HTML和DOM的关系

- HTML是 “死” 的
- DOM是由HTML解析而来，是活的，存在内存中
- JS可以维护DOM

### property和attribute的区别

在HTML中，我们一般称 property为特性，attribute为属性

**区别：**

- attribute是死的
- property是活的

属性和特性是不同步的。

```html
<input value="1"/>
```

你通过js修改了这个输入框的值，但是你获取这个属性框的value属性值，发现还是1，不会改变。

### form的作用

- 直接提交表单
- 使用submit/reset 按钮
- 便于浏览器保存表单
- 第三方库可以整体提取值
- 第三方库可以进行表单验证



# CSS

### CSS基础

**浏览器解析css选择器，是从右往左解析的**

