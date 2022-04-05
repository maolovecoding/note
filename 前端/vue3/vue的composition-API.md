# Vue3-Composition-API(一)



## 前置补充

### 认识Mixin

目前我们是使用组件化的方式在开发整个Vue的应用程序，但是组件和组件之间有时候会**存在相同的代码逻辑**，我 们希望对**相同的代码逻辑进行抽取。**

在Vue2和Vue3中都支持的一种方式就是**使用Mixin来完成：**

1. Mixin提供了一种非常灵活的方式，来分发Vue组件中的可复用功能； 
2. 一个Mixin对象可以包含任何组件选项； 
3. 当组件使用Mixin对象时，所有Mixin对象的选项将被 混合 进入该组件本身的选项中；

### Mixin的基本使用

![image-20210817103436943](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817103436943.png)

![image-20210817103455764](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817103455764.png)

### Mixin的合并规则

**如果Mixin对象中的选项和组件对象中的选项发生了冲突，那么Vue会如何操作呢？**

这里**分成不同的情况**来进行处理；

**情况一：如果是data函数的返回值对象**

- 返回值对象默认情况下会进行合并； 
- 如果data返回值对象的属性发生了冲突，那么会保留组件自身的数据；

**情况二：如何生命周期钩子函数**

- 生命周期的钩子函数会被合并到数组中，都会被调用；

**情况三：值为对象的选项，例如 methods、components 和 directives，将被合并为同一个对象。**

- 比如都有methods选项，并且**都定义了方法，那么它们都会生效**； 
- 但是如果**对象的key相同**，那么**会取组件对象的键值对**；

![image-20210817125624404](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817125624404.png)

![image-20210817125637070](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817125637070.png)

### 全局混入Mixin

**如果组件中的某些选项，是所有的组件都需要拥有的，那么这个时候我们可以使用全局的mixin：**

- 全局的Mixin可以使用 应用app的方法 mixin 来完成注册； 
- 一旦注册，那么全局混入的选项将会影响每一个组件；

![image-20210817130238279](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817130238279.png)

![image-20210817130247318](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817130247318.png)

### extends

**另外一个类似于Mixin的方式是通过extends属性：**

允许声明扩展另外一个组件，类似于Mixins；

**在开发中extends用的非常少，在Vue2中比较推荐大家使用Mixin，而在Vue3中推荐使用Composition API。**

![image-20210817132414819](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817132414819.png)

## Options API的弊端

在Vue2中，我们编写组件的方式是Options API：

1. Options API的一大特点就是在对应的属性中编写对应的功能模块； 
2. 比如data定义数据、methods中定义方法、computed中定义计算属性、watch中监听属性改变，也包括生命 周期钩子；

但是这种代码有一个很大的弊端：

1. 当我们实现某一个功能时，这个功能对应的代码逻辑会被拆分到各个属性中； 
2. 当我们组件变得更大、更复杂时，逻辑关注点的列表就会增长，那么同一个功能的逻辑就会被拆分的很分散； 
3. 尤其对于那些一开始没有编写这些组件的人来说，这个组件的代码是难以阅读和理解的（阅读组件的其他人）；

**如果我们能将同一个逻辑关注 点相关的代码收集在一起会更 好。** 

**这就是Composition API想 要做的事情，以及可以帮助我 们完成的事情。** 

**也有人把Vue Composition API简称为VCA。**

## 认识Composition API

那么既然知道Composition API想要帮助我们做什么事情，接下来看一下到底是怎么做呢？

1. 为了开始使用Composition API，我们需要有一个可以实际使用它（编写代码）的地方； 
2. 在Vue组件中，这个位置**就是 setup 函数；**

**setup其实就是组件的另外一个选项：**

1. 只不过这个选项强大到我们可以用它来替代之前所编写的大部分其他选项； 
2. 比如methods、computed、watch、data、生命周期等等；

**接下来我们一起学习这个函数的使用**

- 函数的参数 
- 函数的返回值

### setup函数的参数

我们先来研究一个setup函数的参数，它主要有两个参数：

- 第一个参数：props 
- 第二个参数：context

props非常好理解，它其实就是父组件传递过来的属性会被**放到props对象**中，我们在setup中如果需要使用，那么就可 以**直接通过props参数**获取：

