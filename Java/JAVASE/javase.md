# JAVASE



## 常用API

### Math 类

1. **abs(int a) 返回参数的绝对值**
2. **ceil(double a) 向上取整**
3. **floor(double a) 向下取整**
4. **round(float a) 四舍五入**
5. **pow(double a,double b) 返回 a 的 b 次方**
6. **random() 无参数，返回值为 double 类型的正值，范围在 [0.0, 1.0) 之间**

### System 类

系统类。

1. void  exit(int status) 终止当前虚拟机的执行，参数为 0 表示正常停止，非 0 表示异常
2. long currentTimeMillis() 返回当前时间，单位是毫秒
3. arraycopy(数据源数组，起始索引，目标数组，起始索引，拷贝个数【数量，长度】)  数组的copy，将源数组的数据拷贝到目标数组

### Object类

**构造方法：**public Object()

子类的构造方法 默认访问的是父类的无参构造。**因为：** 他们的顶级父类中只有无参构造方法

1. 重写 toString() 方法
2. equals() 方法  比较对象是否相等，底层比较的是地址值是否相等。

**StringBuilder类 没有重写 equals 方法**

### Objects类

无构造方法。

**toString(对象) 方法调用的还是对象本身重写的toString()方法**

| 方法名                                          | 说明                                                     |
| ----------------------------------------------- | -------------------------------------------------------- |
| public static String toString(对象)             | 返回参数中对象的字符串表示形式                           |
| public static String toString(对象，默认字符串) | 返回对象的字符串表示形式（如果对象为空，返回默认字符串） |
| public static Boolean isNull(对象)              | 判断对象是否为空                                         |
| public static Boolean nonNull(对象)             | 判断对象是否不为空                                       |



### BigDecimal类

**可以用来进行精确计算。**

例如 10.0 % 3.0 = 3.3333333333333335

最后并不会一直循环 3，而是精度发生损失

使用 BigDecimal类就不会发生这种情况

#### 构造方法：

| 方法名称                                  | 说明          |
| ----------------------------------------- | ------------- |
| public BigDecimal(【数字，字符串都可以】) |               |
| public BigDecimal(double val)             | 参数为double  |
| public BigDecimal(String val)             | 参数为 String |

#### BigDecimal类的常用方法

|                      方法名                      | 说明 |
| :----------------------------------------------: | :--: |
|   public BigDecimal add(另一个bigDecimal对象)    | 加法 |
| public BigDecimal subtract(另一个bigDecimal对象) | 减法 |
| public BigDecimal multiply(另一个bigDecimal对象) | 乘法 |
|  public BigDecimal devide(另一个bigDecimal对象)  | 除法 |

**这些加减乘除方法的返回值，就是我们计算出来的结果**。

**注意：**如果想要进行精确运算，需要向构造方法传递的参数的值的类型是字符串类型。这样结果就是精确值。

```java
BigDecimal bd1 = new BigDecimal("0.1");
BigDecimal bd2 = new BigDecimal("0.1");
BigDecimal bd = bd1.add(bd2);
System.out.println(bd); // 0.3
System.out.println(0.1+0.2);// 0.30000000000000004
```

#### 特殊方法

|                            方法名                            | 说明 |
| :----------------------------------------------------------: | :--: |
| public BigDecimal devide(另一个bigDecimal对象，小数点精确到后多少位，舍入模式【进一法 BigDecimal.ROUND_UP ，去尾法 BigDecimal.ROUND_FLOOR，四舍五入法  BigDecimal.ROUND_HALF_UP】) | 除法 |

#### 总结

1. BigDecimal类是用来进行精确计算的类
2. 创建 BigDecimal 的对象，构造方法尽量选参数类型为字符串的
3. 四则运算中的除法，如果除不尽尽量使用 device的有三个参数的那个特殊方法



#### 数组的高级操作

##### 排序算法：

###### 快速排序

