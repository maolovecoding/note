# Vuex的状态管理

## 什么是状态管理

在开发中，我们会的应用程序需要处理各种各样的数据，这些 数据需要保存在我们应用程序中的某一个位置，对于这些数据 的管理我们就称之为是 **状态管理。**

在前面我们是如何管理自己的状态呢？

- 在Vue开发中，我们使用组件化的开发方式； 
- 而在组件中我们定义data或者在setup中返回使用的数据， 这些数据我们称之为state； 
- 在模块template中我们可以使用这些数据，模块最终会被 渲染成DOM，我们称之为View； 
- 在模块中我们会产生一些行为事件，处理这些行为事件时， 有可能会修改state，这些行为事件我们称之为actions；

![image-20210903172421127](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210903172421127.png)

## 复杂的状态管理

JavaScript开发的应用程序，已经变得越来越复杂了：

- JavaScript需要管理的状态越来越多，越来越复杂； 
- 这些状态包括服务器返回的数据、缓存数据、用户操作产生的数据等等； 
- 也包括一些UI的状态，比如某些元素是否被选中，是否显示加载动效，当前分页；

当我们的应用遇到多个组件共享状态时，单向数据流的简洁性很容易被破坏：

- 多个视图依赖于同一状态； 
- 来自不同视图的行为需要变更同一状态；

我们是否可以通过组件数据的传递来完成呢？

- 对于一些简单的状态，确实可以通过props的传递或者Provide的方式来共享状态； 
- 但是对于复杂的状态管理来说，显然单纯通过传递和共享的方式是不足以解决问题的，比如**兄弟组件如何共享 数据呢**？

## Vuex的状态管理

- 管理不断变化的state本身是非常困难的：
  - 状态之间相互会存在依赖，一个状态的变化会引起另一个状态的变化，View页面也有可能会引起状态的变化； 
  - 当应用程序复杂时，state在什么时候，因为什么原因而发生了变化，发生了怎么样的变化，会变得非常难以控 制和追踪；

- 因此，我们是否可以考虑将组件的内部状态抽离出来，以一个全局单例的方式来管理呢？
  - 在这种模式下，我们的组件树构成了一个巨大的 “试图View”； 
  - 不管在树的哪个位置，任何组件都能获取状态或者触发行为； 
  - 通过定义和隔离状态管理中的各个概念，并通过强制性的规则来维护视图和状态间的独立性，我们的代码边会 变得更加结构化和易于维护、跟踪；

- 这就是Vuex背后的基本思想，它借鉴了Flux、Redux、Elm（纯函数语言，redux有借鉴它的思想）：



![image-20210903172709234](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210903172709234.png)

### Vuex的安装

依然我们要使用vuex，首先第一步需要安装vuex：

我们这里使用的是vuex4.x，安装的时候需要添加 next 指定版本；

```shell
npm i vuex@next
```

### Store

#### 创建Store

- 每一个Vuex应用的核心就是store（仓库）：
  - store本质上是一个容器，它包含着你的应用中大部分的状态（state）；

- **Vuex和单纯的全局对象有什么区别呢？**

  - 第一：Vuex的状态存储是响应式的
    - 当Vue组件从store中读取状态的时候，若store中的状态发生变化，那么相应的组件也会被更新；

  - 第二：你不能直接改变store中的状态
    - 改变store中的状态的唯一途径就显示提交 (commit) mutation； 
    - 这样使得我们可以方便的跟踪每一个状态的变化，从而让我们能够通过一些工具帮助我们更好的管理应用的状态；

- 使用步骤：
  - 创建Store对象； 
  - 在app中通过插件安装；



#### 组件中使用store

在组件中使用store，我们按照如下的方式：

- 在模板中使用； 
- 在options api中使用，比如computed； 
- 在setup中使用；

##### 简单的使用

###### store文件