1. 对于定义props的类型，我们还是和之前的规则是一样的，在props选项中定义； p
2. 并且在template中依然是可以正常去使用props中的属性，比如message； 
3. 如果我们在setup函数中想要使用props，那么不可以通过 this 去获取（后面我会讲到为什么）； 
4. 因为props有直接作为参数传递到setup函数中，所以我们可以直接通过参数来使用即可；

**另外一个参数是context，我们也称之为是一个SetupContext，它里面包含三个属性：**

- attrs：所有的非prop的attribute； 
- slots：父组件传递过来的插槽（这个在以渲染函数返回时会有作用，后面会讲到）； 
- emit：当我们组件内部需要发出事件时会用到emit（因为我们不能访问this，所以不可以通过 this.$emit发出事件）；

![image-20210817141859483](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817141859483.png)

![image-20210817141907813](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817141907813.png)

![image-20210817141917796](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817141917796.png)

### setup函数的返回值

 setup既然是一个函数，那么它也可以有返回值，它的返回值用来做什么呢？

- setup的返回值可以在模板template中被使用； 
- 也就是说我们可以通过setup的返回值来替代data选项；

甚至是我们可以返回一个执行函数来代替在methods中定义的方法：

**但是，如果我们将 counter 在 increment 或者 decrement进行操作时，是否可以实现界面的响应式呢？**

- 答案是不可以； 
- 这是因为对于一个定义的变量来说，默认情况下，Vue并不会跟踪它的变化，来引起界面的响应式操作；

![image-20210817161645538](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817161645538.png)

### setup不可以使用this

**官方关于this有这样一段描述**

- 表达的含义是this并没有指向当前组件实例； 
- 并且在setup被调用之前，data、computed、methods等都没有被解析； 
- 所以无法在setup中获取this；

![image-20210817163136789](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817163136789.png)

![image-20210817163156525](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817163156525.png)

### Reactive API

如果想为在setup中定义的数据提供响应式的特性，那么我们可以**使用reactive的函数**

![image-20210817164854036](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817164854036.png)

![image-20210817164925535](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817164925535.png)

**那么这是什么原因呢？为什么就可以变成响应式的呢？**

- 这是因为当我们使用reactive函数处理我们的数据之后，数据再次被使用时就会进行依赖收集； 
- 当数据发生改变时，所有收集到的依赖都是进行对应的响应式操作（比如更新界面）； 
- 事实上，我们编写的data选项，也是在内部交给了reactive函数将其编程响应式对象的；

### Ref API(尤大大推荐使用)

**reactive API对**传入的**类型是有限制**的，它要求我们必须传入的是**一个对象或者数组**类型：

**如果我们传入一个基本数据类型（String、Number、Boolean）会报一个警告；**

![image-20210817170808693](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817170808693.png)

这个时候Vue3给我们提供了另外一个**API：ref API**

- ref 会返回一个可变的响应式对象，该对象作为一个 响应式的引用 维护着它内部的值，这就是ref名称的来源； 
- 它内部的值是在ref的 value 属性中被维护的；

**这里有两个注意事项：**

- 在模板中引入ref的值时，Vue会自动帮助我们进行解包操作，所以我们并不需要在模板中通过 ref.value 的方式 来使用； 
- 但是在 setup 函数内部，它依然是一个 ref引用， 所以对其进行操作时，我们依然需要使用 ref.value的方式；



![image-20210817171151124](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817171151124.png)

![image-20210817171207810](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817171207810.png)

#### Ref自动解包

模板中的解包是浅层的解包，如果我们的代码是下面的方式，**现在也可以自动解包了**：

![image-20210817172326881](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817172326881.png)

![image-20210817172343208](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817172343208.png)

如果我们将ref放到一个reactive的属性当中，那么在模板中使用时，它会自动解包：

![image-20210817172826882](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817172826882.png)

![image-20210817172837494](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817172837494.png)

### 认识readonly

我们**通过reactive或者ref可以**获取到一个**响应式的对象**，但是某些情况下，我们传入给其他地方（组件）的这个 响应式对象希望在另外一个地方（**组件）被使用**，但是**不能被修改**，这个时候如何防止这种情况的出现呢？

