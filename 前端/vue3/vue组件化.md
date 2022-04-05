# Vue3组件化(一)-父子组件通信

## 组件的嵌套

### 认识组件的嵌套

**前面我们是将所有的逻辑放到一个App.vue中：**

1. 在之前的案例中，我们只是创建了一个组件App； 
2. 如果我们一个应用程序将所有的逻辑都放在一个组件中，那么这个组件就会变成非 常的臃肿和难以维护； 
3. 所以组件化的核心思想应该是对组件进行拆分，拆分成一个个小的组件； 
4. 再将这些组件组合嵌套在一起，最终形成我们的应用程序；

我们来分析一下下面代码的嵌套逻辑，假如我们将所有的代码逻辑都放到一个App.vue 组件中：

![image-20210807201258559](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210807201258559.png)

1. 我们会发现，将所有的代码逻辑全部放到一个组件中，代码是非常的臃肿和难以维 护的。 
2. 并且在真实开发中，我们会有更多的内容和代码逻辑，对于扩展性和可维护性来说 都是非常差的。 
3. 所以，在真实的开发中，我们会对组件进行拆分，拆分成一个个功能的小组件。

### 组件的拆分

**我们可以按照如下的方式进行拆分：**

![image-20210807201350134](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210807201350134.png)

按照如上的拆分方式后，我们开发对应的逻辑只需要去对应的组件编写就可。

```vue
App.vue文件

<template lang="html">
  <!-- 使用局部组件 -->
  <div id="app">
    <Header></Header>
    <!-- 如果组件不需要填充数据或标签等，可以直接使用单标签 -->
    <Main/>
    <Footer/>
  </div>
</template>
<script>
// 导入vue组件
import Header from "./Header";
import Main from "./Main";
import Footer from "./Footer";
export default {
  data() {
    return {};
  },
  // 注册局部组件
  components: {
    Header,
    Main,
    Footer,
  },
};
</script>
<style scoped>
</style>
```

```vue
Header.vue

<template lang="">
  <div id="header">
    <h2>哈哈哈</h2>
    <h2>header</h2>
  </div>
</template>
<script>
export default {}
</script>
<style scoped>
</style>
```

```vue
Main.vue


<template>
  <div id="main">
    <h2>haha</h2>
    <h3>main</h3>
    <main-banner></main-banner>
  </div>
</template>

<script>
import MainBanner from "./MainBanner.vue";
export default {
  components: { MainBanner },
};
</script>

<style scoped>
</style>
```

```vue
MainBanner.vue

<template>
  <ul>
      <li>商品信息1</li>
      <li>商品信息2</li>
      <li>商品信息3</li>
      <li>商品信息4</li>
      <li>商品信息5</li>
    </ul>
</template>

<script>
  export default {
    
  }
</script>

<style scoped>

</style>
```

```vue
Footer.vue


<template>
  <div id="footer">
    <h2>main</h2>
    <h2>hahah</h2>
  </div>
</template>

<script>
export default {

}
</script>

<style scoped>
</style>
```

![image-20210807202015394](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210807202015394.png)



## 组件的通信

### 组件的通信

**上面的嵌套逻辑如下，它们存在如下关系：**

1. App组件是Header、Main、Footer组件的父组件； 
2. Main组件是Banner、ProductList组件的父组件；

在开发过程中，我们会经常遇到需要**组件之间相互进行通信：**

1. 比如App可能使用了多个Header，每个地方的Header展示的内容不同，那么我们就需要使用者传递给Header 一些数据，让其进行展示； 
2. 又比如我们在Main中一次性请求了Banner数据和ProductList数据，那么就需要传递给它们来进行展示； 
3. 也可能是子组件中发生了事件，需要由父组件来完成某些操作，那就需要子组件向父组件传递事件；

总之，在一个Vue项目中，组件之间的通信是非常重要的环节，所以接下来我们就具体学习一下组件之间是如何相 互之间传递数据的；



### 父子组件之间通信的方式

**父子组件之间如何进行通信呢？**

1. 父组件传递给子组件：通过**props**属性； 
2. 子组件传递给父组件：通过**$emit**触发事件；

![image-20210807201629967](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210807201629967.png)



### 父组件传递给子组件

在开发中很常见的就是父子组件之间通信，比如父组件有一些数据，需要子组件来进行展示：

这个时候我们可以通过**props**来完成组件之间的通信；

**什么是Props呢？**

1. Props是你可以在组件上注册一些自定义的**attribute；** 
2. 父组件给这些attribute赋值，子组件通过**attribute**的名称获取到对应的值；

**Props有两种常见的用法：**

1. 方式一：字符串数组，数组中的字符串就是attribute的名称； 
2. 方式二：对象类型，对象类型我们可以在指定attribute名称的同时，指定它需要传递的类型、是否是必须的、 默认值等等



#### Props的数组用法

![image-20210807202329042](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210807202329042.png)

![image-20210807202350113](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210807202350113.png)

![image-20210807202358342](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210807202358342.png)

#### Props的对象用法

数组用法中我们只能说明传入的**attribute**的名称，并不能对其进行任何形式的限制，接下来我们来看一下对象的 写法是如何让我们的**props**变得更加完善的。

**当使用对象语法的时候，我们可以对传入的内容限制更多：**

1. 比如指定传入的attribute的类型； 
2. 比如指定传入的attribute是否是必传的； 
3. 比如指定没有传入时，attribute的默认值；

![image-20210807202626613](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210807202626613.png)

#### 细节一：type类型

**那么type的类型都可以是哪些呢？**

1. String 
2. Number 
3. Boolean 
4. Array 
5. Object 
6. Date 
7. Function 
8. Symbol(ES6新增的基本类型)

#### 细节二：对象类型的其他写法

![image-20210807204244840](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210807204244840.png)

![image-20210807204255330](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210807204255330.png)

#### 细节三：Prop 的大小写命名

**Prop 的大小写命名(camelCase vs kebab-case)**

1. HTML 中的 attribute 名是大小写不敏感的，所以浏览器会把所有大写字符解释为小写字符； 
2. 这意味着当你使用 DOM 中的模板时，camelCase (驼峰命名法) 的 prop 名需要使用其等价的 kebab-case (短 横线分隔命名) 命名

![image-20210807205803322](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210807205803322.png)

#### 非Prop的Attribute

**什么是非Prop的Attribute呢？**

当我们传递给一个组件某个属性，但是该属性并没有定义对应的props或者emits时，就称之为 非Prop的 Attribute；

常见的包括class、style、id属性等；