```js
// vuex全局管理文件
import { createStore } from "vuex";

// 创建一个仓库
const store = createStore({
  state() {
    return {
      counter: 0
    }
  },
  // 在mutations里面定义操作我们vuex的state管理的数据的方法
  mutations: {
    // 每个方法都会默认传递state对象参数
    increment(state) {
      state.counter++;
    },
    decrement(state) {
      state.counter--;
    }
  }
});

// 导出
export default store;
```

###### store的注册

直接在main.js里面使用app.use()进行安装即可。

###### 简单计数器的实现

```vue
<template>
  <div>
    <!-- $store.state.counter 拿到仓库的数据 通过 $store.state对象进行获取 -->
    <h2>{{ $store.state.counter }}</h2>
    <button @click="increment">+1</button>
    <button @click="decrement">-1</button>
  </div>
</template>

<script>
export default {
  name: "App",
  methods: {
    increment() {
      // 使用仓库里面管理的counter数据，并进行操作
      // 官方是不建议我们直接操作vuex管理的数据的
      // this.$store.state.counter++;
      // 我们要修改数据，一般都是会进行commit
      this.$store.commit("increment");
    },
    decrement() {
      this.$store.commit("decrement");
    },
  },
};
</script>

<style scoped>
</style>

```

![image-20210903173548842](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210903173548842.png)



#### 单一状态树

**Vuex 使用单一状态树：**

- 用一个对象就包含了全部的应用层级状态； 
- 采用的是SSOT，Single Source of Truth，也可以翻译成单一数据源； 
- 这也意味着，每个应用将仅仅包含一个 store 实例； 
- 单状态树和模块化并不冲突，后面我们会讲到module的概念；

单一状态树的优势：

- 如果你的状态信息是保存到多个Store对象中的，那么之后的管理和维护等等都会变得特别困难； 
- 所以Vuex也使用了单一状态树来管理应用层级的全部状态； 
- 单一状态树能够让我们最直接的方式找到某个状态的片段，而且在之后的维护和调试过程中，也可以非常方便 的管理和维护；



#### 组件获取状态

在前面我们已经学习过如何在组件中获取状态了。

当然，如果觉得那种方式有点繁琐（表达式过长），我们可以使用计算属性：

![image-20210903202015859](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210903202015859.png)

也是可以正常显示的。



**但是：**如果我们有很多个状态都需要获取的话，可以使用mapState的辅助函数（是vuex插件提供给我们的）：

- mapState的方式一：对象类型； 
- mapState的方式二：数组类型； 
- 也可以使用展开运算符和来原有的computed混合在一起；

![image-20210903202136671](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210903202136671.png)

![image-20210903203542060](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210903203542060.png)

![image-20210903203604530](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210903203604530.png)

```vue
<template>
  <div>
    <h2>Home: {{ $store.state.counter }}</h2>
    <h2>{{ counter }}-{{ name }}-{{ age }}-{{ gender }}</h2>
    <h2>{{ hCounter }}-{{ hName }}-{{ hAge }}-{{ hGender }}</h2>
  </div>
</template>

<script>
import { mapState } from "vuex";
export default {
  name: "Home",
  computed: {
    // 从仓库state中获取到的数据
    /* 
    mapState() 参数： 1. 数组写法
      在数组里面写上我们想要映射过来的属性(就是要获取的数据变量名)
    该方法的返回值是一个对象，我们对对象进行展开，全都放到计算属性中
    TODO  注意：我们知道，计算属性的格式一般都是 fullName(){return xxx}
        既然我们的mapState返回值是对象，而且可以直接展开放到计算属性中，
        说明展开的每个属性对应的属性值其实也是函数，也是类似于我们原生的计算属性的。
        所以说，返回值其实不是计算属性，是函数
    */
    ...mapState(["counter", "name", "age", "gender"]),
    /* 
    参数二：对象写法，我们可以自定义属性的名称
    属性名：对应的值是一个函数的返回值
    */
    ...mapState({
      // state参数会映射为我们在store里面的state
      hCounter: (state) => state.counter,
      hName: (state) => state.name,
      hAge: (state) => state.age,
      hGender: (state) => state.gender
    }),
  },
};
</script>

<style scoped>
</style>
```