- Vue3为我们提供了readonly的方法； 
- readonly会返回原生对象的只读代理（也就是它依然是一个Proxy，这是一个proxy的set方法被劫持，并且不 能对其进行修改）；

**在开发中常见的readonly方法会传入三个类型的参数：**

- 类型一：普通对象； 
- 类型二：reactive返回的对象； 
- 类型三：ref的对象；

#### readonly的使用

**在readonly的使用过程中，有如下规则：**

- readonly返回的对象都是不允许修改的；
- 但是经过readonly处理的**原来的对象**是允许被修改的；
  - 比如 const info = readonly(obj)，info对象是不允许被修改的； 
  - 当obj被修改时，readonly返回的info对象也会被修改； 
  - 但是我们不能去修改readonly返回的对象info；
- 其实本质上就是readonly返回的对象的setter方法被**劫持了而已；**

#### readonly的应用

**那么这个readonly有什么用呢？**

在我们传递给其他组件数据时，往往希望其他组件使用我们传递的内容，但是不允许它们修改时，就可以使用 readonly了

![image-20210817180815814](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817180815814.png)

![image-20210817180921569](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817180921569.png)

![image-20210817180954082](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210817180954082.png)





# Vue3-Composition-API(二)

## Reactive-API

### Reactive判断的API

- **isProxy** 

  检查对象是否是由 reactive 或 readonly创建的 proxy。 

- **isReactive** 
  1. 检查对象是否是由 reactive创建的响应式代理： 
  2. 如果该代理是 readonly 建的，但包裹了由 reactive 创建的另一个代理，它也会返回 true； 

- **isReadonly** 
  1. 检查对象是否是由 readonly 创建的只读代理。

-  **toRaw** 
  1. 返回 reactive 或 readonly 代理的原始对象（不建议保留对原始对象的持久引用。请谨慎使用）。 

- **shallowReactive** 
  1. 创建一个响应式代理，它跟踪其自身 property 的响应性，但不执行嵌套对象的深层响应式转换 (深层还是原生对象)。 

- **shallowReadonly** 
  1. 创建一个 proxy，使其自身的 property 为只读，但不执行嵌套对象的深度只读转换（深层还是可读、可写的）。



## toRefs

如果我们**使用ES6的解构语法**，**对reactive返回的对象进行解构获取值**，那么之后无论是修改结构后的变量，还是修改reactive 返回的state对象，**数据都不再是响应式的：**

![image-20210819110318174](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819110318174.png)

那么有没有办法让我们解构出来的属性是**响应式**的呢？

1. Vue为我们提供了一个toRefs的函数，可以将reactive返回的对象中的属性都转成ref；

2. 那么我们再次进行解构出来的 name 和 age 本身都是 ref的；

这种做法相当于已经在**state.name和ref.value之间建立了 链接，**任何一个修改都会引起另外一个变化；

![image-20210819111534916](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819111534916.png)

![image-20210819111537988](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819111537988.png)

## toRef

如果我们只希望转换一个reactive对象中的属性为ref, 那么可以使**用toRef的方法：**

![image-20210819112216206](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819112216206.png)

```js
// toRef函数，对其中一个属性进行解构为ref响应式对象
/*
  toRef(对象，'对象需要转为ref的属性')
*/
let { name } = info2;
let age = toRef(info2, "age");
```

## ref其他的API

 **unref** 

如果我们想要获取一个**ref引用中的value**，那么也可以**通过unref方法：** 

- 如果参数是一个 ref，则返回内部值，否则返回参数本身； 

- 这是 **val = isRef(val) ? val.value : val** 的语法糖函数；

**isRef** 

判断值是否是一个ref对象。

**shallowRef** 

创建一个浅层的ref对象；

 **triggerRef** 

**手动触发和 shallowRef 相关联的副作用：(比如页面的响应式刷新，就是刷新响应式数据以后的副作用**)



## customRef

创建一个**自定义的ref**，并**对其依赖项跟踪和更新触发**进行显示控制：

它需要一个工厂函数，该函数接受 **track 和 trigger** 函数作为参数；并且应该返回一个**带有 get 和 set 的对象；**

**这里我们使用一个的案例：**