**Attribute继承**

当组件有单个根节点时，非Prop的**Attribute**将自动添加到根节点的Attribute中：

![image-20210807210559784](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210807210559784.png)

#### 禁用Attribute继承和多根节点

如果我们不希望组件的根元素继承**attribute**，可以在组件中设置 **inheritAttrs: false：**

1. 禁用attribute继承的常见情况是需要将attribute应用于根元素之外的其他元素；

2. 我们可以通过 **$attrs**来访问所有的 非props的attribute；

![image-20210808084721740](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210808084721740.png)

![image-20210808084819837](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210808084819837.png)

****

**多个根节点的attribute**

多个根节点的**attribute**如果**没有显示的绑定**，那么**会报警告**，我们必须手动的指定要绑定到哪一个属性上：

![image-20210808085158545](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210808085158545.png)

**如果我们不继承父组件的attribute属性，设置了 `inheritAttrs:false`，那么则不会报警告。没有根节点会继承根元素的属性。**

![image-20210808085525363](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210808085525363.png)

### 子组件传递给父组件

#### 子组件传递给父组件

**什么情况下子组件需要传递内容到父组件呢**

1. 当子组件有一些事件发生的时候，比如在组件中发生了点击，父组件需要切换内容； 
2. 子组件有一些内容想要传递给父组件的时候；

**我们如何完成上面的操作呢？**

- 首先，我们需要在子组件中定义好在某些情况下触发的事件名称； 
- 其次，在父组件中以v-on的方式传入要监听的事件名称，并且绑定到对应的方法中； 
- 最后，在子组件中发生某个事件的时候，根据事件名称触发对应的事件；

#### 自定义事件的流程

我们封装一个CounterOperation.vue的组件：

内部其实是监听两个按钮的点击，点击之后通过 this.$emit的方式发出去事件；

![image-20210808094848820](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210808094848820.png)

![image-20210808095011935](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210808095011935.png)

![image-20210808095038389](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210808095038389.png)

#### 自定义事件的参数和验证

自定义事件的时候，我们也可以传递一些参数给父组件：

![image-20210808095143390](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210808095143390.png)

![image-20210808095210856](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210808095210856.png)

![image-20210808095219037](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210808095219037.png)

**在vue3当中，我们可以对传递的参数进行验证：**

![image-20210808095252388](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210808095252388.png)

![image-20210808095326833](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210808095326833.png)

```vue
App.vue

<template>
  <div>
    <h2>当前计数：{{ counter }}</h2>
    <!-- 监听子组件触发的事件 -->
    <!-- 子组件触发的是什么事件，我们就需要监听什么事件 -->
    <!-- 就如这里：子组件触发的是add和sub事件，那么我们监听的必然是add和sub事件 -->
    <!-- 但是我们监听到的事件用的处理函数是任意的，addOne，subOne是任意的 -->
    <counter-operation @add="addOne" @sub="subOne" @addN="addN"></counter-operation>
    <!-- 注意：监听子组件的事件时，我们绑定的事件处理函数不要带括号，不然无法接收到子组件传递出来的参数 -->
  </div>
</template>

<script>
import CounterOperation from './CounterOperation.vue'
export default {
  components: { CounterOperation },
  data() {
    return {
      counter: 0
    }
  },
  methods: {
    addOne() {
      this.counter++
    },
    subOne() {
      this.counter--
    },
    // 事件处理函数可以接收子组件触发事件时传递给父组件的参数
    // 在括号上写的形参就会直接被子组件传递的参数赋值
    // 我们可以直接使用，多个参数也是一样
    addN(num) {
      this.counter += num
    }
  },
}
</script>

<style scoped>
</style>
```

```vue
CounterOperation.vue

<template>
  <div>
    <button @click="increment">+</button>
    <button @click="decrement">-</button>

    <input type="text" v-model.number="num" />
    <br />
    <button @click="incrementN">+n</button>
  </div>
</template>

<script>
export default {
  // 子组件会触发的事件，写在 emits属性上
  // emits 可以是数组，也可以是对象，用的多的是数组
  // 这是Vue3的写法，需要触发的事件需要先注册
  // emits: ['add', 'sub', 'addN'],

  // emits 对象写法，可以进行参数的验证
  emits: {
    // null 表示不需要进行参数的验证
    add: null,
    sub: null,
    // 需要验证的话，触发的事件后面是一个验证参数的函数
    // 我们可以写成箭头函数
    // 返回 true 表示参数验证通过，fasle则验证失败，会报警告
    // 注意：验证失败也会把参数传递过去的！！！！
    addN: payload => {
      console.log(typeof payload);
      // 看看是不是number类型
      if ((typeof payload) === 'number') return true;
      return false;
    }
  },

  data() {
    return {
      num: 0
    }
  },
  methods: {
    increment() {
      // 触发我们注册的 add事件
      this.$emit('add')
    },
    decrement() {
      // 触发我们注册的 sub 事件
      this.$emit('sub')
    },
    incrementN() {
      // 触发addN事件，一次加N个数，所以需要我们把加的数也传递出去
      // 需要几个参数就传递几个参数
      this.$emit('addN', this.num)
    }
  },
}
</script>

<style scoped>
button {
  margin-right: 20px;
}
</style>
```



# Vue3组件化开发(二)-非父子组件通信及插槽的使用

## 非父子组件的通信

在开发中，我们构建了组件树之后，除了**父子组件之间的通信**之外，还会有非父**子组件之间的通信。**

**这里我们主要讲两种方式：**

1. **Provide/Inject；** 
2. **Mitt全局事件总线；**

### Provide和Inject

#### 概述

Provide/Inject用于**非父子组件之间共享数据**：

1. 比如有一些**深度嵌套的组件**，子组件想要获取父组件的部分内 容； 
2. 在这种情况下，如果我们仍然将props沿着组件链逐级传递下 去，就会非常的麻烦；

对于这种情况下，我们可以使用 **Provide 和 Inject** ：

1. 无论层级结构有多深，父组件都可以作为其所有子组件的依赖 提供者
2. 父组件有一个 **provide 选项**来提供数据； 
3. 子组件有一个 **inject 选项**来开始使用这些数据；

实际上，你可以将依赖注入看作是“long range props”，除了：

1. 父组件不需要知道哪些子组件使用它 provide 的 property 
2. 子组件不需要知道 inject 的 property 来自哪里

#### Provide和Inject基本使用

**我们开发一个这样的结构：**

![image-20210809213806778](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809213806778.png)

