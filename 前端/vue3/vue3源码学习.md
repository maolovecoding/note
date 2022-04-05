# Vue3源码学习

## 真实的DOM渲染

我们传统的前端开发中，我们是编写自己的HTML，最终被渲染到浏览器上的，那么它是什么样的过程呢？

![image-20210825220723339](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210825220723339.png)

### 虚拟DOM的优势

**目前框架都会引入虚拟DOM来对真实的DOM进行抽象，这样做有很多的好处：**

首先是可以对真实的元素节点进行抽象，抽象成VNode（虚拟节点），这样方便后续对其进行各种操作：

- p因为对于直接操作DOM来说是有很多的限制的，比如diff、clone等等，但是使用JavaScript编程语言来操作这 些，就变得非常的简单； 
- 我们可以使用JavaScript来表达非常多的逻辑，而对于DOM本身来说是非常不方便的；

其次是方便实现跨平台，包括你可以将VNode节点渲染成任意你想要的节点

- 如渲染在canvas、WebGL、SSR、Native（iOS、Android）上； 
- 并且Vue允许你开发属于自己的渲染器（renderer），在其他的平台上渲染；

### 虚拟DOM的渲染过程

![image-20210825220744562](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210825220744562.png)



### 三大核心系统

事实上Vue的源码包含三大核心：

- Compiler模块：编译模板系统； 
- Runtime模块：也可以称之为Renderer模块，真正渲染的模块； 
- Reactivity模块：响应式系统；

![image-20210825220828228](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210825220828228.png)

### 三大系统协同工作

三个系统之间如何协同工作呢：

![image-20210825220853441](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210825220853441.png)

### 实现Mini-Vue

这里我们实现一个简洁版的Mini-Vue框架，该Vue包括三个模块：

- 渲染系统模块；
- 可响应式系统模块； 
- 应用程序入口模块；

#### 渲染系统实现

渲染系统，该模块主要包含三个功能：

- 功能一：h函数，用于返回一个VNode对象； 
- 功能二：mount函数，用于将VNode挂载到DOM上； 
- 功能三：patch函数，用于对两个VNode进行对比，决定如何处理新的VNode；

#### h函数 – 生成VNode

h函数的实现：直接返回一个VNode对象即可

```js
// 定义 h 函数
/**
 * 
 * @param {*} tag 元素名
 * @param {*} props 属性
 * @param {*} children 子元素
 */
const h = (tag, props, children) => {
  // vnode -> js对象 -> {}
  return {
    tag,
    props,
    children,
    // vnode -> el -> 记录一份真实dom
    // el
  }
}
```

#### Mount函数 – 挂载VNode

**mount函数的实现：**

- 第一步：根据tag，创建HTML元素，并且存储 到vnode的el中；

- 第二步：处理props属性 
  - 如果以on开头，那么监听事件； 
  - 普通属性直接通过 setAttribute 添加即可；

- 第三步：处理子节点 
  - 如果是字符串节点，那么直接设置 textContent/innerHTML； 
  - 如果是数组节点，那么遍历调用 mount 函 数

```js
// 挂载功能
/**
 * 
 * @param {*} vnode 虚拟dom
 * @param {*} container 挂载的容器
 */
const mount = (vnode, container) => {
  // vnode -> element
  // 1. 创建出真实的元素，并在vnode上保留一份 el
  const el = vnode.el = document.createElement(vnode.tag);
  // 2. 处理props
  if (vnode.props) {
    for (const key in vnode.props) {
      const value = vnode.props[key];
      // 以on开头 做函数的事件监听
      if (key.startsWith("on")) {
        el.addEventListener(key.slice(2).toLowerCase(), value);
      } else {
        // 设置属性
        el.setAttribute(key, value);
      }
    }
  }
  // 3. 处理children
  if (vnode.children) {
    if (typeof vnode.children === "string") {
      // 字符串
      el.innerHTML = vnode.children;
    } else if (vnode.children instanceof Array) {// 数组进行类型检测 拿到的是object
      // 数组
      vnode.children.forEach(childVnode => {
        mount(childVnode, el);
      })
    }
  }
  if (!container) {
    // 没有容器
    throw new Error('container is must be exist');
  }
  // 将el 挂载到container容器上
  else if (typeof container === "string") {
    // 是字符串，则传进来的是选择器
    document.querySelector(container).appendChild(el);
  } else {
    // 是已经选中的元素
    container.appendChild(el);
  }
}
```