```java
 public static void quiteSort(int[] arr, int left, int right) {
        if (right < left) {
            return;
        }
        int l = left;
        int r = right;
//    计算出基准数
        int baseNumber = arr[l];
        while (left != right) {
//            从右边开始找比基准数小的
            while (arr[right] >= baseNumber && right > left) {
                right--;
            }
//            从左边找比基准数大的
            while (arr[left] <= baseNumber && right > left) {
                left++;
            }
//            交换位置
            int temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
        }
//        基准数归位
        int temp = arr[left];
        arr[left] = arr[l];
        arr[l] = temp;
        quiteSort(arr, l, left-1);
        quiteSort(arr, left+1, r);
    }
```

### Arrays 数组工具类

| 方法名                                  | 说明                                                 |
| --------------------------------------- | ---------------------------------------------------- |
| binarySearch(int[] a, int key) 二分查找 | 二分查找，参数一：要查找的数组，参数二：要查找的元素 |
| sort(int[] arr)                         | 对数组进行排序，底层也是采用快速排序                 |

**注意：binarySearch 方法使用**

1. 数组必须要先进行排序

2. 如果要查找的元素存在，返回的就是这个元素所在的索引

3. 元素不存在，返回的是 ( -插入点 -1)

   插入点： 如果这个元素在数组中，它应该在哪个索引的位置上

### 时间相关操作

#### Date类

Date 代表了一个特定的时间，精确到毫秒

| 方法名                 | 说明                                               |
| ---------------------- | -------------------------------------------------- |
| public Date()          | 创建一个Date对象，表示默认的时间（也就是当前时间） |
| public Date(long date) | 创建该对象，表示指定时间                           |

```java
//        创建当前电脑的当前时间
        Date date = new Date();
        System.out.println(date); // Sun May 09 13:14:43 CST 2021
//        创建一个指定时间,表示从计算机原点（1970年1月1日0时开始）所经过的时间（毫秒）
        Date date1 = new Date(1111111111111L);
        System.out.println(date1); // Fri Mar 18 09:58:31 CST 2005
```

**成员方法**

| 方法                           | 说明                 |
| ------------------------------ | -------------------- |
| public long getTime()          | 获取时间对象的毫秒值 |
| public void setTime(long time) | 设置时间，传递毫秒值 |

```java
//        获取当前时间的毫秒值
        System.out.println(date.getTime()); // 1620537736694
//        设置对象自身所代表的时间。参数为毫秒值
        date.setTime(111111111111L);
        System.out.println(date);// Tue Jul 10 08:11:51 CST 1973
        System.out.println(date.getTime()); // 111111111111
```

#### SimpleDateFormat 日期格式化类

对 Date对象，**进行格式化和解析**

格式化： date 对象 转换为我们习惯 阅读的日期格式

解析： 将一个我们事先约定好的日期格式的某个字符串进行解析为一个 Date对象

“yyyy-MM-dd HH:mm:ss SSS”  ----------------------》 “2020-11-11 11:11:11 111”

| 方法名                                  | 说明                         |
| --------------------------------------- | ---------------------------- |
| public SimpleDateFormat()               | 采用默认的格式构造该对象     |
| public SimpleDateFormat(String pattern) | 使用指定的格式构造对象       |
| 格式化 format(Date date)                | 将日期对象格式化为字符串     |
| 解析 parse(String source)               | 将给定的字符串解析为日期对象 |

```java
Date date = new Date();
        SimpleDateFormat sdf = new SimpleDateFormat();
        System.out.println(sdf.format(date)); // 21-5-9 下午1:53
        try {
            System.out.println(sdf.parse("21-5-9 下午2:49")); // Sun May 09 14:49:00 CST 2021
        } catch (ParseException e) {
            e.printStackTrace();
        }
        SimpleDateFormat sdf2 = new SimpleDateFormat("YYYY-MM-dd");
        System.out.println(sdf.format(date));
        try {
            System.out.println(sdf2.parse("2048-02-01"));
        } catch (ParseException e) {
            e.printStackTrace();
        }
```

#### JDK8 新增时间类

<span style='color:red'>**重要**</span>：**这次Java引入的时间类非常成功**

- LocalDate 表示日期（年月日）
- LocalTime 表示时间（时分秒）
- LocalDateTime 表示时间 + 日期（年月日时分秒）

**三个方法基本类似**。这里以用的最多的 LocalDateTime 为主

**LocalDateTime**