![image-20210809214129165](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809214129165.png)

![image-20210809215147771](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809215147771.png)

![image-20210809215151699](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809215151699.png)

![image-20210809215402748](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809215402748.png)

#### Provide和Inject函数的写法

如果Provide中提供的一些数据是来自data，那么我们可能会想要通过this来获取：

**这个时候会报错：**

这里给大家留一个思考题，我们的this使用的是哪里的this？

![image-20210809215637892](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809215637892.png)

![image-20210809215733865](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809215733865.png)



![image-20210809215653396](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809215653396.png)

##### 对象类型的写法

![image-20210809220311862](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809220311862.png)

![image-20210809220405128](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809220405128.png)

**我们发现往names里面添加元素，非子组件拿到的names的length也不会响应式的方式改变**

![image-20210809220611680](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809220611680.png)

![image-20210809220623869](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809220623869.png)





#### 处理响应式数据

我们先来验证一个结果：如果我们修改了this.names的内容，那么使用length的子组件会不会是响应式的？

我们会发现对应的**子组件中是没有反应的：**

这是因为当我们修改了names之后，之前在provide中引入的 **this.names.length** 本身并不是响应式的；

**那么怎么样可以让我们的数据变成响应式的呢？**

1. 非常的简单，我们可以使用**响应式的一些API**来完成这些功能，比如说computed函数； 
2. 当然，这个**computed是vue3**的新特性，在后面我会专门讲解，这里大家可以先直接使用一下；

**注意：我们在使用length的时候需要获取其中的value**

这是因为computed返回的是一个ref对象，需要取出其中的value来使用；

![image-20210809221124200](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809221124200.png)

![image-20210809221148276](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809221148276.png)

![image-20210809221256512](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210809221256512.png)

```vue
App.vue

<template>
  <div>
    <home></home>
    <!-- 测试数组长度发生改变，通过provide传递到子组件的数组长度是否发生改变 -->
    <button @click="names.push('张三')">添加names</button>
    <!-- 结论：不会发生改变 -->
    <ul>
      <li v-for="item in names" :key="item">{{ item }}</li>
    </ul>
  </div>
</template>

<script>
import Home from './Home.vue'
// 导入 vue 中的computed函数 （暂时使用，后面会讲）
import { computed } from 'vue';
export default {
  components: { Home },
  // 我们可以通过 provide属性 直接提供数据供任意层级的子类使用（比如孙子组件，曾孙组件）
  /* provide: {
    info: {
      name: '毛毛',
      age: 21,
      message: '好好学vue.js！闷声发大财！'
    },
    test: '哈哈，测试一下别名好不好使！',
    length:this.names.length
  }, */
  // 如果想要在provide中使用data中的数据，那也就是必须用到了this
  // 那么这时候我们需要把provide写成函数的形式，返回一个对象
  // 这样才能在使用this。函数绑定了当前组件的this
  provide() {
    return {
      info: {
        name: '毛毛',
        age: 21,
        message: '好好学vue.js！闷声发大财！'
      },
      test: '哈哈，测试一下别名好不好使！',
      // 获取 names数组的长度 这种获取的方式并不是响应式的
      // length: this.names.length,

      // 通过 computed函数 响应式的传递数据
      // computed函数的返回值是一个 ref对象
      // 我们真正的值在 ref对象的value属性上,所以子类组件取值的时候，需要通过value属性获取
      // 一旦我们监听的值发生了改变，就会重新计算
      length: computed(() => this.names.length)
    }
  },
  data() {
    return {
      names: ['哈哈', '哼哼', '呵呵']
    }
  },
}
</script>

<style  scoped>
</style>
```

```vue
Home.vue

<template>
  <div>
    <home-content></home-content>
  </div>
</template>

<script>
import HomeContent from './HomeContent.vue'
export default {
  components: { HomeContent },

}
</script>

<style  scoped>
</style>
```

```vue
HomeContent.vue

<template>
  <div>
    <h2>我是HomeContent.vue!</h2>
    <ul>
      <li v-for="value,key in info" :key="key">{{ key }}--->{{ value }}</li>
    </ul>
    <h3>{{ homeTest }}</h3>
    <!-- 提示 test 没有被定义 -->
    <!-- <h3>{{ test }}</h3> -->
    <!-- 发现父类（统称）传递数据的时候使用了computed函数的话，
      我们取值的时候通过该函数返回对象的value属性拿真正的值就是响应式的了
    -->
    <!-- <h3>{{ length }}</h3> -->
    <h3>{{ length.value }}</h3>
  </div>
</template>

<script>
export default {
  // provide提供数据的注入，需要什么属性对应的数据，就注入什么属性
  // inject: ['info']
  // 起别名： 别名：注册的属性。在组件中可以使用别名，不能使用父组件提供的原来的属性名了
  inject: {
    info: 'info',
    homeTest: 'test',
    length: 'length'
  }
}
</script>

<style scoped>
</style>
```





### 全局事件总线mitt库

**事件总线的函数可以自己写，本质上是使用了发布订阅的设计模式。**

Vue3从实例中移除了 **$on、$off 和 $once** 方法，所以我们如果希望继续使用全局事件总线，要通过第三方的库：

1. Vue3官方有推荐一些库，例如 **mitt** 或 **tiny-emitter；** 
2. 这里我们主要讲解一下mitt库的使用；

**首先，我们需要先安装这个库：**

```shell
npm install mitt
```

**其次，我们可以封装一个工具eventbus.js：**

```js
/* 事件总线的封装 */
// 导入第三方库，返回值是一个函数
import mitt from 'mitt';

const emitter = mitt();

export default emitter
```

#### 使用事件总线工具

**在项目中可以使用它们：**

1. 我们在HomeContent.vue中监听事件； 
2. 我们在Subling.vue中发射（触发）事件；

![image-20210810090811490](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810090811490.png)

![image-20210810090846400](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810090846400.png)

![image-20210810091003293](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810091003293.png)



#### Mitt的事件取消

**在某些情况下我们可能希望取消掉之前注册的函数监听：**

1. **取消所有的监听事件**

   ![image-20210810091115213](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810091115213.png)

   ![image-20210810091126907](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810091126907.png)

2. **取消某个事件的监听**

   ![image-20210810091717819](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810091717819.png)

![image-20210810091746955](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810091746955.png)



## 组件插槽Slot

### 认识插槽Slot

**在开发中，我们会经常封装一个个可复用的组件：**