对双向绑定的属性进行debounce**(节流)**的操作；

![image-20210819152343898](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819152343898.png)

![image-20210819152356575](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819152356575.png)

## computed

 在前面我们讲解过计算属性computed：当我们的某些属性是依赖其他状态时，我们可以使用计算属性来处理

1. 在前面的Options API中，我们是使用computed选项来完成的； 
2. 在Composition API中，我们可以在 setup 函数中使用 computed 方法来编写一个计算属性；

如何使用computed呢？

1. 方式一：接收一个getter函数，并为 getter 函数返回的值，返回一个不变的 ref 对象； 
2. 方式二：接收一个具有 get 和 set 的对象，返回一个可变的（可读写）ref 对象；

![image-20210819154035057](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819154035057.png)

![image-20210819154045046](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819154045046.png)

## 侦听数据的变化

在前面的Options API中，我们可以通过watch选项来侦听data或者props的数据变化，当数据变化时执行某一些 操作。

在Composition API中，我们可以使用watchEffect和watch来完成响应式数据的侦听；

1. watchEffect用于自动收集响应式数据的依赖； 
2. watch需要手动指定侦听的数据源；



### watchEffect

当侦听到某些响应式数据变化时，我们希望执行某些操作，这个时候可以使用 watchEffect。

我们来看一个案例：

- 首先，watchEffect传入的函数会被立即执行一次，并且在执行的过程中会收集依赖； 
- 其次，只有收集的依赖发生变化时，watchEffect传入的函数才会再次执行；

![image-20210819155923525](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819155923525.png)

![image-20210819160013737](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819160013737.png)



### watchEffect的停止侦听

如果在发生某些情况下，我们希望**停止侦听**，这个时候我们可以获取watchEffect的**返回值函数**，调用该函数即可。

比如在上面的案例中，我们age达到25的时候就停止侦听：

![image-20210819161134008](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819161134008.png)

### watchEffect清除副作用

什么是清除副作用呢？

- 比如在开发中我们需要在侦听函数中执行网络请求，但是在网络请求还没有达到的时候，我们停止了侦听器， 或者侦听器侦听函数被再次执行了。
- 那么上一次的网络请求应该被取消掉，这个时候我们就可以清除上一次的副作用；

在我们给watchEffect传入的函数被回调时，其实可以获取到一个参数：**onInvalidate**

- 当**副作用即将重新执行 或者 侦听器被停止** 时会执行该函数传入的回调函数； 
- 我们可以在传入的回调函数中，**执行一些清除工作；**

![image-20210819162743948](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819162743948.png)

![image-20210819162857567](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819162857567.png)

### setup中使用ref

在**讲解 watchEffect执行时机之前**，我们先补充一个知识：在setup中如何使用ref或者元素或者组件？

其实非常简单，我们只需要定义一个ref对象，绑定到元素或者组件的ref属性上即可；

![image-20210819203324866](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819203324866.png)

![image-20210819203358058](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819203358058.png)

### watchEffect的执行时机

默认情况下，组件的更新会在副作用函数执行之前：

如果我们希望在副作用函数中获取到元素，是否可行呢？

**结果如上图**

我们会发现打印结果打印了两次：

- 这是因为setup函数在执行时就会立即执行传入的副作用函数，这个时候DOM并没有挂载，所以打印为null； 
- 而当DOM挂载时，会给title的ref对象赋值新的值，副作用函数会再次执行，打印出来对应的元素；

### 调整watchEffect的执行时机

如果我们希望在第一次的时候就打印出来对应的元素呢？

- 这个时候我们需要改变副作用函数的执行时机； 
- 它的默认值是pre，它会在元素 挂载 或者 更新 之前执行； 
- 所以我们会先打印出来一个空的，当依赖的title发生改变时，就会再次执行一次，打印出元素；

我们可以设置副作用函数的执行时机：

![image-20210819203730628](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819203730628.png)

**flush 选项还接受 sync**，这将**强制效果始终同步触发**。然而，这是低效的，应该很少需要。

## Watch的使用

watch的API完全等同于组件watch选项的Property：

- watch需要侦听特定的数据源，并在回调函数中执行副作用； 
- 默认情况下它是惰性的，只有当被侦听的源发生变化时才会执行回调；