| 方法名                                                 | 说明                                                         |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| public static LocalDateTime now()                      | 获取当前系统时间                                             |
| public static LocalDateTime of(年，月，日，时，分，秒) | 使用指定年月日时分秒初始化一个LocalDateTime对象（参数都是int类型） |

```java
//        now方法 获取当前时间
        LocalDateTime ldt = LocalDateTime.now();
        System.out.println(ldt); // 2021-05-09T15:04:46.369
//      获取指定时间的 该时间对象
        LocalDateTime of = LocalDateTime.of(2021, 5, 9, 12, 12, 12);
        System.out.println(of); //  2021-05-09T12:12:12
```

| 方法名(这里的方法都需要通过对象调用，实例方法) | 说明                        |
| ---------------------------------------------- | --------------------------- |
| public int getYear()                           | 获取年                      |
| public int getMonthValue()                     | 获取月份（1-12）            |
| public int getDayOfMonth()                     | 获取月份中的第几天（1-31）  |
| public int getDayOfYear()                      | 获取一年中的第几天（1-366） |
| public DayOfWeek getDayOfWeek()                | 获取星期（星期几）          |
| public int getMinute()                         | 获取分钟                    |
| public int getHour()                           | 获取小时                    |

```java
        LocalDateTime localDateTime = LocalDateTime.now();
//        年
        int year = localDateTime.getYear();
//        月份
        int monthValue = localDateTime.getMonthValue();
//        该月的第几天
        int dayOfMonth = localDateTime.getDayOfMonth();
//        该年的第几天
        int dayOfYear = localDateTime.getDayOfYear();
//        这个星期的第几天
        DayOfWeek dayOfWeek = localDateTime.getDayOfWeek();
//        分
        int minute = localDateTime.getMinute();
//       秒
        int second = localDateTime.getSecond();
//        小时
        int hour = localDateTime.getHour();
        System.out.println(year+ " "+monthValue+ " "+dayOfMonth+ " "+dayOfYear+ " "+dayOfWeek+ " "+minute+ " "+hour+ " "+second);
```

##### LocalDateTime 转换方法

**LocalDateTime**：

- 如果只需要日期： 转换为 LocalDate  .只保留年月日
- 如果只需要时间： 转换为 LocalTime   只保留时分秒

| 方法名                         | 说明                      |
| ------------------------------ | ------------------------- |
| public LocalDate toLocalDate() | 转换为一个LocalDate对象   |
| public LocalTime toLocalTime() | 转换为一个 LocalTime 对象 |

```java
        LocalDateTime localDateTime = LocalDateTime.of(2021, 5, 9, 11, 11, 11);
        // 转换为 localDate
        LocalDate localDate = localDateTime.toLocalDate();
        System.out.println(localDate); // 2021-05-09
//        转换为 LocalTime对象
        LocalTime localTime = localDateTime.toLocalTime();
        System.out.println(localTime); // 11:11:11
```

##### LocalDateTime 格式化和解析

| 方法名                                                   | 说明                                        |
| -------------------------------------------------------- | ------------------------------------------- |
| public String format(指定格式)                           | 把一个LocalDateTime格式化成为一个字符串     |
| public LocalDateTime parse(要解析的日期字符串，解析格式) | 把一个日期字符串解析为一个LocalDateTime对象 |

**注意： 在 JDK7及其之前，我们使用的日期格式化器是 ： SimpleDateFormat，在JDK8及之后，我们使用的日期格式化器： DateTimeFormatter**

DateTimeFormatter类的方法： 生成一个日期格式化器

| 方法名                                                    |                                                              |
| --------------------------------------------------------- | ------------------------------------------------------------ |
| public static DateTimeFormatter ofPattern(String pattern) | 使用指定的日期模板获取一个日期格式化器 DatetimeFormatter 对象 |

```java
        LocalDateTime localDateTime = LocalDateTime.of(2021, 5, 9, 11, 11, 11);
//        获取日期格式化对象 使用format方法
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        String format = localDateTime.format(dateTimeFormatter);
        System.out.println(format); // 2021-05-09 11:11:11
        String str = "2021-05-09 12:12:12";
//        解析字符串到日期对象的方法
        LocalDateTime parse = LocalDateTime.parse(str, dateTimeFormatter);
        System.out.println(parse);// 2021-05-09T12:12:12
```