1. 前面我们会通过props传递给组件一些数据，让组件来进行展示； 
2. 但是为了让这个组件具备更强的通用性，我们不能将组件中的内容限制为固定的div、span等等这些元素； 
3. 比如某种情况下我们使用组件，希望组件显示的是一个按钮，某种情况下我们使用组件希望显示的是一张图片； 
4. 我们应该让使用者可以决定某一块区域到底存放什么内容和元素；

**举个栗子：假如我们定制一个通用的导航组件 - NavBar**

1. 这个组件分成三块区域：左边-中间-右边，每块区域的内容是不固定； 
2. 左边区域可能显示一个菜单图标，也可能显示一个返回按钮，可能什么都不显示； 
3. 中间区域可能显示一个搜索框，也可能是一个列表，也可能是一个标题，等等； 
4. 右边可能是一个文字，也可能是一个图标，也可能什么都不显示；

![image-20210810091923684](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810091923684.png)

### 如何使用插槽slot？

**这个时候我们就可以来定义插槽slot：**

1. 插槽的使用过程其实是抽取共性、预留不同； 
2. 我们会将共同的元素、内容依然在组件内进行封装； 
3. 同时会将不同的元素使用slot作为占位，让外部决定到底显示什么样的元素；

**如何使用slot呢？**

1. Vue中将`<slot>`  元素作为承载分发内容的出口； 
2. 在封装组件中，使用特殊的元素`<slot>`就可以为封装组件开启一个插槽； 
3. 该插槽**插入什么内容**取决于父组件如何使用；

### 插槽的基本使用

#### 基本使用

我们一个组件MySlotCpn.vue：该组件中有一个插槽，我们可以在插槽中放入需要显示的内容；

我们在App.vue中使用它们：我们可以插入普通的内容、html元素、组件元素，都可以是可以的；

**预留的插槽我们不填充任何数据时：**

![image-20210810095927253](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810095927253.png)

**使用组件时往插槽插入数据：**

![image-20210810100208843](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810100208843.png)

**往组件插槽中填充自定义组件：**

![image-20210810100931706](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810100931706.png)

#### 插槽的默认内容

有时候我们希望在使用插槽时，如果没有插入对应的内容，那么我们需要显示一个**默认的内容**：

当然这个默认的内容只会在没有提供插入的内容时，才会显示；

![image-20210810101443748](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810101443748.png)



#### 多个插槽的效果

我们先测试一个知识点：如果一个组件中含有**多个插槽**，我们插入多个内容时是什么效果？

我们会发现默认情况下每个插槽都会获取到我们插入的内容来显示；

![image-20210810104919157](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810104919157.png)

#### 具名插槽的使用

事实上，我们希望达到的效果是插槽对应的显示，这个时候我们就可以使用 具名插槽：

1. 具名插槽顾名思义就是给插槽起一个名字，`<slot>` 元素有一个特殊的 **attribute：name；** 
2. 一个**不带 name** 的slot，会带有隐含的名字 default；

![image-20210810112506472](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810112506472.png)

#### 动态插槽名

**什么是动态插槽名呢？**

1. 目前我们使用的插槽名称都是固定的； 
2. 比如 v-slot:left、v-slot:center等等； 
3. 我们可以通过 v-slot:[dynamicSlotName]方式动态绑定一个名称；

![image-20210810113654563](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810113654563.png)

![image-20210810113842773](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810113842773.png)

![image-20210810113909486](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810113909486.png)



#### 具名插槽使用的时候缩写

**具名插槽使用的时候缩写：**

1. 跟 **v-on 和 v-bind** 一样，**v-slot** 也有缩写； 
2. 即把参数之前的所有内容 **(v-slot:)** 替换为字符 **#**；

![image-20210810130048326](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810130048326.png)



## 渲染作用域

**在Vue中有渲染作用域的概念：**

1. 父级模板里的所有内容都是在**父级作用域**中编译的； 
2. 子模板里的所有内容都是在**子作用域中编译**的；

如何理解这句话呢？我们来看一个案例：

1. 在我们的案例中ChildCpn自然是可以让问自己作用域中的title内容的； 
2. 但是在App中，是访问不了ChildCpn中的内容的，因为它们是跨作用域的访问；

![image-20210810131433831](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810131433831.png)

![image-20210810131437971](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810131437971.png)

### 认识作用域插槽

**但是有时候我们希望插槽可以访问到子组件中的内容是非常重要的：**

1. 当一个组件被用来渲染一个数组元素时，我们使用插槽，并且希望插槽中没有显示每项的内容； 
2. 这个Vue给我们提供了作用域插槽；

**我们来看下面的一个案例：**

1. 在App.vue中定义好数据 
2. 传递给ShowNames组件中 
3. ShowNames组件中遍历names数据 
4. 定义插槽的prop 
5. 通过v-slot:default的方式获取到slot的props 
6. 使用slotProps中的item和index

![image-20210810133254354](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810133254354.png)

![image-20210810133335479](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810133335479.png)

**发现我们传递的标签不同，最后的显示效果就不同，甚至我们还可以控制那些数据显示，那些不显示**

![image-20210810133425113](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810133425113.png)

#### 独占默认插槽的缩写

如果我们的插槽是默认插槽default，那么在使用的时候 v-slot:default="slotProps"可以简写为v-slot="slotProps"：

![image-20210810134528571](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810134528571.png)

![image-20210810134532181](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810134532181.png)

并且如果我们的插槽只有默认插槽时，组件的标签可以被当做插槽的模板来使用，这样，我们就可以将 **v-slot** 直 接用在组件上：

**这种写法是简写，可以不使用到template标签。但是有局限**

![image-20210810134623858](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810134623858.png)

#### 默认插槽和具名插槽混合

**但是，如果我们有默认插槽和具名插槽，那么按照完整的template来编写。**

![image-20210810134955626](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810134955626.png)

**只要出现多个插槽，请始终为所有的插槽使用完整的基于 `<template>语法`**

![image-20210810135256596](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210810135256596.png)



# Vue3组件化开发(三)-动态/异步组件-vue3生命周期



## 切换组件案例

**比如我们现在想要实现了一个功能：**

点击一个tab-bar，切换不同的组件显示；

![image-20210811211016025](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210811211016025.png)

**这个案例我们可以通过两种不同的实现思路来实现：**

1. 方式一：通过**v-if**来判断，显示不同的组件；
2. 方式二：**动态组件**的方式；
3. 方式三：**路由实现**（日后学到再说）

### v-if实现

我们可以先通过v-if来判断显示不同的组件，这个可以使用我们之前讲过的知识来实现：