#### 在setup中使用mapState

在setup中如果我们单个获取装是非常简单的：

- 通过useStore拿到store后去获取某个状态即可；

  ![image-20210903205459653](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210903205459653.png)

- 但是如果我们需要使用 mapState 的功能呢？

**默认情况下，Vuex并没有提供非常方便的使用mapState的方式，这里我们进行了一个函数的封装：**

![image-20210903213230243](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210903213230243.png)

```js
setup() {
    // 在 computed里面如何拿到 $store对象 ?
    // vuex提供了一个hook，使用useStore就可以拿到全局的store对象
    const store = useStore();
    const hcounter = computed(() => store.state.counter);
    // 使用 mapState拿到数据
    /* 
      TODO  本质上 其实mapState函数取属性的时候，还是通过 this.$store.state.属性
      来获取我们需要的属性值 
      所以：我们的属性对应的那个函数的返回值会调用 $store.state 属性
      那么，我们需要给这个函数传递一个对象，因为setup函数里面是没有this的
      既然没有this，那么肯定就没有 this.$store 对象
      所以说，我们需要传递给这个函数 $store 对象
      这个对象就是前面我们通过 useStore函数拿到的返回值
    */
    const storeStateFns = mapState(["counter", "name", "age", "gender"]);
    // 用来保存我们生成的计算属性
    const state = {};
    Object.keys(storeStateFns).forEach((fnKey) => {
      const fn = storeStateFns[fnKey].bind({ $store: store });
      state[fnKey] = computed(fn);
    });

    return { hcounter, ...state };
  }
```

##### useState自封装函数

上面那种在setup函数里面写一大坨代码不是我们想要的效果。所以我们进行简单的函数封装，使看起来更舒服。

```js
import { useStore, mapState } from "vuex"
import { computed } from "vue"
/**
 * 封装一个拿到指定属性的函数，返回值是计算属性的对象
 *
 * @export
 * @param {*} mapper 对象或者数组 {} || [] 两种写法都支持
 * @return {*} 返回值就是封装好的对象，对象的属性是计算属性对象
 */
export default function (mapper) {
  // 拿到 $store 对象
  const $store = useStore();
  // 获取到对应的对象的所有函数
  /* 
  functions:{
    name:function(){},
    age:function(){},
    ...
  }
  */
  const storeStateFns = mapState(mapper);
  // 对数据进行转换
  const storeState = {};
  Object.keys(storeStateFns).forEach(fnKey => {
    // 绑定我们的对象，并在函数上赋值我们的 $store属性
    // 返回的函数就是一个能拿到 $store 属性的函数了
    const fn = storeStateFns[fnKey].bind({ $store });
    // 属性转为计算属性，并保存到返回的对象上
    storeState[fnKey] = computed(fn);
  })
  return storeState;
}
```

**这样就舒服多了**

![image-20210903221635835](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210903221635835.png)





### getters

#### getters的基本使用

某些属性我们可能需要经过变化后来使用，这个时候可以使用getters：

![image-20210904082048773](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904082048773.png)



#### getters第二个参数

getters可以接收第二个参数：

![image-20210904082200680](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904082200680.png)

![image-20210904082259062](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904082259062.png)



#### getters的返回函数

getters中的函数本身，可以返回一个函数，那么在使用的地方相当于可以调用这个函数：

调用函数的同时也可以传递参数。

![image-20210904082506302](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904082506302.png)



#### mapGetters的辅助函数

这里我们也可以使用mapGetters的辅助函数。

`mapGetters` 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：

![image-20210904083326466](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904083326466.png)



在setup中使用

![image-20210904084849673](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904084849673.png)





### Mutation

#### Mutation基本使用

更改 Vuex 的 store 中的状态的唯一方法是提交 mutation：Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的**事件类型 (type)\**和一个\**回调函数 (handler)**。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

![image-20210904085430225](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904085430225.png)

![image-20210904085849392](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904085849392.png)



#### Mutation携带数据