##### LocalDateTime 增加和减少时间的方法（plus类型方法）

底层是采用返回新的对象的方式。也就是获取原时间对象相应的时间然后对其进行加减操作，返回新的时间对象。

**增加减少（年，月，日，时，分，秒，周）**方法雷同。

| 方法名                                       | 说明                                                |
| -------------------------------------------- | --------------------------------------------------- |
| public LocalDateTime plusYears(long years)   | 添加和减少年（值的正负决定）,返回值是修改之后的结果 |
| public LocalDateTime plusMonths(long months) | 添加和减少月份                                      |
| public LocalDateTime plusWeeks(long weeks)   | 添加或者减去周                                      |



```java
        LocalDateTime localDateTime = LocalDateTime.of(2021, 5, 9, 11, 11, 11);
//       在原有的时间上，减去一年,返回值是我们减去一年以后的那个时间，才是我们需要的
        LocalDateTime localDateTime1 = localDateTime.plusYears(-1);
        System.out.println(localDateTime); // 2021-05-09T11:11:11
        System.out.println(localDateTime1); // 2020-05-09T11:11:11
//        添加一周
        LocalDateTime localDateTime2 = localDateTime.plusWeeks(1);
        System.out.println(localDateTime2); // 2021-05-16T11:11:11
```



##### LocalDateTime 减少和增加时间的方法（minus类型方法）

**和plus类型方法刚好相反，这个是正数表示减少，负数表示增加**

| 方法名                                      | 说明           |
| ------------------------------------------- | -------------- |
| public LocalDateTime minusYears(long years) | 减少或者增加年 |



##### LocalDateTime 修改时间的方法（with类型的方法）

**传递的参数，不能超过应该存在的返回，比如时间小时不能超过24小时**

| 方法名                                              | 说明                             |
| --------------------------------------------------- | -------------------------------- |
| public LocalDateTime withYear(int year)             | 直接修改年                       |
| public LocalDateTime withMonth(int month)           | 直接修改月                       |
| public LocalDateTime withDayOfMonth(int dayofmonth) | 直接修改日期（一个月中的第几天） |
| public LocalDateTime withDayOfYear(int dayofYear)   | 直接修改日期（一年中的第几天）   |
| public LocalDateTime withHour(int hour)             | 直接修改小时                     |
| public LocalDateTime withHour(int hour)             | 直接修改分钟                     |
| public LocalDateTime withSecond(int second)         | 直接修改秒                       |

```java
        LocalDateTime localDateTime = LocalDateTime.of(2021, 5, 9, 11, 11, 11);
//        修改年
        LocalDateTime localDateTime1 = localDateTime.withYear(2020);
        System.out.println(localDateTime1); // 2020-05-09T11:11:11
//        修改小时
        LocalDateTime localDateTime2 = localDateTime1.withHour(20);
        System.out.println(localDateTime2); //  2020-05-09T20:11:11

```

#### 时间间隔对象 Period

| 方法名                                                       | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| public static Period between(开始时间（都是LocalDate对象），结束时间) | 计算两个时间点直接的间隔                                     |
| public int getYears()                                        | 获取这期间的总年份                                           |
| public int getMonths() / getDays()                           | 获取这期间的月数（是两个月份之差的绝对值，相当于在同一年进行运算月份，当然需要计算一下天在内）/天数 |
| public long toTotalMonths()                                  | 获取此期间的总月数                                           |

```java
        Period between = Period.between(LocalDate.of(2020, 12, 12), LocalDate.of(2220, 11, 11));
//        获取期间的年数
        System.out.println(between.getYears()); // 199
//        获取月数(直接相当于月份相减的绝对值)
        System.out.println(between.getMonths()); // 10
//        直接相当于
        System.out.println(between.getDays()); // 30
//        获取总月数
        System.out.println(between.toTotalMonths()); // 2398
```

##### Duration 

Duration类表示秒或纳秒时间间隔，适合处理较短的时间，需要更高的精确性。我们能使用between()方法比较两个瞬间的差：

**间隔的时间差可以为负值哦**