![image-20210811211229950](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210811211229950.png)

![image-20210811211332131](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210811211332131.png)

```vue
<template>
  <div>
    <button
      :key="item"
      v-for="item in tabs"
      :class="{ active: currentTab === item }"
      @click="toggle(item)"
    >{{ item }}</button>

    <template v-if="currentTab === 'home'">
      <home></home>
    </template>
    <template v-if="currentTab === 'about'">
      <about></about>
    </template>
    <template v-if="currentTab === 'category'">
      <category></category>
    </template>
  </div>
</template>

<script>
import About from './page/About.vue';
import Category from './page/Category.vue';
import Home from './page/Home.vue';
export default {
  components: {
    Home, About, Category
  },
  data() {
    return {
      tabs: ['home', 'about', 'category'],
      currentTab: 'home'
    }
  },
  methods: {
    toggle(item) {
      this.currentTab = item;
    }
  },
}
</script>

<style scoped>
.active {
  background-color: #aca;
  color: aqua;
}
button {
  font-size: 20px;
}
</style>
```

**效果**

![image-20210811211416362](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210811211416362.png)



### 动态组件实现

动态组件是使用 **component** 组件，通过一个特殊的attribute **is** 来实现：

**component：** Vue的内置组件，根据属性is的值，来决定那个组件被选渲染。`is` 的值是一个字符串，它既可以是 HTML 标签名称也可以是组件名称。**如果你传递组件本身到 `is` 而不是其名字，则不需要注册。**

![image-20210811212012331](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210811212012331.png)

**这个currentTab的值需要是什么内容呢？**

1. 可以是通过component函数注册的组件； 
2. 在一个组件对象的components对象中注册的组件；

## 动态组件

### 动态组件的传值

**如果是动态组件我们可以给它们传值和监听事件吗？**

1. 也是一样的； 
2. 只是我们需要将属性和监听事件放到component上来使用；

![image-20210811214920431](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210811214920431.png)



## Keep-alive

### 认识keep-alive

**我们先对之前的案例中About组件进行改造：**

在其中增加了一个按钮，点击可以递增的功能；

![image-20210811220635353](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210811220635353.png)

比如我们将counter点到10，那么在切换到home再切换回来about时，状态是否**可以保持**呢？

1. 答案是否定的； 
2. 这是因为默认情况下，我们在切换组件后，about组件会被销毁掉，再次回来时会重新创建组件；

但是，在开发中某些情况我们希望**继续保持组件的状态**，而不是销毁掉，这个时候我们就可以使用一个**内置组件**：**keep-alive。**

![image-20210811221617535](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210811221617535.png)

![image-20210811221725646](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210811221725646.png)

**的确记录了组件所处的状态。**



### keep-alive属性

**keep-alive有一些属性：**

1. **include** - string | RegExp | Array。只有名称匹配的组件会被缓 存；
2. **exclude** - string | RegExp | Array。任何名称匹配的组件都不 会被缓存； 
3. **max** - number | string。最多可以缓存多少组件实例，一旦达 到这个数字，那么缓存组件中最近没有被访问的实例会被销毁；

**include 和 exclude prop 允许组件有条件地缓存：**

1. 二者都可以用逗号分隔字符串、正则表达式或一个数组来表示； 
2. 匹配首先检查组件自身的 name 选项；

![image-20210812090809195](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812090809195.png)

```vue
<!-- 内置组件 keep-alive的使用，
    记录组件的状态，切换时不会被销毁 -->
    <!-- 在 keep-alive 这个组件内，
    不要使用注释：不然会报警告，而且也不能记录组件的状态了 -->
    <!-- include="组件里面的name属性的值，
    推荐每个组件都加上name属性，和data同级，多个组件名称逗号分隔
    中间不要有空格" -->
    <!-- include="about,home" v-bind:include="/正则表达式/" -->
    <!-- include="数组" -->
     <keep-alive include="about">
      <component :is="currentTab" :name="'毛毛'" 
      :age="22" @pageClick="click"></component>
    </keep-alive> 

    <!-- 排除缓存，这个属性匹配到的都不会被缓存 -->
    <!-- exclude的用法同上 -->
     <keep-alive exclude="about">
      <component :is="currentTab" :name="'毛毛'" 
      :age="22" @pageClick="click"></component>
    </keep-alive> 

<!-- 最大缓存数量，是一个数字，也可以写字符串 -->
<!-- 会清除缓存列表中，最长时间没有被访问的那个组件 -->
    <keep-alive max="2">
      <component :is="currentTab" :name="'毛毛'" 
      :age="22" @pageClick="click"></component>
    </keep-alive>
```

### 缓存组件的生命周期

对于缓存的组件来说，再次进入时，我们是不会执行**created**或者**mounted**等生命周期函数的：

1. 但是有时候我们确实希望监听到何时重新进入到了组件，何时离开了组件； 
2. 这个时候我们可以使用activated 和 deactivated 这两个生命周期钩子函数来监听；

![image-20210812143746757](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812143746757.png)

![image-20210812143904102](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812143904102.png)





## Webpack5打包

### Webpack的代码分包

**默认的打包过程：**

默认情况下，在构建整个组件树的过程中，因为**组件和组件之间是通过模块化直接依赖的**，那么**webpack在打包**时就会将组 件模块打包到一起（比如一个**app.js**文件中）；

这个时候随着项目的不断庞大，app.js文件的内容过大，会造成**首屏的**渲染速度变慢；

![image-20210812093141976](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812093141976.png)

**打包时，代码的分包：**

1. 所以，对于一些不需要立即使用的组件，我们可以单独对它们进行拆分，拆分成一些小的代码块chunk.js； 
2. 这些chunk.js会在需要时从服务器加载下来，并且运行代码，显示对应的内容；

**那么webpack中如何可以对代码进行分包呢？**

![image-20210812093944480](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812093944480.png)



## 异步组件

### Vue中实现异步组件

如果我们的项目过大了，对于**某些组件我们希望通过异步的方式来进行加载**（目的是可以对其进行**分包处理**），那 么Vue中给我们提供了一个函数：**defineAsyncComponent**。

**defineAsyncComponent接受两种类型的参数：**

1. 类型一：工厂函数，该工厂函数需要返回一个Promise对象； 
2. 类型二：接受一个对象类型，对异步函数进行配置；

#### 工厂函数类型一的写法

![image-20210812095742723](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812095742723.png)

#### 异步组件对象类型写法

![image-20210812100721257](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812100721257.png)

