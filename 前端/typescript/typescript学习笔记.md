# typescript学习笔记

## 一. 为什么要学习typescript

##  二. 如何安装typescript

## 三. typescript的类型

<span style="color:red;font-weight:600;">类型：</span>

|  类型   |       例子        |               描述               |
| :-----: | :---------------: | :------------------------------: |
| number  |     1,-33,2.7     |             热议数字             |
| string  |  ‘hi’,”hh”,`hq`   |            任意字符串            |
| boolean |    true,false     |       布尔值true或者false        |
| 字面量  |      其本身       |   限制变量的值就是该字面量的值   |
|   any   |         *         |             任意类型             |
| unknown |         *         |          类型安全的any           |
|  void   | 空值（undefined） |       没有值（undefined）        |
|  never  |      没有值       | 不能是任何值（里面常用来抛异常） |
| object  |  {name:’孙悟空’}  |           任意的JS镀锡           |
|  array  |     [1,2,3,4]     |           任意的JS数组           |
|  tuple  |       [1,4]       |  元组，TS新增类型，固定长度数组  |
|  enum   |     enum{A,B}     |        枚举，TS中新增类型        |

- number:

  

- string 

  

- boolean

  

- 字面量

  

- any

  

- unknown

  

- void

  

- never

  

- object

  

- array

  

- tuple

  

- enum



## 四. 类型断言

通过*类型断言*这种方式可以告诉编译器，“相信我，我知道自己在干什么”。类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。它没有运行时的影响，只是在编译阶段起作用。 TypeScript会假设你，程序员，已经进行了必须的检查。

可以用来告诉解析器变量的实际类型 编译器不知道什么类型，但是我们知道，可以手动让编译器知道。

<span style="color:red">类型断言有两种形式。 其一是“尖括号”语法：</span>

```typescript
/*
语法 ：
    1. 变量 as 类型;
    2. <类型> 变量;
 */
let a:any;
a = 'asss';
let b:string = <string> a;
```

另一个为<span style="color:red">`as`</span>语法：

```typescript
let a:any;
a = 'asss';
let b:string = a as string;
```



## 五. 编译选项

- 自动编译文件

  - 编译文件时，使用 -w 指令后，TS编译器会自动监视文件的变化，并在文件发生变化时对文件进行重新编译。

  - 示例：

    - ```shell
      tsc xxx.ts -w
      ```

    - 一旦开启这个命令行窗口就只能用来处理这个文件了。<span style="color:red">CTRL + C 可以终止</span>

- 自动编译整个项目

  - 如果直接使用tsc指令，则可以自动将当前项目下的所有ts文件编译为js文件。

  - 但是能直接使用tsc命令的前提时，要先在项目根目录下创建一个ts的配置文件tsconfig.json 

  - tsconfig.json是一个JSON文件，添加配置文件后，只需只需tsc命令即可完成对整个项目的编译

  - 配置选项：

    - include

      - 定义希望被编译文件所在的目录

      - 默认值：[“**/\*”]

      - 示例：

        - ```json
          "include":["src/**/*","tests/**/*"]
          ```

        - 上述示例中，所有src目录和tests目录下的文件都会被编译。

    - exclude

      - 定义需要排除在外的目录

      - 默认值 ： [“node_modules”,”bower_components”,”jspm_packages”]

      - 示例：

        - ```json
          "exclude":["./src/hello/**/*"]
          ```

        - 上述示例中，src目录下的hello目录下的文件都不会被编译。

    - extends

      - 定义被继承的配置文件

      - 示例：

        - ```json
          "extends":"./configs/base"
          ```

        - 上述示例中，当前配置文件中会自动包含config目录下的base.json中所有的配置信息。

    - files

      - 指定被编译文件的列表，只有需要编译的文件少时才会用到

      - 示例：

        - ```json
          "files":[
              "sys.ts",
              "aaa.ts",
              "hander.ts",
              "tsc.ts",
              "binder.ts"
          ]
          ```

        - 列表中的文件都会被ts 编译器编译

    - compilerOptions

      - 编译选项是配置文件中非常重要也比较复杂的配置选项

      - 在compilerOptions中包含了多个子选项，用力啊完成对编译的配置

        - 项目选项

          - target

            - 设置ts代码编译的目标版本

            - 可选值

              - ES3（默认），ES5，ES6/ES2015，ES7/ES2016，ES2017，ES2018，ES2019，ES2020，ESNext()

            - 示例：

              - ```json
                "compilerOptions":{
                    "target":"ES6"
                }
                ```

              - 如上设置，我们所编写的ts代码将会被编译为ES6版本的js代码

          - lib

            - 指定代码运行时所包含的库(宿主环境)

            - 可选值：

              - ES5,ES6/ES2015，ES7/ES2016，ES2017，ES2018，ES2019，ES2020，ESNext，DOM，WebWorker，ScriptHost ......

            - 默认值：一般情况下我们不需要改。

            - 示例：

              - ```json
                "compilerOptions":{
                    "target":"ES6",
                    "lib":["ES6","DOM"],
                    "outDir":"dict",
                    "outFile":"dict/aa.js"
                }
                ```

          - module

            - 设置编译后代码使用的模块化系统

            - 可选值：

              - 'none', 'commonjs', 'amd', 'system', 'umd', 'es6', 'es2015', 'es2020', 'esnext'.

              - 示例：

                - ```json
                  "compilerOptions":{
                      "target":"es6",
                      "module":"ES6"
                  }
                  ```

                - 







### 依赖声明

当前文件依赖某个其他的文件。

```ts
///<reference path="./依赖" />
```



