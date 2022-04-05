# jsx核心语法 必知必会

## 认识jsx

![image-20220117171026858](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20220117171026858.png)

这个render函数返回值的标签语法是什么呢？（return跟上一个括号只是为了表示这是一个整体，方便换行）

- 它不是一段字符串（因为没有使用引号包裹），它看起来是一段HTML原生，但是我们能在js中直接给一个变量赋值html吗？
- 其实是不可以的，如果我们讲 type="text/babel" 去除掉，那么就会出现语法错误； 
- 它到底是什么呢？其实它是一段jsx的语法；

### JSX是什么？

- JSX是一种JavaScript的语法扩展（eXtension），也在很多地方称之为JavaScript XML，因为看起就是一段XML语法；
- 它用于描述我们的UI界面，并且其完成可以和JavaScript融合在一起使用； 
- 它不同于Vue中的模块语法，你不需要专门学习模块语法中的一些指令（比如v-for、v-if、v-else、v-bind）；



## 为什么React选择了JSX

**React认为渲染逻辑本质上与其他UI逻辑存在内在耦合**

- 比如UI需要绑定事件（button、a原生等等）； 
- 比如UI中需要展示数据状态，在某些状态发生改变时，又需要改变UI；

**他们之间是密不可分，所以React没有讲标记分离到不同的文件中，而是将它们组合到了一起，这个地方就是组件 （Component）；**

1. 当然，后面我们还是会继续学习更多组件相关的东西；

在这里，我们只需要知道，JSX其实是嵌入到JavaScript中的一种结构语法；



### JSX的书写规范：

- JSX的顶层只能有一个根元素，所以我们很多时候会在外层包裹一个div原生（或者使用后面我们学习的Fragment）； 
- 为了方便阅读，我们通常在jsx的外层包裹一个小括号()，这样可以方便阅读，并且jsx可以进行换行书写； 
- JSX中的标签可以是**单标签**，也可以是**双标签**；
  - 注意：如果是单标签，**必须以/>结尾；**



## jsx的使用

**在react的初始阶段，我是在html中直接引入cdn的方式，来学习react的jsx的语法。**

在书写jsx之前需要引入三个文件：

```jsx
  <script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
  <script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
```

而且。书写jsx的时候，也需要在外层的srcipt标签上的type属性值设置为 **text/babel**

```jsx
<script type="text/babel">
/ ...
</script>
```





### jsx的注释如何书写

**jsx的注释看起来的确是有点怪，是让人不习惯的方式。不同于js的注释。**

```jsx
{/* 这是jsx的注释写法 的确是有点怪 不习惯  */}
```



### jsx嵌入变量

#### 一：当变量是Number、String、Array类型时，直接显示

如果是数组类型，会把数组的元素全部取出，然后做字符串的拼接，最后显示到页面。

![image-20220117172213279](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220117172213279.png)







#### 二：当变量是null、undefined、Boolean类型时，内容为空

- 如果希望可以显示null、undefined、Boolean，那么需要转成字符串； 
- 转换的方式有很多，比如toString方法、和空字符串拼接，String(变量)等方式；

![image-20220117172323066](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220117172323066.png)



**为什么react不让布尔类型，null或者undefined这些值在使用{表达式} 的时候，不进行显示到页面上？**

想一想，这玩意渲染到页面上不会显得奇怪吗？是吧，我想显示一个变量，但是这个变量没有给初始值，那么这个值就是undefined，既然是undefined，为什么还要渲染到页面上呢？很明显开发人员也没想让这个undefined的值渲染到页面。





#### 三：对象类型不能作为子元素（not valid as a React child）

直接报错。（react就是这么规定的）

![image-20220117172450666](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220117172450666.png)







### JSX嵌入表达式

- 运算表达式 
- 三元运算符 
- 执行一个函数

![image-20220117173507839](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220117173507839.png)





### JSX绑定属性

#### 常见属性(src,href...)

![image-20220118151858353](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118151858353.png)



#### class属性

*绑定 class属性 在ES6以后，class变成了关键字，所以我们给元素绑定class的时候，需要写成 className*

![image-20220118152110722](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118152110722.png)

如果绑定class的时候，我们坚持使用class的话，react会报警告。

![image-20220118152802035](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118152802035.png)





#### style内联样式

绑定style可以直接传一个对象。**键是样式名(采用小驼峰)，值是样式值(不是变量的话需要加双引号)**

