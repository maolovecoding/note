```python

print(r"hello\s\t\t\t\t\t\s");
print("r表示原样输出后面字符串"); #表示'\'不生效
```

 常量：命名的时候，名字大写。

#：单行注释，#后面的内容不会被解读，但只是一行

\r: 使光标回到本行开头

```python
NAME = "jack"
print(NAME)
```

##### '''   '''   三引号：

​			1保证内容按照输入的原格式输出,

​			2还可以用来当做注释使用（没有发生赋值的情况）

```Python
emal = '''      大胡汉三
安东脚手架
奥斯迪短视的
安东司机  ！！！'''
print(emal)
''' askdhadsjkasjdhc ajsdbkasjdAUKD'''
```

 +：可以用于字符串的拼接。(只能拼接字符串，其他类型需要强转)

```Python
print('毛毛'+'俊俊'+'毛毛')
name = '毛俊'
age = 18
studentNo = 18188
print('姓名:%s,年龄:%d,学号:%d' %(name,age,studengtNo))
```

字符串的格式化输出：

方式：1.使用占位符 	2. format() 使用format前面需要大括号，后面为.format(内容,内容)

format是一个字符串中的函数 .format() 	'.'调用 [] {} ()

```Python
age = 20
message = '毛毛说：我今年{}岁了。'.format(age)
print(message)
```

#输入：	input() 	input 标准输入流（键盘输入）

#阻塞  admin（回车）------》接收键盘（流）输入的信息

input()接收的流的输入，都默认为字符串类型，即使输入的是数字，也是带引号的‘数字’

```Python
name = input('请输入名字：')
print(name)
```

#### operator：运算符

赋值运算符： =，+=，-=，*=，%=，/=，

+=： 字符串也支持。可以完成字符串的拼接



```Python
#id:特点，身份，返回对象的内存地址
name = 'mmm'
print(id(name))
name1 = name
print(name == name1)#true 两个变量指向同一块地址，Python不同于Java
name1 = "asd"
print(name == name1)#false
```

算术运算符： +  -  *  /     拓展的算术运算符： **   //  %

​		** :   a ** b  表示a的b次方

​		// :  表示整除。 9 // 2 = 4

关系运算符：>=  <=  !=  ==  >  <  

​		input()接收的数字都是字符串，因此进行数字的比较时需要先转化为int类型

​		is		用户对象的比较 

```python
age  = 20
age1 = 20
print(id(age))
print(id(age1))
print(age is age1)#true
money = 20000000
salary = 20000000
print(id(money))
print(id(salary))
print(money is salary)#在pycharm里面地址一样，在交互式环境窗口中，doc窗口显示id不一样
''' 交互式环境中，所见即所得，执行一句，开辟一个空间，开辟的空间不同  '''
'''
1. 小整数对象池
整数在程序中的使用非常广泛，Python为了优化速度，使用了小整数对象池， 避免为整数频繁申请和销毁内存空间。
Python 对小整数的定义是 [-5, 256] 这些整数对象是提前建立好的，不会被垃圾回收。在一个 Python 的程序中，无论这个整数处于LEGB中的哪个位置，
所有位于这个范围内的整数使用的都是同一个对象。同理，单个字母也是这样的。
intern机制处理空格一个单词的复用机会大，所以创建一次，有空格创建多次，但是字符串长度大于20，就不是创建一次了。

2.大整数对象池。说明：终端（也就是交互式窗口）是每次执行一次，所以每次的大整数都重新创建，而在pycharm中，每次运行是所有代码都加载都内存中，属于一个整体，所以
 这个时候会有一个大整数对象池，即处于一个代码块的大整数是同一个对象。c1 和d1 处于一个代码块，而c1.b和c2.b分别有自己的代码块，所以不相等。
 '''
```

逻辑运算符：

​				and  	or		not 

​		and:逻辑与

​		or：逻辑或

​		not：逻辑非

| and  | x  and  y |   如果x为false，返回false，否则返回y的值   |
| :--: | :-------: | :----------------------------------------: |
|  or  |  x or y   |    如果x为true，返回true，否则返回y的值    |
| not  |   not x   | 如果x为true返回false，如果x为false返回true |

位运算符：

​		二进制： 0 1

​		八进制：0o开头，里面不能出现超过（包含等于8 ）的数字0o1234567

​		十六进制：0x开头，0-9，a-f  

​		bin():	将数字转化为二进制 