| 方法名                                                       | 说明                     |
| ------------------------------------------------------------ | ------------------------ |
| public static Duration between(开始时间（可以是LocalDate，LocalTime，LocalDateTime等对象），结束时间) | 计算两个时间点之间的间隔 |
| public long getSeconds()                                     | 获取此时时间间隔的秒     |
| public int toMills()                                         | 获得此时间间隔的毫秒     |
| public int toNanos()                                         | 获得此时间间隔的纳秒     |

```java
        Duration between = Duration.between(LocalDateTime.of(2020,2,2,12, 12, 12), LocalDateTime.of(2020,12,12,11, 11, 11));
//        间隔的秒数
        System.out.println(between.getSeconds());
//        间隔的毫秒数
        System.out.println(between.toMillis());
//        间隔的纳秒数
        System.out.println(between.toNanos());
```



### 异常

##### 自定义异常：(两个构造)

- 定义异常类
- 写继承关系

```java
public class AgeOutOfBoundsException extends RuntimeException{
    public AgeOutOfBoundsException(){
        
    }
    public AgeOutOfBoundsException(String message){
        super(message);
    }
}
```





## 集合

##### Collection集合常用方法

| 方法名                                                       | 说明                               |
| ------------------------------------------------------------ | ---------------------------------- |
| boolean add(E e)                                             | 添加元素                           |
| boolean remove(Object o)                                     | 从集合中删除指定元素               |
| boolean removeif(Object o)                                   | 根据条件删除元素                   |
| void clear()                                                 | 清空集合                           |
| boolean contains(Object o)【参数可以书写lambda表达式，lambda表达式的参数就是每次遍历的值，返回值为true表示删除。会将所有符合要求的元素都删除】 | 判断是否包含指定的元素             |
| boolean isEmpty()                                            | 集合是否为空                       |
| int size()                                                   | 集合的长度，也就是集合中元素的个数 |

##### Collection 集合的遍历

**Iterator：迭代器，集合特有的遍历方式**







## 线程



## IO流

### 序列化

```java
package com.mao.file.io;

import java.io.Serializable;

/**
 * @ClassName: User
 * @Description: TODO
 * @Author 毛毛
 * @CreateDate 2021/07/12/周一 13:27
 * @Version: v1.0
 */
public class User implements Serializable {
    //Serializable 实现了Serializable接口，表示这个类可以被序列化，用作网络传输等
    /**
     * 实现了这个序列化接口，如果我们没有定义序列号，那么虚拟机会根据类中的信息，会自动计算出一个序列号
     * 如果类被修改了，那么虚拟机会再次计算出一个序列号
     * 如果用对象流写入对象到文件中，然后将类给修改了，文件中的对象无法被反序列化成功
     * 因为在序列化对象的时候，会将序列号也写入文件，当反序列化的时候发现序列号不一致，则报错
     * 解决方式就是手动定义一个序列号：不让虚拟机计算提供
     */
    // 定义序列号 serialVersionUID
    private static final long serialVersionUID = 10000L;

    private String name;
    private Integer age;
    // transient 表示这个属性 不序列化到本地
    private transient String gender;

    public User(String name, Integer age, String gender) {
        this.name = name;
        this.age = age;
        this.gender = gender;
    }

    public User() {
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", gender='" + gender + '\'' +
                '}';
    }
}

```

### 对象操作流

```java
public class ObjectStreamTest {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        User user = new User("毛",22,"男");
        // 对象流,构造函数的参数是字节输出流
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("study-01-java-basics/src/com/mao/file/io/user.txt"));
        // 写对象到文件中,对象需要实现系列化接口，Serializable （序列化）
        oos.writeObject(user);
        oos.close();

        // 修改user类，然后在读取

        // 读取文件中的对象（反序列化）
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("study-01-java-basics/src/com/mao/file/io/user.txt"));
        System.out.println(ois.readObject());
        ois.close();
    }
}
```

#### 读取多个序列化的对象