很多时候我们在提交mutation的时候，会携带一些数据，这个时候我们可以使用参数：

![image-20210904090441870](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904090441870.png)



**payload为对象类型**

![image-20210904090801951](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904090801951.png)

**对象风格的提交方式**

和上面的payload对象类型的方式提交是等价的。

![image-20210904091003783](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904091003783.png)



#### Mutation常量类型

**定义常量：mutation-type.js**

```js
// 将mutations的那些定义的函数名，也就是一个个的mutation的名称定义为常量
// 方便外界调用，不会出错
export const INCREMENT_N = "incrementN";
```

**定义mutation**

```js
// 使用定义好的常量作为函数名
[INCREMENT_N](state, payload) {
    state.counter += payload.num;
}
```



**提交mutation**

```js
this.$store.commit({
    // 提交的mutation的类型直接都写在对象上
    // type就是我们的mutation类型
    type: INCREMENT_N,
    name: "毛毛",
    num: 10,
});
```





#### mapMutations辅助函数

我们也可以借助于辅助函数，帮助我们快速映射到对应的方法中：

```vue
<template>
  <div>
    <h2>{{ $store.state.counter }}</h2>
    <!-- 提交 mutation 修改state中的数据-->
    <button @click="increment">+</button>
    <button @click="myDecrement">-</button>
    <button @click="incrementN({num:10})">+10</button>
  </div>
</template>

<script>
import { mapMutations } from "vuex";
import { INCREMENT_N } from "../store/mutation-types";
export default {
  name: "Home",
  methods: {
    // 参数为数组类型
    ...mapMutations([
      "increment", // 将 `this.increment()` 映射为 `this.$store.commit('increment')`
      // 提交带参数，直接在调用的时候传递参数即可
      INCREMENT_N,// 将 `this[INCREMENT_N](amount)` 映射为 `this.$store.commit(INCREMENT_N, amount)`
    ]),
    // 参数为对象类型，可以方便起别名
    ...mapMutations({
      myDecrement:"decrement" // 将 `this.myDecrement()` 映射为 `this.$store.commit('decrement')`
    })
  },
};
</script>

<style scoped>
</style>
```

![image-20210904095152100](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904095152100.png)

**在setup中使用也是一样的：**

mapMutations返回的对象里面的属性值就刚好是函数，不需要再次封装，直接解构即可使用。

![image-20210904095629038](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210904095629038.png)



#### mutation重要原则

一条重要的原则就是要记住 **mutation 必须是同步函数**

- 这是因为devtool工具会记录mutation的日记； 
- 每一条mutation被记录，devtools都需要捕捉到前一状态和后一状态的快照； 
- 但是在mutation中执行异步操作，就无法追踪到数据的变化； 
- 所以Vuex的重要原则中要求 mutation必须是同步函数；



### Actions

#### actions的基本使用

Action类似于mutation，不同在于：

- Action提交的是mutation，而不是直接变更状态；

- Action可以包含任意异步操作；



这里有一个非常重要的参数context：

- context是一个和store实例均有相同方法和属性的context对象； 
- 所以我们可以从其中获取到commit方法来提交一个mutation，或者通过 context.state 和 context.getters 来 获取 state 和 getters； 
- 但是为什么它不是store对象呢？这个等到我们讲Modules时再具体来说；

![image-20210905094951404](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905094951404.png)



#### actions的分发操作

如何使用action呢？进行action的分发：

- 分发使用的是 store 上的dispatch函数；

![image-20210905101505909](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905101505909.png)

- 同样的，它也可以携带我们的参数：

```js
incrementN() {
    // 提交antion
    this.$store.dispatch("incrementNAction", { num: 10 });
}
```

```js
actions:{
    // payload 就是页面提交action的时候，传递的参数
    incrementNAction(context, payload) {
      // 执行异步操作 可以发起网络请求等
      setTimeout(() => {
        context.commit("incrementN", payload);
      }, 1000);
    }
}
```

- 也可以以对象的形式进行分发：