​		int(): 	将数字转化为十进制

​		负数如何转化?    原反补码

​		位运算符： 	&	|	~(取反)	^（相同0，不同1）	<<	>>

​			

```python
a = 3
print(bin(a))#0b 11 0b开头代表二进制
a = 13
print(bin(a))#0b1101
b = 0b1111
print(int(b))#15
c = -3
print(bin(c))#-0b11
```

#### 三目运算符

​		格式： 表达式 ？ 真 ：假

##### 		运算符的优先级：

​			排序：  （从左到右,一行属于一个级别）

​												**	

​												~

​												+，-(符号运算符)

​												*	/	//	%

​												+	-

```python
#python 格式： 结果 if 表达式  else 结果
a = 3 
b = 4
print( ( a + b ) if a < b else ( b - a ) )
#判断表达式是true还是false
#如果是true，将执行if前面的内容进行运算，并将结果直接输出（也可以赋值）
#如果是false，则将else后面的内容进行运算，并......
```

#### if语句

​			1	if 条件	： 

​						(必须缩进一下，Tab)条件成立执行的语句

​			2	if	条件 ：

​						(条件成立语句)

​				else	：

​						(条件不成立语句)

```python
name = ',ap';
if name != '' :
    print('我的名字真确')
    #python:判断的变量是'' 	0	None 默认是false
```

#### 		随机数：

​			import 	random

| **函数**           | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| random()           | 生成一个[0.0,1.0)之间的随机小数>>>random.random()0.5714025946899135 |
| randint(a,b)       | 生成一个[a,b]之间的整数>>>random.randint(10,100)64           |
| randrange(m,n[,k]) | 生成一个[m,n)之间以k为步长的随机整数>>>random.randrange(10,100,10)80 |
| getrandbits(k)     | 生成一个k比特长的随机整数>>>random.getrandbits(16)37885      |
| uniform(a,b)       | 生成一个[a,b]之间的随机小数>>>random.uniform(10,100)13.096321648808136 |
| choice(seq)        | 从序列seq中随机选择一个元素>>>random.choice([1,2,3,4,5,6,7,8,9])8 |
| shuffle(seq)       | 将序列seq中元素随机排列，返回打乱后的序列>>>s=[1,2,3,4,5,6,7,8,9];random.shuffle(s);print(s)[3,5,8,9,6,1,2,7,4] |
| seed(a=None)       | 初始化给定的随机种子，默认为当前系统时间>>>random.seed(10) #产生种子10对应的序列 |



```python
import	random
print(random.randint(1,10))#系统随机产生一个随机数，大小在1-10之间
num	= int(input('请输出您的数：'))
ran = random.randint(1,10)
print(num == ran)
```

#### for 循环

for 变量名	in	集合：

​		语句

```python
#range()可以传1个参数或者两个参数
#range(6)包含0，不包含6
#range(1,8)产生1到7的数
for	i in range(3):
    print('oooooo',i)
```

#### Python中range()函数的用法

​		1、函数原型：range（start， end， scan)：

##### 					参数含义：

start:计数从start开始。默认是从0开始。例如range（5）等价于range（0， 5）;

end:技术到end结束，但不包括end.例如：range（0， 5） 是[0, 1, 2, 3, 4]没有5

scan：每次跳跃的间距，默认为1。例如：range（0， 5） 等价于 range(0, 5, 1)

#### for	else

​	语法;	for	i	 in	集合：

​				else：

注意：for和else是同一级别的

```python
for i in range(10):
    print('sssss')
else:
    print('for循环执行完，或者没有任何循环数据（也就是说循环0次，如range(0)）才会进入else')
```

#### pass

​	空语句：

```python
for i in range(10):
    print('sssss')
else:
    pass#只要有缩进，而缩进的内容还不确定的时候，此时为了保证语法的正确性，就可以使用pass占位
    #print('for循环执行完，或者没有任何循环数据（也就是说循环0次）才会进入else')
print('结束')
for i in range(3):
    pass#打算循环三次，但还没有确定循环语句，可以先用pass占位，确保语法的正确性
```

#### while语句

​	语法：	while：关键字	完成循环

​			完整结构：	while	条件：

​										语句块

​								else	：

​										语句块

```python
i= 0
while i < 10:
    print(i)
    i++;#条件的修正，避免死循环
print('结束')    
```

#### 循环就是让一件事重复做多次

循环分为 while 和for

1. while循环
2. for循环