#### Patch函数 – 对比两个VNode

patch函数的实现，分为两种情况:

- n1和n2是不同类型的节点： 
  - 找到n1的el父节点，删除原来的n1节点的el； 
  - 挂载n2节点到n1的el父节点上；

- n1和n2节点是相同的节点： 
  - 处理props的情况 
    - 先将新节点的props全部挂载到el上； 
    - 判断旧节点的props是否不需要在新节点上，如果不需要，那么删除对应的属性；
  - 处理children的情况 
    - 如果新节点是一个字符串类型，那么直接调用 el.textContent = newChildren； 
    - 如果新节点不同一个字符串类型：
      - 旧节点是一个字符串类型
        - 将el的textContent设置为空字符串； 
        - 就节点是一个字符串类型，那么直接遍历新节点，挂载到el上；
      - 旧节点也是一个数组类型
        - 取出数组的最小长度； 
        - 遍历所有的节点，新节点和旧节点进行path操作； 
        - 如果新节点的length更长，那么剩余的新节点进行挂载操作； 
        - 如果旧节点的length更长，那么剩余的旧节点进行卸载操作；

```js
// patch 函数
/**
 * 
 * @param {*} n1 vnode1
 * @param {*} n2 vnode2
 */
const patch = (n1, n2) => {
  // 先判断类型(元素)是否相同
  if (n1.tag !== n2.tag) {
    // 类型不同 肯定不同了
    // 移除vnode1，换成vnode2 通过父节点移除
    const parent = n1.el.parentElement;
    parent.removeChild(n1.el);
    // 创建新的vnode2节点，并挂载到父节点上
    mount(n2, parent);
  } else {
    // 类型相同
    // 1. 取出元素（element）对象，并且在n2中也进行保存el
    const el = n2.el = n1.el;
    // 2. 处理props
    // 如果props为null 则默认为空对象
    const oldProps = n1.props || {};
    const newProps = n2.props || {};
    // 2.1 获取所有的newProps 添加到el的属性上
    for (const key in newProps) {
      // 看是否有相同的属性且值相同
      const oldValue = oldProps[key];
      const newValue = newProps[key];
      if (oldValue !== newValue) {
        // 事件处理 on开头
        if (key.startsWith("on")) {
          el.addEventListener(key.slice(2).toLowerCase(), newValue);
        } else {
          // 不相同才需要进行设置
          el.setAttribute(key, newValue);
        }
      }
    }
    // 2.2 删除旧的props
    for (const key in oldProps) {
      // 在新的属性里面不存在的旧属性都移除
      if (!(key in newProps)) {
        // 移除事件
        if (key.startsWith("on")) {
          el.removeEventListener(key.slice(2).toLowerCase(), oldProps[key]);
        } else {
          // 移除属性
          el.removeAttribute(key);
        }
      }
    }

    // 3. 处理children
    const oldChildren = n1.children || [];
    const newChildren = n2.children || [];
    if (typeof newChildren === "string") {
      // 新子节点是字符串类型数据 且内容不同 直接完成内容替换
      if (typeof oldChildren === "string") {
        if (newChildren !== oldChildren) // 有需要的话 可以自行加更多的边界判断
          el.textContent = newChildren;
      } else {
        // oldChildren不是字符串类型
        el.innerHTML = newChildren;
      }
    }
    // 不是字符串类型 则在这里就是数组类型 对象形式我们不考虑
    else {
      if (typeof oldChildren === "string") {
        // string vs array
        el.innerHTML = "";
        // 遍历newChildren 挂载 子节点
        newChildren.forEach(child => {
          mount(child, el);
        })
      } else {
        // array vs array 都是数组
        // old: [v1,v2,v3,v5,v7]
        // new: [v3,v5,v6,v8,v9,v11]
        // diff 算法 进行比对（这里不考虑key）

        // 拿到新旧child数组较短的那个
        const commonLength = Math.min(oldChildren.length, newChildren.length);
        // 1. 遍历 将共有长度的那些节点进行patch
        for (let i = 0; i < commonLength; i++) {
          // 循环进行patch操作
          patch(oldChildren[i], newChildren[i]);
        }

        // 2. 旧child数组的长度更长，则删除多余的元素
        if (oldChildren.length > commonLength) {
          oldChildren.slice(commonLength).forEach(child => {
            // 移除原来挂载的子元素
            el.removeChild(child.el);
          })
        } else if (newChildren > commonLength) {
          // 3. 新的child的数组长度更长 则做新增操作
          // 截取数组后面多出来的部分进行遍历，挂载
          newChildren.slice(commonLength).forEach(child => {
            // 挂载
            mount(child, el);
          })
        }
      }
    }
  }
}
```

