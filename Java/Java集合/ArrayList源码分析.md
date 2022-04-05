# ArrayList原理

## ArrayList集合底层数据结构

### ArrayList集合介绍

`List`接口的可调整大小的数组实现

数组：一大初始化长度以后，就不可以发生改变



## ArrayList继承关系

### Serializable可序列化 标记性接口

```java
public interface Serializable {
}
```

实现该接口的类，可进行序列化和反序列化。可序列化类的所有子类型都是可序列化的（继承传递）。该接口仅仅是作为标识。

**序列化：**将对象的数据写入到文件

**反序列化：**将文件中的对象的数据读取出来，重新变成java对象



### Cloneable克隆 标记性接口

一个类实现该接口，用来指示 `Object.clone()`方法，该方法对应该类的实例进行字段的复制是合法的，不实现该接口的实例，如果调用对象的**clone**方法会出现异常`CloneNotSupportedException`.

克隆就是依据现有的数据，创造一份新的完全一样的数据拷贝。

```java
// 可克隆接口 调用Object.clone() 不会抛异常
public interface Cloneable {
}
```

克隆条件：

1. 实现该接口
2. 必须重新clone方法

```java
public Object clone() {
        try {
            ArrayList<?> v = (ArrayList<?>) super.clone();
            v.elementData = Arrays.copyOf(elementData, size);
            v.modCount = 0;
            return v;
        } catch (CloneNotSupportedException e) {
            // this shouldn't happen, since we are Cloneable
            throw new InternalError(e);
        }
    }
```

**super.clone()，调用的是Object类的方法。native表示调用本地方法**

该方法的具体是实现是调用c写的实现以及结合操作系统等。

```java
protected native Object clone() throws CloneNotSupportedException;
```



## RandomAccess快速访问 标记接口

标记接口由`List`实现使用，表明支持快速（通常为恒定时间）随机访问

此接口的目的主要是允许通过算法更改其行为，以便应用于 **随机访问列表**

或**顺序访问列表**时提供良好的性能。

**list实现该接口，for循环（随机访问）的效率一般来说是高于iterator（顺序访问）的效率的**

**注意：LinkedList没有实现该接口**

在LinkedList集合中，for循环的效率远低于迭代器遍历方式的。所以当我们对集合数据进行遍历的时候，**可以判断是ArrayList还是LinkedList集合，选择不同的遍历方式。**

也可以根据是否实现RandomAccess接口，来选择for循环还是迭代器遍历



**迭代器遍历方式iterator，以及增强for内部实现也是迭代器的方式**



### AbstractList 

该抽象类是list接口的基本骨架实现





## 源码分析

