```java
private static void method() throws IOException, ClassNotFoundException {
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("study-01-java-basics/src/com/mao/file/io/user.txt"));
        // 读取多个序列化的对象
        // 这个流的读取到的文件的结束，会报错 而不是 -1 或者 null
        while (true){
            try {
                System.out.println(ois.readObject());
            } catch (EOFException e) {
                // 捕获到这个异常，表示读取到了文件末尾
                e.printStackTrace();
                break;
            }finally {
                ois.close();
            }
        }
    }
```







## 网络编程



## 反射

### 类加载器

#### 什么是类加载器

负责将字节码文件加载到内存中（jvm虚拟机中）

#### 类加载时机

1. 创建一个类的实例的时候（对象）
2. 调用类的类方法（也就是静态方法）
3. 访问类或接口的类变量，或者为该类变量赋值（静态变量）
4. 使用反射方式来强制创建某个类或接口对应的java.lang.Class 对象
5. 初始化某个类的子类
6. 直接使用 java.exe 命令运行某个主类

#### 类的加载过程

加载 --->  【 验证 ---> 准备 ---> 解析 】（中间三步也称为链接）---> 初始化

- 加载：

  -  通过一个类的全限定名 来获取定义此类的二进制字节流

    通过 包名 + 类名 来获取到这个类，准备用流进行传输

  - 将这个字节流所代表的静态存储结构转化为运行时数据结构

    将这个类加载到内存中

  - 在内存中生成代表这个类的java.lang.Class 对象，任何类在被使用的时候，系统都会建立一个对应的对象。

    加载完毕创建一个Class对象

- 链接

  - 验证

    确保Class文件字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身安全

    (文件中的信息是否符合虚拟机规范有没有安全隐患)

    ![03_类加载过程验证](D:/%E7%99%BE%E5%BA%A6%E7%BD%91%E7%9B%98%E4%B8%8B%E8%BD%BD/%E5%8D%9A%E5%AD%A6%E8%B0%B7%E8%B5%84%E6%BA%90/%E8%A7%86%E9%A2%91%E6%96%87%E6%A1%A3/12.%E5%9F%BA%E7%A1%80%E5%8A%A0%E5%BC%BA/day25-%E5%9F%BA%E7%A1%80%E5%8A%A0%E5%BC%BA01/%E7%AC%94%E8%AE%B0/img/03_%E7%B1%BB%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B%E9%AA%8C%E8%AF%81.png?lastModify=1620662850)

  - 准备

    负责为类的类变量（被static修饰的变量）分配内存，并设置默认初始化值(初始化静态变量)

  ![04_类加载过程准备](D:/%E7%99%BE%E5%BA%A6%E7%BD%91%E7%9B%98%E4%B8%8B%E8%BD%BD/%E5%8D%9A%E5%AD%A6%E8%B0%B7%E8%B5%84%E6%BA%90/%E8%A7%86%E9%A2%91%E6%96%87%E6%A1%A3/12.%E5%9F%BA%E7%A1%80%E5%8A%A0%E5%BC%BA/day25-%E5%9F%BA%E7%A1%80%E5%8A%A0%E5%BC%BA01/%E7%AC%94%E8%AE%B0/img/04_%E7%B1%BB%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B%E5%87%86%E5%A4%87.png?lastModify=1620662970)

  

  - 解析

    将类的二进制数据流中的符号引用替换为直接引用(本类中如果用到了其他类，此时就需要找到对应的类)

![05_类加载过程解析](D:/%E7%99%BE%E5%BA%A6%E7%BD%91%E7%9B%98%E4%B8%8B%E8%BD%BD/%E5%8D%9A%E5%AD%A6%E8%B0%B7%E8%B5%84%E6%BA%90/%E8%A7%86%E9%A2%91%E6%96%87%E6%A1%A3/12.%E5%9F%BA%E7%A1%80%E5%8A%A0%E5%BC%BA/day25-%E5%9F%BA%E7%A1%80%E5%8A%A0%E5%BC%BA01/%E7%AC%94%E8%AE%B0/img/05_%E7%B1%BB%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B%E8%A7%A3%E6%9E%90.png?lastModify=1620663074)





## 基础加强

### 反射

- 反射机制

  是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；
  对于任意一个对象，都能够调用它的任意属性和方法；
  这种动态获取信息以及动态调用对象方法的功能称为Java语言的反射机制。