![image-20210905101710585](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905101710585.png)



#### actions的辅助函数mapActions

##### methods中使用

**action也有对应的辅助函数：**

- 数组类型的写法；

![image-20210905102659204](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905102659204.png)



- 对象类型的写法

![image-20210905102937069](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905102937069.png)



##### 在setup函数中使用

![image-20210905103228015](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905103228015.png)





#### actions的异步操作

 Action 通常是异步的，那么如何知道 action 什么时候结束呢？

- 我们可以通过让action返回Promise，在Promise的then中来处理完成后的操作；

![image-20210905104736067](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905104736067.png)



### module

#### module的基本使用

**什么是Module？**

- 由于使用单一状态树，应用的所有状态会集中到一个比较大的对象，当应用变得非常复杂时，store 对象就有可 能变得相当臃肿； 
- 为了解决以上问题，Vuex 允许我们将 store 分割成**模块（module）；** 
- 每个模块拥有自己的 state、mutation、action、getter、甚至是**嵌套子模块；**

![image-20210905112855452](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905112855452.png)

![image-20210905112914375](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905112914375.png)



#### module的局部状态

对于模块内部的 mutation 和 getter，接收的第一个参数是模块的局部状态对象：

在homeModule里面注册的mutation，对increment进行提交，可以发现，全局的rootCounter和homeCounter都进行了+1.因为我们在homeModule和全局的mutation中都注册了increment这个mutation提交事件。

![image-20210905114631515](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905114631515.png)

![image-20210905114518377](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905114518377.png)

只要mutation中有叫做increment的这个事件，对应的函数全都会被执行一次。

如果我们想执行（提交）的只是homeModule里面的mutation呢？

**我们在module中定义的getters，mutations，以及actions都是这样的问题。**

我们进行提交，或者获取getters里面的数据，以及派发actions，都是和直接在全局的store里面定义都没有什么区别。

![image-20210905115847241](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905115847241.png)

**我们如果想进行区分，要如何做呢？**





#### module的命名空间

默认情况下，模块内部的action和mutation仍然是**注册在全局的命名空间中**的：

- 这样使得多个模块能够对同一个 action 或 mutation 作出响应； 
- Getter 同样也默认注册在全局命名空间；

如果我们希望模块具有更高的封装度和复用性，可以添加 **namespaced: true** 的方式使其成为带命名空间的模块：

当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名；

![image-20210905121001586](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905121001586.png)



#### module修改或派发根组件

如果我们希望在action中修改root中的state，那么有如下的方式：

![image-20210905131830019](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905131830019.png)



#### module的辅助函数

**辅助函数有三种使用方法：**

- 方式一：通过完整的模块空间名称来查找； 
- 方式二：第一个参数传入模块空间名称，后面写上要使用的属性； 
- 方式三：通过 createNamespacedHelpers 生成一个模块的辅助函数；

##### 方式一

```html
<template>
  <div>
    <h2>{{ homeCounter }}</h2>
    <h2>{{doubleHomeCounter}}</h2>
    <!-- 需要使用 getters["注册的模块名称/getters里面注册的getter名称"] -->
    <h2>homeGetters: {{ $store.getters["home/doubleHomeCounter"] }}</h2>
    <button @click="homeIncrement">homeCounter+1</button>
    <button @click="homeIncrementAction">homeCounter+1</button>
  </div>
</template>

<script>
import { mapState, mapGetters, mapMutations, mapActions } from "vuex";

export default {
  name: "Home",
  computed: {
    // 带命名空间的模块数据取出来比较麻烦
    // 取出带命名空间的state里面的数据，只支持对象的写法
    // 而且返回值 是 state.模块名.数据变量
    ...mapState({
      homeCounter: (state) => state.home.homeCounter,
    }),
    ...mapGetters({
      // 也是需要带模块名，后面跟数据名
      doubleHomeCounter:"home/doubleHomeCounter"
    }),
  },
  methods: {
    ...mapMutations({
      homeIncrement:"home/increment"
    }),
    ...mapActions({
      homeIncrementAction:"home/incrementAction"
    }),
  },
};
</script>
```