与watchEffect的比较，watch允许我们：

- 懒执行副作用（第一次不会直接执行）； 
- 更具体的说明当哪些状态发生变化时，触发侦听器的执行； 
- 访问侦听状态变化前后的值；

### 侦听单个数据源

watch侦听函数的数据源有两种类型：

侦听器数据源可以是**返回值的 getter 函数**，也可以直接是 `ref`：

- 一个getter函数：但是该getter函数必须引用可响应式的对象（比如reactive或者ref）；

- 直接写入一个可响应式的对象，reactive或者ref（比较常用的是ref）；

**下面这种方式就侦听失败了，新值旧值是一样的。**

![image-20210819205627220](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819205627220.png)



**方式一**

![image-20210819211938552](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819211938552.png)

**方式二**

![image-20210819212058408](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819212058408.png)

### 侦听多个数据源

侦听器还可以使用数组同时侦听多个源：

![image-20210819213624892](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819213624892.png)

### 侦听响应式对象

如果我们希望侦听一个数组或者对象，那么可以使用一个getter函数，并且对可响应对象进行解构：

![image-20210819215123772](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819215123772.png)

### watch的选项

如果我们希望侦听一个深层的侦听，那么依然需要设置 deep 为true：

也可以传入 immediate 立即执行；

**如果传入的是一个reactive对象，默认情况就是会自动进行深度侦听的，输入获取到的新值旧值都是还是同一个对象**

![image-20210819215717291](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819215717291.png)

**设置深度侦听**

![image-20210819220928428](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819220928428.png)

![image-20210819221004914](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210819221004914.png)



# Composition API（三） 高级语法补充



## 生命周期钩子

我们前面说过 setup 可以用来替代 data 、 methods 、 computed 、watch 等等这些选项，也可以替代 生命周 期钩子。

**那么setup中如何使用生命周期函数呢？**

可以使用直接导入的 onX 函数注册生命周期钩子；

![image-20210821145225776](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821145225776.png)



![image-20210821144149835](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821144149835.png)

| 选项式 API        | Hook inside `setup` |
| ----------------- | ------------------- |
| `beforeCreate`    | Not needed*         |
| `created`         | Not needed*         |
| `beforeMount`     | `onBeforeMount`     |
| `mounted`         | `onMounted`         |
| `beforeUpdate`    | `onBeforeUpdate`    |
| `updated`         | `onUpdated`         |
| `beforeUnmount`   | `onBeforeUnmount`   |
| `unmounted`       | `onUnmounted`       |
| `errorCaptured`   | `onErrorCaptured`   |
| `renderTracked`   | `onRenderTracked`   |
| `renderTriggered` | `onRenderTriggered` |
| `activated`       | `onActivated`       |
| `deactivated`     | `onDeactivated`     |

> 因为 `setup` 是围绕 `beforeCreate` 和 `created` 生命周期钩子运行的，所以不需要显式地定义它们。换句话说，在这些钩子中编写的任何代码都应该直接在 `setup` 函数中编写。

**这些函数接受一个回调函数，当钩子被组件调用时将会被执行:**

```js
export default {
  setup() {
    // mounted
    onMounted(() => {
      console.log('Component is mounted!')
    })
  }
}
```

<span style="color:red">我们发现，最上面的两个生命周期函数，也就是`beforeCreate` 和 `created` 两个生命周期函数没有对应的composition-API的生命周期函数。实际上，setup函数的执行时机是在这两个生命周期之前的，如果有需要放在这两个生命周期里需要执行的，完全可以直接写在setup函数里面。</span>

其他的生命周期都是一一对应的关系。

## Provide函数

事实上我们之前还学习过Provide和Inject，Composition API也可以替代之前的 Provide 和 Inject 的选项。

**我们可以通过 provide来提供数据**

- 可以通过 provide 方法来定义每个 Property； 
- provide可以传入两个参数：
  - name：提供的属性名称； 
  - value：提供的属性值；

![image-20210821151840084](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821151840084.png)

## Inject函数

在 后代组件 中可以通过 inject 来注入需要的属性和对应的值：

- 可以通过 inject 来注入需要的内容； 