```js

// import AsyncCategory from './AsyncCategory.vue'

// 打包组件时也进行分包，需要使用vue3中提供好的一个函数
// defineAsyncComponent
import { defineAsyncComponent } from 'vue'
// 现在这个组件 AsyncCategory 就变成异步组件了，组件的使用和注册还是和以前一样
// const AsyncCategory =
//   defineAsyncComponent(() => import('./AsyncCategory.vue'));

const AsyncCategory = defineAsyncComponent({
  // 工厂函数，只有这一个参数时，和上个写法没有区别
  loader: () => import('./AsyncCategory.vue'),
  // 加载过程中显示的组件，当真正的组件加载完毕时会进行切换
  loadingComponent: Loading,
  // 加载失败时显示的组件
  errorComponent: ErrorCmp,
  // 在显示 loadingComponent 组件之前的延迟，默认值是200 单位ms
  // 只有指定时长没有加载完毕我们loader指定的组件，才会显示 loadingComponent
  delay: 200,
  // timeout 超时时间，超过了指定时间没有加载完毕指定组件
  // 则会显示错误组件，默认值 Infinity 不超时
  timeout: Infinity,
  // 组件是否可以挂起 默认值 true
  suspensible: true,
  // onError 监听组件是否发生错误
  /* 
  err: 错误信息
  retry: 一个函数，调用该函数则进行重新加载
  fail: 一个函数 允许加载程序结束退出（类似于强制结束）
  attempts: 重试次数
  */
  onError:(err,retry,fail,attempts)=>{
    console.log(err);
  }
})
export default {
  components: { AsyncCategory },
}

```

### 异步组件和Suspense

#### Suspense

![image-20210812102407747](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812102407747.png)

**目前：Suspense显示的是一个实验性的特性，API随时可能会修改。**

**Suspense是一个内置的全局组件，该组件有两个插槽：**

1. default：如果default可以显示，那么显示default的内容； 
2. fallback：如果default无法显示，那么会显示fallback插槽的内容；

![image-20210812103328161](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812103328161.png)



## $引用元素和组件



### $refs的使用

**某些情况下，我们在组件中想要直接获取到元素对象或者子组件实例：**

1. 在Vue开发中我们是不推荐进行DOM操作的； 
2. 这个时候，我们可以给元素或者组件绑定一个ref的attribute属性；

**组件实例有一个$refs属性：**

它一个对象Object，持有注册过 **ref attribute** 的所有 DOM 元素和组件实例

![image-20210812111727677](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812111727677.png)

![image-20210812112214446](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812112214446.png)

### $parent和$root

**我们可以通过$parent来访问父元素。**

HelloWorld.vue的实现：

这里我们也可以**通过$root**来实现，因为App是我们的根组件；

注意：在**Vue3**中已经**移除了$children的**属性，所以不可以使用了。

![image-20210812112904661](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812112904661.png)



### $el

**可以拿到根元素，就是使用本组件的根DOM元素。但是不能操作**

![image-20210812113751687](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812113751687.png)

![image-20210812113837138](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812113837138.png)

**实际上拿到的就是自己，组件本质上也是一个个的html，dom元素，根元素就是本组件的的根元素**

**如果本组件出现了多个根标签，比如：**

![image-20210812114118598](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812114118598.png)

**我们不使用div进行包裹，那么，获取的值$el获取的值又会不同**

![image-20210812114217105](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812114217105.png)

**vue3已经可以不使用单个根标签了。但是本质上还是被一个其他标签进行了包裹的。**



## 生命周期

### 认识生命周期

**什么是生命周期呢？**

1. 每个组件都可能会经历从**创建、挂载、更新、卸载**等一系列的过程； 
2. 在这个过程中的某一个阶段，用于可能会想要**添加一些属于自己的代码逻辑**（比如组件创建完后就请求一些服 务器数据）； 
3. 但是我们如何可以知道目前组件正在哪一个过程呢？Vue给我们提供了组件的**生命周期函数**

**生命周期函数：**

1. **生命周期函数是一些钩子函数**，在某个时间会被**Vue源码内部进行回调；** 
2. 通过**对生命周期函数的回调**，我们可以知道**目前组件正在经历什么阶段**； 
3. 那么我们就可以在该生命周期中编写**属于自己的逻辑代码**了；

### 生命周期的流程

[生命周期图](https://v3.cn.vuejs.org/guide/instance.html#%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E9%92%A9%E5%AD%90)

![image-20210812134823543](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812134823543.png)



## 组件的v-model

### 组件v-model的实现

**那么，为了我们的MyInput组件可以正常的工作，这个组件内的 <input>  必须：**

1. 将其 value attribute 绑定到一个名叫 **modelValue** 的 prop 上；
2. 在其 input 事件被触发时，将新的值通过自定义的 **update:modelValue** 事件抛出；

![image-20210812145850159](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812145850159.png)

![image-20210812145956762](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812145956762.png)

**如果我们直接在组件内部使用v-model绑定外界传进来的属性值来进行双向绑定会怎么样呢？首先这种做法不可取，其次也不会影响到外界的值**

![image-20210812150633849](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812150633849.png)



### computed实现

我们依然希望在组件内部按照双向绑定的做法去完成，应该如何操作呢？我们可以使用**计算属性的setter和getter 来完成。**

![image-20210812151236189](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812151236189.png)

### 绑定多个属性

我们现在通过v-model是直接绑定了一个属性，如果我们**希望绑定多个属性呢？**

1. 也就是我们希望在一个组件上使用**多个v-model是否可以实现**呢？ 
2. 我们知道，默认情况下的v-model其实是绑定了 **modelValue** 属性和 **@update:modelValue**的事件； 
3. 如果我们希望绑定更多，可以给**v-model传入一个参数**，那么这个参数的名称就是**我们绑定属性的名称**；

**注意：这里我是绑定了两个属性的**

![image-20210812152740822](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812152740822.png)

**v-model:age相当于做了两件事：**

1. **绑定了age属性；** 
2. **监听了 @update:age的事件；**

![image-20210812153016033](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812153016033.png)

![image-20210812153035830](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210812153035830.png)





# Vue3过渡&动画实现

## 动画

### 认识动画

在开发中，我们想要给一个组件的显示和消失添加某种**过渡动画**，可以很好的**增加用户体验**：

1. React框架本身并没有提供任何动画相关的API，所以在React中使用过渡动画我们需要使用一个第三方库 react-transition-group；
2. Vue中为我们提供一些内置组件和对应的API来完成动画，利用它们我们可以方便的实现过渡动画效果；

**我们来看一个案例：**

Hello World的显示和隐藏；

通过下面的代码实现，是不会有任何动画效果的；

![image-20210813163332445](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210813163332445.png)

****

**没有动画的情况下，整个内容的显示和隐藏会非常的生硬：**

如果我们希望给单元素或者组件实现过渡动画，可以使用 **transition** 内置组件来完成动画；

### Vue的transition动画

**Vue 提供了 transition 的封装组件**，在下列情形中，可以给任何元素和组件添加进入/离开过渡：

1. **p条件渲染 (使用 v-if)条件展示 (使用 v-show)** 
2. **动态组件** 
3. **组件根节点**

![image-20210813164522866](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210813164522866.png)

### Transition组件的原理

**我们会发现，Vue自动给h2元素添加了动画，这是什么原因呢？**

**当插入或删除包含在 transition 组件中的元素时，Vue 将会做以下处理：**

1. 自动**嗅探**目标元素是否**应用了CSS过渡或者动画**，如果有，那么在恰当的时机**添加/删除 CSS类名**； 
2. 如果 **transition** 组件提供了**JavaScript钩子函数**，这些钩子函数将在恰当的时机被调用； 
3. 如果没有找到JavaScript钩子并且也没有检测到CSS过渡/动画，**DOM插入、删除操作将会立即执行；**

**那么都会添加或者删除哪些class呢？**

### 过渡动画class

**我们会发现上面提到了很多个class，事实上Vue就是帮助我们在这些class之间来回切换完成的动画：**

1.  **v-enter-from：**定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。 
2. **v-enter-active：**定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动 画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。 
3. **v-enter-to：**定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter-from 被移除)，在过渡/ 动画完成之后移除。 
4. **v-leave-from：**定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。 
5. **v-leave-active：**定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在 过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。 
6. **v-leave-to：**离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave-from 被删除)，在过渡/ 动画完成之后移除。