##### 方式二

```html
<template>
  <div>
    <h2>{{ homeCounter }}</h2>
    <h2>{{doubleHomeCounter}}</h2>
    <!-- 需要使用 getters["注册的模块名称/getters里面注册的getter名称"] -->
    <h2>homeGetters: {{ $store.getters["home/doubleHomeCounter"] }}</h2>
    <button @click="increment">homeCounter+1</button>
    <button @click="incrementAction">homeCounter+1</button>
  </div>
</template>

<script>
import { mapState, mapGetters, mapMutations, mapActions } from "vuex";

export default {
  name: "Home",
  computed: {
    // 方式二：
    // 第一个参数是我们的模块名称
    // 第二个参数是我们这个模块下要获取的数据
    ...mapState("home",["homeCounter"]),
    ...mapGetters({
      // 也是需要带模块名，后面跟数据名
      doubleHomeCounter:"home/doubleHomeCounter"
    }),
  },
  methods: {
    ...mapMutations("home",["increment"]),
    ...mapActions("home",["incrementAction"]),
  },
};
</script>
```





##### 方式三

```html
<template>
  <div>
    <h2>{{ homeCounter }}</h2>
    <h2>{{ doubleHomeCounter }}</h2>
    <!-- 需要使用 getters["注册的模块名称/getters里面注册的getter名称"] -->
    <h2>homeGetters: {{ $store.getters["home/doubleHomeCounter"] }}</h2>
    <button @click="increment">homeCounter+1</button>
    <button @click="incrementAction">homeCounter+1</button>
  </div>
</template>

<script>
import { createNamespacedHelpers } from "vuex";
// 方式三
// 使用 createNamespacedHelpers 创建基于某个命名空间辅助函数
// 它返回一个对象，对象里有新的绑定在给定命名空间值上的组件绑定辅助函数：
const { mapState, mapGetters, mapMutations, mapActions } =
  createNamespacedHelpers("home");

export default {
  name: "Home",
  computed: {
    // 这里也是必须使用对象类型进行获取的
    ...mapState({
      homecounter:state=>state.homecounter
    }),
    ...mapGetters({
      // 也是需要带模块名，后面跟数据名
      doubleHomeCounter: "doubleHomeCounter",
    }),
  },
  methods: {
    ...mapMutations(["increment"]),
    ...mapActions(["incrementAction"]),
  },
};
</script>

<style scoped>
</style>
```



### nexttick

官方解释：将回调推迟到下一个 DOM 更新周期之后执行。在更改了一些数据以等待 DOM 更新后立即使用它。

**比如我们有下面的需求：**

- 点击一个按钮，我们会修改在h2中显示的message； 
- message被修改后，获取h2的高度；

实现上面的案例我们有三种方式：

- 方式一：在点击按钮后立即获取到h2的高度（错误的做法） 。**这种做法每次获取到的高度都是上次的高度。**

  ![image-20210905164631027](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905164631027.png)

- 方式二：在updated生命周期函数中获取h2的高度（但是其他数据更新，也会执行该操作） 

  ![image-20210905164917043](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905164917043.png)

- 方式三：使用nexttick函数；

nexttick是如何做到的呢？

![image-20210905172202592](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905172202592.png)



### historyApiFallback

-  historyApiFallback是开发中一个非常常见的属性，它主要的作用是解决SPA页面在路由跳转之后，进行页面刷新 时，返回404的错误。

- boolean值：默认是false
  - 如果设置为true，那么在刷新时，返回404错误时，会自动返回 index.html 的内容；

- object类型的值，可以配置rewrites属性：
  - 可以配置from来匹配路径，决定要跳转到哪一个页面；

- 事实上devServer中实现historyApiFallback功能是通过connect-history-api-fallback库的：

- 可以查看[connect-history-api-fallback](https://github.com/bripkens/connect-history-api-fallback) 文档

![image-20210905172411233](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210905172411233.png)