- inject可以传入两个参数：
  - 参数一：要 inject 的 property 的 name； 
  - 参数二：默认值；

![image-20210821152127496](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821152127496.png)

![image-20210821152144802](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821152144802.png)



## 数据的响应式

为了增加 provide 值和 inject 值之间的响应性，我们可以在 provide 值时使用 ref 和 reactive。**如上面代码所示**



## 修改响应式Property

如果我们需要修改可响应的数据，那么最好是在数据提供的位置来修改：

我们可以将修改方法进行共享，在后代组件中进行调用；

![image-20210821154947452](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821154947452.png)



## script setup实验性特性

### setup顶层写法

![image-20210821171738689](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821171738689.png)

## 认识h函数

Vue推荐在绝大数情况下**使用模板**来创建你的HTML，然后一些特殊的场景，你真的需要**JavaScript的完全编程的 能力**，这个时候你可以使用 **渲染函数** ，它比**模板更接近编译器**；

- 前面我们讲解过VNode和VDOM的改变： 
- Vue在生成真实的DOM之前，会将我们的节点转换成VNode，而VNode组合在一起形成一颗树结构，就是虚 拟DOM（VDOM）； 
- 事实上，我们之前编写的 template 中的HTML 最终也是使用渲染函数生成对应的VNode； 
- 那么，如果你想**充分的利用JavaScript的编程能力**，我们可以自己来**编写 createVNode 函数，生成对应的 VNode**；

**那么我们应该怎么来做呢？使用 h()函数：**

- h() 函数是一个用于创建 vnode 的一个函数； 
- 其实更准备的命名是 createVNode() 函数，但是为了简便在Vue将之简化为 h() 函数；

### h()函数 如何使用呢？

h()函数 如何使用呢？它接受三个参数：

![image-20210821211501955](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821211501955.png)

**注意事项：**

- 如果没有props，那么通常可以将children作为第二个参数传入； 
- 如果会产生歧义，可以将**null**作为第二个参数传入，将children作为第三个参数传入；

### h函数的基本使用

**h函数可以在两个地方使用：**

- render函数选项中； 
- setup函数选项中（setup本身需要是一个函数类型，函数再返回h函数创建的VNode）；

![image-20210821211940463](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821211940463.png)

![image-20210821212733915](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821212733915.png)

### h函数计数器案例

#### 1. render函数实现

![image-20210821214240353](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821214240353.png)



#### 2. setup函数实现

![image-20210821214316584](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821214316584.png)

### 函数组件和插槽的使用

**了解render函数的使用方式即可。不说难不难，这种方式的阅读性应该大部分人是受不了的。**

![image-20210821221823552](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821221823552.png)

![image-20210821221836619](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821221836619.png)

## jsx的babel配置

如果我们希望在**项目中使用jsx**，那么我们需要**添加对jsx的支持**：

jsx我们通常会**通过Babel来进行转换**（React编写的jsx就是通过babel转换的）； 

对于Vue来说，我们只需要在Babel中配置对应的插件即可；

**安装Babel支持Vue的jsx插件：**

```shell
npm i @vue/babel-plugin-jsx -D
```

在babel.config.js配置文件中配置插件：

```js
module.exports = {
  presets: [
    '@vue/cli-plugin-babel/preset'
  ],
    plugins:[
        "@vue/babel-plugin-jsx"
    ]
}
```

**但是笔者这里使用最新的vue脚手架的时候，发现已经支持了jsx的写法，应该是已经预装了该插件，已经内置支持该jsx写法，不需要再重新安装**

### jsx的基本使用和计数器案例

![image-20210821224756607](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821224756607.png)

![image-20210821224811395](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821224811395.png)

**效果**

![image-20210821224833337](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210821224833337.png)

```html
// Home.vue

<script>

export default {
  render() {
    return (
      // 定义好默认的组件插槽，外面使用了插槽就调用
      <div>
        <h2>哈哈Home  111！！！</h2>
        {this.$slots.default?this.$slots.default():<h3>我是插槽的默认值</h3>}
      </div>
    )
  },
}
</script>
```