```jsx
{/* 3.绑定 style属性 可以直接传一个对象，键是样式名(采用小驼峰)，值是样式值(不是变量的话需要加双引号) */}
<h3 style={{ color: "red", backgroundColor: "yellow" }}>我是style</h3>
```

![image-20220118153503209](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118153503209.png)





### 事件绑定

如果原生DOM原生有一个监听事件，我们可以如何操作呢？

- 方式一：获取DOM原生，添加监听事件； 
- 方式二：在HTML原生中，直接绑定onclick；

**在React中是如何操作呢？**

- 我们来实现一下React中的事件监听，这里主要有两点不同 
  - React 事件的命名采用小驼峰式（camelCase），而不是纯小写； 
- 我们需要通过**{}**传入一个事件处理函数，这个函数会在事件发生时被执行；

**注意：在react绑定事件的时候，容易出现this的指向问题。**很多人不是很清楚this的绑定规则，如果不是很清晰，可以去看看这一篇博文：[js令人费解的this](https://blog.csdn.net/weixin_45747310/article/details/121328086?spm=1001.2014.3001.5501)



#### this的绑定问题

在事件执行后，我们可能需要获取当前类的对象中相关的属性，这个时候需要用到this，如果我们这里直接打印this，也会发现它是一个undefined。

**为什么是undefined呢？**

- 原因是btnClick函数并不是我们主动调用的，而且当button发生改变时，React内部调用了btnClick函数； 
- 而它内部调用时，并不知道要如何绑定正确的this；

**如何解决this的问题呢？**

- 方案一：bind给btnClick显示绑定this 
- 方案二：使用 ES6 class fields 语法 
- 方案三：事件监听时传入箭头函数（推荐）

![image-20220118161757375](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118161757375.png)



#### bind显示绑定this

我觉得这个方式不是很好，如果在多个地方都使用了这同一个函数，会影响性能。毕竟这个函数会被拷贝出多个。

```jsx
{/* 显示绑定 */}
<button onClick={this.btnClick.bind(this)}>按钮2</button><br/>
```

![image-20220118162145384](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118162145384.png)

但是，我们也可以在初始化这个组件的时候，在构造函数(constructor)内给使用的函数绑定好this。这样就不会出现this绑定不明确的问题

```jsx
constructor() {
    super();
    this.state = {}
    this.btnClick = this.btnClick.bind(this)
}
btnClick(){
    console.log(this);
}
```



#### class fields 语法绑定this

这种绑定方式会直接绑定到class类中的this。因为箭头函数是不会绑定this的，它只会使用父级作用域中的this。

```jsx
btnClick = () => {
    console.log(this);
}
```

![image-20220118162414895](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118162414895.png)



#### 事件监听时传入箭头函数（推荐）

```jsx
{/* 箭头函数 */}
<button onClick={()=>{this.btnClick()}}>按钮3</button><br/>
```

使用这种方式呢，可以在箭头函数内还写一些其他的代码逻辑。而且是直接调用绑定的函数(其实是this的隐式绑定了)。而且，在调用this.btnClick函数的时候，我们还可以传入相关操作， 更加灵活！

![image-20220118162701116](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118162701116.png)





### 事件参数传递

在执行事件函数时，有可能我们需要获取一些参数信息：比如event对象、其他参数

- 情况一：获取event对象 p 很多时候我们需要拿到event对象来做一些事情（比如阻止默认行为） 

  - 假如我们用不到this，那么直接传入函数就可以获取到event对象；

  ```jsx
  class App extends React.Component {
      constructor() {
          super();
      }
      btnClick(e){
          console.log(e);
      }
      render() {
          return (
              <div>
                  <button onClick={this.btnClick.bind(this)}>按钮1</button>
              </div>
          );
      }
  }
  ReactDOM.render(<App />, document.querySelector("#app"));
  ```

  采用这种方式进行绑定事件函数，在触发事件后，react内部会自动给我们传递一个event对象(是react封装过的，不是原生event对象)。

  ![image-20220118165812742](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118165812742.png)

  

- 情况二：获取更多参数 

  - 有更多参数时，我们最好的方式就是传入一个箭头函数，主动执行的事件函数，并且传入相关的其他参数；

  上面那种绑定事件的方式很明显是有弊端的，弊端就在于我们只是方便拿到event对象，如果处理函数中需要接收其他的参数，无疑对我们来说，是很麻烦的。那么这个时候，我们就推荐上面绑定函数的第三种方式，既可以方便的拿到event，也可以在执行的过程中，接收其他的参数。

  ```jsx
  class App extends React.Component {
      constructor() {
          super();
          this.state = {
              movieList: ["火影忍者", "海贼王", "名侦探柯南"]
          }
      }
      btnClick(e) {
          console.log(e);
      }
      liClick(value,index,e){
          console.log(value,index,e);
      }
      render() {
          return (
              <div>
                  <button onClick={this.btnClick.bind(this)}>按钮1</button>
                  <ul>
                      {
                          this.state.movieList.map((value, index) => {
                              return <li onClick={e => { this.liClick(value, index, e) }}>{value}</li>
                          })
                      }
                  </ul>
              </div>
          );
      }
  }
  ReactDOM.render(<App />, document.querySelector("#app"));
  ```

  这里会报一个没有唯一key的警告，暂时不需要管。

  ![image-20220118170355447](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118170355447.png)



### 条件渲染

某些情况下，界面的内容会根据不同的情况显示不同的内容，或者决定是否渲染某部分内容：

- 在vue中，我们会通过指令来控制：比如v-if、v-show； 
- 在React中，所有的条件判断都和普通的JavaScript代码一致；

**常见的条件渲染的方式有哪些呢？**

- 方式一：条件判断语句
  - 适合逻辑较多的情况
- 方式二：三元运算符 
  - 适合逻辑比较简单
- 与运算符&& 
  - 适合如果条件成立，渲染某一个组件；如果条件不成立，什么内容也不渲染；

- v-show的效果
  - 主要是控制display属性是否为none

```jsx
class App extends React.Component {
    constructor() {
        super();
        this.state = {
            flag1: true,
            flag2: true,
            flag3: true,
            flag4: true,
        }
    }
    render() {
        const { flag1, flag2, flag3, flag4 } = this.state;
        // 方式一：
        let f1 = undefined;
        if (flag1) f1 = <h2>你好啊，好兄弟！</h2>;
        else f1 = <h2>我不好！</h2>;
        // 方式二：三元运算符
        return (
            <div>
                {f1}
                {flag2 ? <h2>哈哈哈</h2> : ""}
                {flag3 && <h2>你好   11</h2>}
                <h2 style={ {display: flag4 ? "block": "none"} }>你好</h2>
            </div>
        );
    }
}
    ReactDOM.render(<App />, document.querySelector("#app"));
```

![image-20220118185652581](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118185652581.png)





### 列表渲染

真实开发中我们会从服务器请求到大量的数据，数据会以列表的形式存储：

- p 比如歌曲、歌手、排行榜列表的数据； 
- 比如商品、购物车、评论列表的数据； 
- 比如好友消息、动态、联系人列表的数据；

**如何展示列表数据呢？**

在React中，展示列表最多的方式就是使用数组的**map/filter高阶函数**；

很多时候我们在展示一个数组中的数据之前，需要先对它进行一些处理：

- 比如过滤掉一些内容：filter函数 

- 比如截取数组中的一部分内容：slice函数



```jsx
class App extends React.Component {
    constructor() {
        super();
        this.state = {}
    }
    render() {
        const list1 = ["你好", "你好啊", "我也好"];
        const list2 = [40, 55, 22, 33, 88, 99];
        return (
            <div>
                {/*可以直接展示数组,相当于直接取出数组的所有元素，放到标签内*/}
                <h2>{list1}</h2>
                <hr />
                <ul>
                    {list1.map(value => <li>{value}</li>)}
                </ul>
                <hr />
                {/*展示大于55的数字*/}
                <ol>
                    {
                        list2.filter(value => value >= 55).map(value => <li>{value}</li>)
                                                               }
                </ol>
            </div>
        );
    }
}
ReactDOM.render(<App />, document.querySelector("#app"));
```

![image-20220118190627038](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118190627038.png)



![image-20220118190550758](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118190550758.png)



### 列表中的key

我们会发现在前面的代码中只要展示列表都会报一个警告：

![image-20220118190702551](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118190702551.png)

这个警告是告诉我们需要在列表展示的jsx中添加一个key。

至于如何添加一个key，为什么要添加一个key，这个我们放到后面学习setState时再来讨论

![image-20220118190840101](https://gitee.com/maolovecoding/picture/raw/master/images/web/react/image-20220118190840101.png)