#### 测试

代码如下：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <div id="app"></div>
  <script src="./renderer.js"></script>
  <script>
    const vnode = h("div", { class: 'mao', name: '111' }, [
      h("h2", {}, "你好！！！！"),
      h("h2", null, "你好！当前计数：100"),
      h("button", { onClick: function () { } }, "+1")
    ]);
    // 通过mount函数 将vnode 挂载到div#app上
    mount(vnode, "#app")

    // 创建新的vnode 这个作为更新数据后的vnode
    const vnode2 = h("div", { class: 'codermao', data: "mao" }, [
    h("h2", {}, "你好！！！！"),  
    h("h2", { style: "color:red" }, "哈哈哈")
    ]);
    // 新的vnode和旧的vnode进行diff算法的比对，找出不同
    // patch函数进行比对
    setTimeout(() => { patch(vnode, vnode2); }, 3000);
  </script>
</body>

</html>
```

```js
// 定义 h 函数
/**
 * 
 * @param {*} tag 元素名
 * @param {*} props 属性
 * @param {*} children 子元素
 */
const h = (tag, props, children) => {
  // vnode -> js对象 -> {}
  return {
    tag,
    props,
    children,
    // vnode -> el -> 记录一份真实dom
    // el
  }
}

// 挂载功能
/**
 * 
 * @param {*} vnode 虚拟dom
 * @param {*} container 挂载的容器
 */
const mount = (vnode, container) => {
  // vnode -> element
  // 1. 创建出真实的元素，并在vnode上保留一份 el
  const el = vnode.el = document.createElement(vnode.tag);
  // 2. 处理props
  if (vnode.props) {
    for (const key in vnode.props) {
      const value = vnode.props[key];
      // 以on开头 做函数的事件监听
      if (key.startsWith("on")) {
        el.addEventListener(key.slice(2).toLowerCase(), value);
      } else {
        // 设置属性
        el.setAttribute(key, value);
      }
    }
  }
  // 3. 处理children
  if (vnode.children) {
    if (typeof vnode.children === "string") {
      // 字符串
      el.innerHTML = vnode.children;
    } else if (vnode.children instanceof Array) {// 数组进行类型检测 拿到的是object
      // 数组
      vnode.children.forEach(childVnode => {
        mount(childVnode, el);
      })
    }
  }
  if (!container) {
    // 没有容器
    throw new Error('container is must be exist');
  }
  // 将el 挂载到container容器上
  else if (typeof container === "string") {
    // 是字符串，则传进来的是选择器
    document.querySelector(container).appendChild(el);
  } else {
    // 是已经选中的元素
    container.appendChild(el);
  }
}


// patch 函数
/**
 * 
 * @param {*} n1 vnode1
 * @param {*} n2 vnode2
 */
