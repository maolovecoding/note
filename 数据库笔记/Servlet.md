# Servlet

## 1.1 Servlet简介

- Servlet就是sun公司开发动态web的一门技术
- SUn在这这些API中提供了一个接口叫做：Servlet。如果你想开发一个Servlet程序，只需要完成两个小步骤：
  - 编写一个类，实现Servlet接口
  - 把开发好的java类部署到web服务器中

**把实现了Servlet接口的java程序叫做，Servlet**

## 1.2 HelloServlet

Servlet接口在sun公司有两个默认的实现类：HTTPServlet，

 1. 构建一个普通的Maven项目，删除里面的src目录，以后我们学习就在这个项目里面建立Moudel。这个空的工程就是Maven的主工程。

 2. 关于Maven父子工程的理解：

    父项目中会有

    ```xml
    <modules>
            <module>Servlet-01</module>
    </modules>
    ```

    子项目会有

    ```xml
    <parent>
            <artifactId>My-Maven</artifactId>
            <groupId>org.example</groupId>
            <version>1.0-SNAPSHOT</version>
    </parent>
    ```

    父项目中的java子项目可以直接使用

    ```java
    son extends father
    ```

3. Maven环境优化
   	1. 修改web.xml为最新的
    	2. 将maven的结构搭建完整

4. 编写一个Servlet程序

   ​	1. 编写一个普通类

    2. 实现Servlet接口，这里我们直接继承HttpServlet

       ```java
       /**
        * @Author 毛毛
        * @Date 2020/11/29/周日 16:24
        */
       public class HelloServlet extends HttpServlet {
           //由于get和post只是请求实现的不同的方式，可以相互调用，业务逻辑都一样
           @Override
           protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
               //响应流
               PrintWriter writer = resp.getWriter();
               writer.print("Hello Servlet!!!!!!");
           }
       
           @Override
           protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       
           }
       }
       ```

5. 编写Servlet的映射

   为什么需要映射：我们写的是java程序，但是要通过浏览器去访问。而浏览器去访问需要；连接web服务器，所以我们需要再web服务中注册我们写的Servlet，还需要给他一个浏览器能够访问的路径；

   ```xml
   <!--    注册Servlet-->
   <servlet>
       <servlet-name>hello</servlet-name>
       <servlet-class>com.mao.servlet.HelloServlet</servlet-class>
   </servlet>
   <!--    Servlet的请求路径-->
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello</url-pattern>
   </servlet-mapping>
   ```

   

6. 配置Tomcat

   配置项目发布的路径

7. 启动测试。。。okk

## 1.3 Servlet原理

## 1.4 Mapping问题

 1. 一个Servlet可以指定一个映射路径

    ```xml
    <servlet-mapping>
            <servlet-name>hello</servlet-name>
            <url-pattern>/hello</url-pattern>
    </servlet-mapping>
    ```

    

 2. 一个Servlet可以指定多个映射路径

    ```xml
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello2</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello3</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello4</url-pattern>
    </servlet-mapping>
    ```

    

 3. 一个Servlet可以指定通用映射路径

    ```xml
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello/*</url-pattern>
    </servlet-mapping>
    ```

	4. 默认请求路径

    ```xml
    <!--    默认请求路径-->
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/*</url-pattern>
    </servlet-mapping>
    ```

    

 5. 指定一些后缀或者前缀等等......

    ```xml
    <!--    可以自定义后缀实现请求映射-->
    <!-- 注意*前面不能加映射的路径-->
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>*.mao</url-pattern>
    </servlet-mapping>
    ```

    

 6. 优先级问题

    指定了固有的映射路径优先级最高，如果找不到就会走默认的处理请求。