```html
<!--App.vue-->

<script>
import Home from './Home.vue';
export default {
  data() {
    return {
      counter: 0
    }
  },
  render() {
    const increment = () => this.counter++;
    const decrement = () => this.counter--;
    // jsx语法
    return ( // 使用小括号括起来，方便换行，且这个括号里面的都是返回值
      // jsx里面使用变量和函数 都是使用花括号获取
      // 使用组件 必须使用导入的组件名称，比如导入的是 Home 标签也必须大写为 Home
      // 也可以在组件内使用插槽，直接使用{}传递过去，当然传递的也必须是变量，所以就 {{default:()=>{}}}
      <div>
        <h2>哈哈哈 ！！！</h2>
        <h2>当前计数：{this.counter}</h2>
        <button onClick={increment}>+1</button>
        <button onClick={decrement}>-1</button>

        <Home>
          {{ default: (props) => <h3>哈哈。。。。</h3> }}
        </Home>
      </div>
    )
  },
}
</script>
```





# Vue3高级语法补充

## 自定义指令

### 认识自定义指令

在Vue的模板语法中我们学习过各种各样的指令：v-show、v-for、v-model等等，除了使用这些指令之外，**Vue 也允许我们来自定义自己的指令。**

- 注意：在Vue中，代码的复用和抽象主要还是通过组件； 
- 通常在某些情况下，你需要对DOM元素进行底层操作，这个时候就会用到自定义指令；

**自定义指令分为两种：**

- 自定义局部指令：组件中通过 directives 选项，只能在当前组件中使用； 
- 自定义全局指令：app的 directive 方法，可以在任意组件中被使用；

**比如我们来做一个非常简单的案例：当某个元素挂载完成后可以自定获取焦点**

- 实现方式一：如果我们使用默认的实现方式； 
- 实现方式二：自定义一个 v-focus 的局部指令； 
- 实现方式三：自定义一个 v-focus 的全局指令；

#### 实现方式一：默认实现

![image-20210822202810574](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822202810574.png)

![image-20210822202901896](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822202901896.png)

#### 实现方式二：局部自定义指令

**实现方式二：自定义一个 v-focus 的局部指令**

- 这个**自定义指令**实现非常简单，我们只需要在组件选项中使用 **directives** 即可； 
- 它是一个对象，在对象中编写我们自定义指令的名称（注意：这里不需要加v-）； 
- **自定义指令有一个生命周期**，是在组件挂载后调用的 **mounted**，我们可以在其中完成操作；

![image-20210822204614559](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822204614559.png)

**注意：参数bindings可以拿到使用该指令的组件或元素上指令的参数和修饰符等信息。后面会说。**

#### 方式三：自定义全局指令

自定义一个**全局的v-focus指令**可以让我们在任何地方直接使用



![image-20210822204940163](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822204940163.png)

### 指令的生命周期

**一个指令定义的对象，Vue提供了如下的几个钩子函数：**

- **created：**在绑定元素的 attribute 或事件监听器被应用之前调用； 
- **beforeMount：**当指令第一次绑定到元素并且在挂载父组件之前调用； 
- **mounted：**在绑定元素的父组件被挂载后调用； n
- **beforeUpdate：**在更新包含组件的 VNode 之前调用； 
- **updated：**在包含组件的 VNode 及其子组件的 VNode 更新后调用； 
- **beforeUnmount：**在卸载绑定元素的父组件之前调用； 
- **unmounted：**当指令与元素解除绑定且父组件已卸载时，只调用一次；

### 指令的参数和修饰符

如果我们指令需要**接受一些参数或者修饰符**应该如何操作呢？

- info是参数的名称； 
- aaa-bbb是修饰符的名称； 
- 后面是传入的具体的值；

在我们的生命周期中，我们可以**通过 bindings 获取**到对应的内容：

![image-20210822213505316](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822213505316.png)

![image-20210822213609137](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822213609137.png)