#### class添加的时机和命名规则

![image-20210813171422027](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210813171422027.png)

**class的name命名规则如下：**

1. 如果我们使用的是一个没有name的transition，那么所有的class是以 v- 作为默认前缀； 
2. 如果我们添加了一个name属性，比如 ，那么所有的class会以 **name属性值-** 开头；

#### 过渡css动画

前面我们是通过**transition**来实现的动画效果，另外我们也可以通过**animation**来实现。

![image-20210813174438158](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210813174438158.png)

#### 同时设置过渡和动画

Vue为了知道过渡的完成，内部是在监听 transitionend 或 animationend，到底使用哪一个取决于元素应用的 CSS规则：

如果我们只是使用了其中的一个，那么Vue能自动识别类型并设置监听；

**但是如果我们同时使用了过渡和动画呢？**

1. 并且在这个情况下可能某一个动画执行结束时，另外一个动画还没有结束； 
2. 在这种情况下，我们可以设置 type 属性为 animation 或者 transition 来明确的告知Vue监听的类型；

![image-20210813211840483](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210813211840483.png)

```css
.mydiv{
    width: 100px;
    height: 100px;
  background-color: aqua;
  }
  .mao-enter-from,.mao-leave-to{
    opacity: 0;
  }
  .mao-enter-to,.mao-enter-from{
    opacity: 1;
  }
.mao-enter-active {
  animation: bounce 2s ease;
}
.mao-leave-active {
  animation: bounce 2s reverse;
}
.mao-enter-active,.mao-leave-active{
  transition: opacity 1s ease;
}
/* 定义动画帧 */
@keyframes bounce {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
```

#### 显示的指定动画时间

我们也可以显示的来指定过渡的时间，**通过 duration 属性。**

**duration可以设置两种类型的值：**

1. number类型：同时设置进入和离开的过渡时间； 
2. object类型：分别设置进入和离开的过渡时间；

![image-20210813212205061](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210813212205061.png)



#### 过渡的模式mode

**我们来看当前的动画在两个元素之间切换的时候存在的问题：**

![image-20210813212531361](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210813212531361.png)

![image-20210813212513947](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210813212513947.png)

我们会发现 **两个元素的动画是同时存在的**：

1. 这是因为默认情况下进入和离开动画是同时发生的；
2. 如果确实我们希望达到这个的效果，那么是没有问题；

但是如果我们**不希望同时执行进入和离开动画**，那么我们需要设置t**ransition的过渡模式：**

1. **in-out:** 新元素先进行过渡，完成之后当前元素过渡离开； 
2. **out-in:** 当前元素先进行过渡，完成之后新元素过渡进入；

![image-20210813213053898](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210813213053898.png)

### 动态组件的切换

#### 动态组件的切换动画

**上面的示例同样适用于我们的动态组件：**

![image-20210813214234810](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210813214234810.png)



#### appear初次渲染

默认情况下，**首次渲染的时候是没有动画的**，如果我们希望给他添加上去动画，那么就可以增加另外一个属性 **appear**：

![image-20210813214335867](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210813214335867.png)



### 第三方动画库

#### 认识animate.css

如果我们手动一个个来编写这些动画，那么效率是比较低的，所以在开发中我们可能会**引用一些第三方库的动画库， 比如animate.css。**

**什么是animate.css呢？**

**Animate.css** is a library of ready-to-use, cross-browser animations for use in your web projects. Great  for emphasis, home pages, sliders, and attention-guiding hints.

**Animate.css**是一个已经准备好的、跨平台的动画库为我们的web项目，对于**强调、主页、滑动、注意力引导** 非常有用；

**如何使用Animate库呢？**

1. 第一步：需要安装animate.css库； 
2. 第二步：导入animate.css库的样式； 
3. 第三步：使用animation动画或者animate提供的类；

![image-20210814094340607](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814094340607.png)

#### 自定义过渡class

**我们可以通过以下 attribute 来自定义过渡类名：**

1. enter-from-class 
2. enter-active-class 
3. enter-to-class 
4. leave-from-class 
5. leave-active-class 
6. leave-to-class

他们的优先级高于普通的类名，这对于 Vue 的过渡系统和其他第三方 CSS 动画库，如 **Animate.css**. 结合使用十 分有用。

#### animate.css库的使用

**安装animate.css：**

```shell
npm i animate.css --save
```

**在main.js中导入animate.css：**

```js
import 'animate.css'
```

**接下来在使用的时候我们有两种用法：**

用法一：直接使用animate库中定义的 keyframes 动画；

![image-20210814095212789](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814095212789.png)

用法二：直接使用animate库提供给我们的类；

![image-20210814095242072](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814095242072.png)