const patch = (n1, n2) => {
  // 先判断类型(元素)是否相同
  if (n1.tag !== n2.tag) {
    // 类型不同 肯定不同了
    // 移除vnode1，换成vnode2 通过父节点移除
    const parent = n1.el.parentElement;
    parent.removeChild(n1.el);
    // 创建新的vnode2节点，并挂载到父节点上
    mount(n2, parent);
  } else {
    // 类型相同
    // 1. 取出元素（element）对象，并且在n2中也进行保存el
    const el = n2.el = n1.el;
    // 2. 处理props
    // 如果props为null 则默认为空对象
    const oldProps = n1.props || {};
    const newProps = n2.props || {};
    // 2.1 获取所有的newProps 添加到el的属性上
    for (const key in newProps) {
      // 看是否有相同的属性且值相同
      const oldValue = oldProps[key];
      const newValue = newProps[key];
      if (oldValue !== newValue) {
        // 事件处理 on开头
        if (key.startsWith("on")) {
          el.addEventListener(key.slice(2).toLowerCase(), newValue);
        } else {
          // 不相同才需要进行设置
          el.setAttribute(key, newValue);
        }
      }
    }
    // 2.2 删除旧的props
    for (const key in oldProps) {
      // 在新的属性里面不存在的旧属性都移除
      if (!(key in newProps)) {
        // 移除事件
        if (key.startsWith("on")) {
          el.removeEventListener(key.slice(2).toLowerCase(), oldProps[key]);
        } else {
          // 移除属性
          el.removeAttribute(key);
        }
      }
    }

    // 3. 处理children
    const oldChildren = n1.children || [];
    const newChildren = n2.children || [];
    if (typeof newChildren === "string") {
      // 新子节点是字符串类型数据 且内容不同 直接完成内容替换
      if (typeof oldChildren === "string") {
        if (newChildren !== oldChildren) // 有需要的话 可以自行加更多的边界判断
          el.textContent = newChildren;
      } else {
        // oldChildren不是字符串类型
        el.innerHTML = newChildren;
      }
    }
    // 不是字符串类型 则在这里就是数组类型 对象形式我们不考虑
    else {
      if (typeof oldChildren === "string") {
        // string vs array
        el.innerHTML = "";
        // 遍历newChildren 挂载 子节点
        newChildren.forEach(child => {
          mount(child, el);
        })
      } else {
        // array vs array 都是数组
        // old: [v1,v2,v3,v5,v7]
        // new: [v3,v5,v6,v8,v9,v11]
        // diff 算法 进行比对（这里不考虑key）

        // 拿到新旧child数组较短的那个
        const commonLength = Math.min(oldChildren.length, newChildren.length);
        // 1. 遍历 将共有长度的那些节点进行patch
        for (let i = 0; i < commonLength; i++) {
          // 循环进行patch操作
          patch(oldChildren[i], newChildren[i]);
        }

        // 2. 旧child数组的长度更长，则删除多余的元素
        if (oldChildren.length > commonLength) {
          oldChildren.slice(commonLength).forEach(child => {
            // 移除原来挂载的子元素
            el.removeChild(child.el);
          })
        } else if (newChildren > commonLength) {
          // 3. 新的child的数组长度更长 则做新增操作
          // 截取数组后面多出来的部分进行遍历，挂载
          newChildren.slice(commonLength).forEach(child => {
            // 挂载
            mount(child, el);
          })
        }
      }
    }
  }
}
```

![image-20210825222303539](https://gitee.com/maolovecoding/picture/raw/master/images/web/wei_chat/image-20210825222303539.png)





### 依赖收集系统

```js
// 收集依赖
/**
 * 收集依赖的类
 */
class Dep {
  constructor() {
    // 发布订阅模式
    // 订阅者 将其都加入进来
    this.subscribers = new Set();
  }
  // 收集副作用
  // addEffect(effect) {
  //   this.subscribers.add(effect);
  // }

  /* 自动收集副作用 */
  depend() {
    if (activeEffect) {
      this.subscribers.add(activeEffect)
    }
  }

  // 通知，进行回调执行
  notify() {
    this.subscribers.forEach(effect => {
      effect();
    })
  }
}

let activeEffect = null;
/**
 * 帮助收集依赖
 * @param {*} effect 数据发送改变后要执行的副作用函数
 */
function watchEffect(effect) {
  activeEffect = effect;
  // 用原始数据执行一次
  effect()
  activeEffect = null;
}
/* 
  Map: {key:value} key: 字符串 value任意
  WeakMap: key 是一个对象，弱引用  value任意
*/
const targetMap = new WeakMap();

/**
 * 
 * @param {*} target 目标对象
 * @param {*} key 目标对象的属性
 */