```html
<template>
  <div>
    <h2>当前计数：{{ counter }}</h2>
    <!-- 事件修饰符和参数 aaa bbb 是修饰符 等于号右边参数 -->
    <button v-mao.aaa.bbb="'字符串毛毛！'" @click="increment">+1</button>
  </div>
</template>

<script>
import { ref, watch, onMounted } from 'vue';
export default {
  // 局部组件在 directives属性里面定义
  directives: {
    mao: {
      // 指令的生命周期
      created(el, bindings, vnode, preVnode) {
        console.log('v-mao created ......')
        console.log(el, bindings, vnode, preVnode);
        console.log("v-mao指令的参数：", bindings.value);
        // 通过 bindings的 modifiers 属性获取修饰符， modifiers是一个对象，修饰符是该对象的属性
        // 只要用了这个修饰符，值就是 true
        console.log("v-mao指令的修饰符：", bindings.modifiers);
      },
      beforeMount() {
        console.log('v-mao beforeMount ......');
      },
      mounted() {
        console.log('v-mao mounted ......');
      },
      // 只要指令所在的元素或组件发生了改变，包括属性等发生改变，都会调用这两个生命周期
      beforeUpdate() {
        console.log('v-mao beforeUpdate ......');
      },
      updated() {
        console.log('v-mao updated ......');
      },
      beforeUnmount() {
        console.log('v-mao beforeUnmount ......');
      },
      unmounted() {
        console.log('v-mao unmounted ......');
      },
    }
  },
  setup() {
    const counter = ref(0);
    const increment = () => counter.value++
    return {
      counter, increment
    }
  }

}
</script>

<style scoped>
</style>
```



### 自定义指令练习

自定义指令案例：时间戳的显示需求:

- 在开发中，**大多数情况**下**从服务器获取到的都是时间戳**； 
- 我们需要将时间戳转换成具体**格式化的时间**来展示； 
- 在**Vue2中我们可以通过过滤器**来完成； 
- 在Vue3中我们可以通过 计算属性（computed） 或者 自定义一个方法（methods） 来完成； 
- 其实我们还可以通过一个**自定义的指令**来完成；

我们来实现一个可以**自动对时间格式化的指令v-format-time：**

这里我封装了一个函数，在首页中我们只需要**调用这个函数并且传入app即可；**

#### 时间格式化指令

![image-20210822220805982](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822220805982.png)

![image-20210822220945803](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822220945803.png)



## 认识Teleport(vue3)

在组件化开发中，我们**封装一个组件A，在另外一个组件B中使用：**

- 那么组件A中template的元素，会被挂载到组件B中template的某个位置； 
- 最终我们的应用程序会形成一颗DOM树结构；

但是某些情况下，我们**希望组件不是挂载在这个组件树**上的，可能是**移动到Vue app之外的**其他位置：

- 比如移动到body元素上，或者我们有其他的div#app之外的元素上； 
- 这个时候我们就可以通过teleport来完成；

**Teleport是什么呢？**

- 它是一个Vue提供的内置组件，类似于react的Portals； 

- teleport翻译过来是心灵传输、远距离运输的意思；

**它有两个属性：**

- **to：**指定将其中的内容移动到的目标元素，可以使用选择器； 
- **disabled：**是否禁用 teleport 的功能；

#### 和组件结合使用

当然，teleport也可以和组件结合一起来使用：

我们可以在 teleport 中使用组件，并且也可以给他传入一些数据；

#### 多个teleport

**如果我们将多个teleport应用到同一个目标上（to的值相同），那么这些目标会进行合并：**

![image-20210822230012209](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822230012209.png)



## 认识Vue插件

通常我们向Vue全局添加一些功能时，会采用插件的模式，它有两种编写方式：

- 对象类型：一个对象，但是必须包含一个 install 的函数，该函数会在安装插件时执行； 
- 函数类型：一个function，这个函数会在安装插件时自动执行；

插件可以完成的**功能没有限制**，比如下面的几种都是可以的：

- 添加全局方法或者 property，通过把它们添加到 config.globalProperties 上实现； 
- 添加全局资源：指令/过滤器/过渡等； 
- 通过全局 mixin 来添加一些组件选项； 
- 一个库，提供自己的 API，同时提供上面提到的一个或多个功能；

### 插件的编写方式

#### 对象类型

![image-20210822232344537](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822232344537.png)

![image-20210822232433107](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822232433107.png)

![image-20210822232508121](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822232508121.png)





#### 函数类型

![image-20210822232803913](https://gitee.com/maolovecoding/picture/raw/master/images/web/vue/image-20210822232803913.png)