### 认识gsap库

某些情况下我们希望通过JavaScript来实现一些动画的效果，这个时候我们可以选择使用gsap库来完成。

**什么是gsap呢？**

1. GSAP是The GreenSock Animation Platform（GreenSock动画平台）的缩写；
2. 它可以通过JavaScript为CSS属性、SVG、Canvas等设置动画，并且是浏览器兼容的；

**这个库应该如何使用呢？**

1. 第一步：需要安装gsap库； 
2. 第二步：导入gsap库； 
3. 第三步：使用对应的api即可；

**我们可以先安装一下gsap库：**

```shell
npm i gsap
```

#### JavaScript钩子

在使用动画之前，我们先来看一下**transition**组件给我们提供的**JavaScript钩子，**这些钩子可以帮助我们监听动画执行到 什么阶段了。

![image-20210814101138774](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814101138774.png)

![image-20210814101157536](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814101157536.png)

当我们使用JavaScript来执行过渡动画时，需要**进行 done 回调**，否则它们将会被同步调用，过渡会立即完成。

添加 **:css="false"，**也会让 **Vue 会跳过 CSS 的检测**，除了性能略高之外，这可以**避免过渡过程中 CSS 规则的影响**。

![image-20210814101301362](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814101301362.png)

#### gsap库的使用

**那么接下来我们就可以结合gsap库来完成动画效果：**

![image-20210814103331698](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814103331698.png)

![image-20210814103336037](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814103336037.png)

![image-20210814103339396](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814103339396.png)

![image-20210814103400933](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814103400933.png)



#### gsap实现数字变化动画

在一些项目中，我们会见到**数字快速变化的动画效果**，这个动画可以**很容易通过gsap来实现：**

![image-20210814104630294](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814104630294.png)

![image-20210814104646356](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814104646356.png)



#### 认识列表的过渡

目前为止，过渡动画我们只要是**针对单个元素或者组件**的：

要么是单个节点； 要么是同一时间渲染多个节点中的一个；

那么如果希望渲染的是一个列表，并且**该列表中添加删除数据也希望有动画执行**呢？

这个时候我们要使用 <transition-group>  组件来完成；

**使用<transition-group> 有如下的特点：**

1. 默认情况下，它不会渲染一个元素的包裹器，但是你可以指定一个元素并以 tag attribute 进行渲染； 
2. 过渡模式 **mode** 不可用，因为我们不再相互切换特有的元素； 
3. 内部元素总是需要提供唯一的 key attribute 值； 
4. CSS 过渡的类将会应用在内部的元素中，而不是这个组/容器本身；

#### 列表过渡的基本使用

**我们来做一个案例：**

案例是一列数字，可以继续添加或者删除数字；

**在添加和删除数字的过程中，对添加的或者移除的数字添加动画；**

![image-20210814111215939](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814111215939.png)

![image-20210814111236462](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814111236462.png)

![image-20210814111344677](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814111344677.png)

```html
<template>
  <div>
    <button @click="addNum">添加数字</button>
    <button @click="delNum">删除数字</button>
    <br />
    <!-- 列表的过渡，使用该内置组件进行包裹 -->
    <transition-group tag="p" name="mao">
      <span v-for="number in numbers" :key="number">{{ number }}</span>
    </transition-group>
  </div>
</template>

<script>
export default {
  data() {
    return {
      numbers: [1, 2, 3, 4, 5, 6, 7, 8, 9],
      numberCounter: 10
    }
  },
  methods: {
    addNum() {
      // this.numbers.push(this.numberCounter++)
      this.numbers.splice(this.randomIndex(), 0, this.numberCounter++)
    },
    delNum() {
      this.numbers.splice(this.randomIndex(), 1)
    },
    randomIndex() {
      return Math.floor(Math.random() * this.numbers.length)
    }
  },
}
</script>

<style scoped>
button {
  margin: 10px;
}
span {
  margin-right: 5px;
  /* 转为行内块元素 */
  /* 行内元素是不能改变宽高等 */
  display: inline-block;
}
.mao-enter-from,.mao-leave-to{
  opacity: 0;
  transform: translateY(30px);
}
.mao-enter-active,.mao-leave-active{
  transition: all 2s ease-in;
}
</style>
```

#### 列表过渡的移动动画

**在上面的案例中虽然新增的或者删除的节点是有动画的，但是对于哪些其他需要移动的节点是没有动画的：**

1. 我们可以通过使用一个新增的 v-move 的class来完成动画； 
2. 它会在元素改变位置的过程中应用； 
3. 像之前的名字一样，我们可以通过name来自定义前缀；

![image-20210814112549656](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814112549656.png)

![image-20210814112553932](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814112553932.png)

![image-20210814112658298](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814112658298.png)

#### 列表的交错过渡案例

**我们来通过gsap的延迟delay属性，做一个交替消失的动画：**

![image-20210814115410408](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210814115410408.png)

```html
<template>
  <div>
    <input type="text" v-model="keyword" />
    <!-- 使用ul进行包裹动画的元素 -->
    <transition-group 
    tag="ul" name="mao"
    @before-enter="beforeEnter"
    @enter="enter"
    @leave="leave"
    :css="false">
      <li :key="name" 
      v-for="(name,index) in showNames"
      :data-index="index">{{ name }}</li>
    </transition-group>
  </div>
</template>

<script>
  import gsap from 'gsap'
export default {
  data() {
    return {
      keyword: '',
      names: ['aaa', 'avc', 'read', 'tre', 'tres', 'ttt', 'www', 'mao', 'jjj', 'gs', 'gdj', 'adssa']
    }
  },
  computed: {
    showNames() {
      return this.names.filter(name =>
        name.indexOf(this.keyword) !== -1
      )
    }
  },
  methods: {
    // 指定初始化的状态
    beforeEnter(el){
      el.style.opaciy = 0
      el.style.height = 0
    },
    enter(el,done){
      gsap.to(el,{
        opacity:1,
        height:'1.5em',
        delay:el.dataset.index* 0.2,
        onComplete:done
      })
    },
    leave(el,done){
      gsap.to(el,{
        opacity:0,
        height:0,
        delay:el.dataset.index * 0.2,
        onComplete:done
      })
    }
  },
}
</script>

<style scoped>
  /* .mao-enter-from,.mao-leave-to{
    opacity: 0;
  }
  .mao-enter-to,.mao-leave-from{
    opacity: 1;
  }
  .mao-enter-active,.mao-leave-active{
    transition: opacity 2s ease;
  } */
</style>
```