function getDep(target, key) {
  // 1. 根据目标对象 取出对应的Map对象
  let depsMap = targetMap.get(target);
  if (!depsMap) {
    depsMap = new Map();
    targetMap.set(target, depsMap);
  }
  // 取出具体的依赖对象
  let dep = depsMap.get(key);
  if (!dep) {
    dep = new Dep();
    depsMap.set(key, dep);
  }
  return dep;
}
```

### 数据劫持

#### 响应式系统Vue2实现

```js
**
 * vue2对raw进行数据劫持
 * @param {*} raw 原始数据
 */
function reactive(raw) {
  Object.keys(raw).forEach(key => {
    // 收集依赖
    const dep = getDep(raw, key);
    let value = raw[key];
    // 劫持的对象，对象的属性，属性的描述
    Object.defineProperty(raw, key, {
      // 获取数据 会执行get函数
      get() {
        // 添加依赖
        dep.depend()
        return value;
      },
      // 设置数据
      set(newValue) {
        if (value !== newValue) {
          value = newValue;
          dep.notify();
        }
      }
    })
  })
  return raw;
}
```

#### 响应式系统Vue3实现

```js
**
 * vue3对raw进行数据劫持
 * @param {*} raw 原始数据
 */
function reactive(raw) {
  // 劫持的对象 对代理对象的数据劫持
  return new Proxy(raw, {
    // 获取数据时调用
    // target 参数 就是 我们劫持的对象 raw
    get(target, key) {
      const dep = getDep(target, key);
      dep.depend();
      // 这里没有考虑属性值还是对象的情况
      return target[key];
    },
    // 设置数据时调用
    set(target, key, newValue) {
      const dep = getDep(target, key);
      target[key] = newValue;
      dep.notify();
    }
  });
}
```

#### 响应式数据的测试

```js
// ----------------测试----------------------

// 数据
const info = reactive({ name: '毛毛', age: 21 })
// 会自动收集依赖
watchEffect(() => {
  console.log(info.age * 2);
})
// 数据发送改变 自动执行副作用函数
info.age++;

let obj  = reactive({name:'hah ',counter:1});
watchEffect(()=>{
  console.log("name:",obj.name);
})
watchEffect(()=>{
  console.log("counter:",obj.counter);
})
obj.counter++
```



#### 为什么Vue3选择Proxy呢？

Object.definedProperty 是劫持对象的属性时，如果新增元素：

那么Vue2需要再次 调用definedProperty，而 Proxy 劫持的是整个对象，不需要做特殊处理；

修改对象的不同:

- 使用 defineProperty 时，我们修改原来的 obj 对象就可以触发拦截； 
- 而使用 proxy，就必须修改代理对象，即 Proxy 的实例才可以触发拦截；

Proxy 能观察的类型比 defineProperty 更丰富

- has：in操作符的捕获器； 
- deleteProperty：delete 操作符的捕捉器； 
- 等等其他操作；

### 框架外层API设计

这样我们就知道了，从框架的层面来说，我们需要 有两部分内容：

- createApp用于创建一个app对象；

- 该app对象有一个mount方法，可以将根组件挂 载到某一个dom元素上；

```js
/**
 * 
 * @param {*} rootComponent 根组件（元素）
 * @returns 
 */
function createApp(rootComponent) {
  return {
    mount(selector) {
      const container = document.querySelector(selector);
      // 是否挂载过
      let isMounted = false;
      // 数据未更新前的vnode
      let oldVNode = null;
      watchEffect(() => {
        if (!isMounted) {
          // 没有挂载过
          //  进行挂载
          oldVNode = rootComponent.render();
          mount(oldVNode, container);
          isMounted = true;
        } else {
          // 已经挂载过
          // 进行patch比对
          const newVNode = rootComponent.render()
          patch(oldVNode, newVNode);
          // 保存这次新的vnode
          oldVNode = newVNode;
        }
      })
    }
  }
}
```

### 测试

```js
// 根组件
    const App = {
      data: reactive({
        counter: 0
      }),
      render() {
        return h("div", { style: "color:red" }, [
          h("h2", null, `当前计数：${this.data.counter}`),
          h("button", {
            onClick: () => {
              this.data.counter++
            }
          }, "+1")
        ])
      }
    }
    // 挂载根组件
    const app = createApp(App)
    app.mount("#app")
```

