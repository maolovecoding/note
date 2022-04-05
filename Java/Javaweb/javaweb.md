# Javaweb





## Filter过滤器

```java
public class fliter implements Filter{
    doFilter(ServletRequest req,ServletResponse resp, FilterChain fa){
    
}
}

```

什么是 FilterChain?

**FilterChain 代表过滤器链对象。**

- FilterChain 是一个接口，其实现由我们的Servlet容器提供，我们可以直接使用。

- 过滤器可以定义多个，可以组成过滤器链

- 核心方法

  ```java
  doFilter(req,resp)  // 放行方法
  ```

  **<span style='color:red'>如果有多个过滤器，在第一个过滤器中调用下一个过滤器，依此类推。直到到达最终访问资源。如果只有一个过滤器，放行的话就直接到达最终访问资源。</span>**

  

过滤器的基本使用

```java
// /* 表示拦截所有请求
@WebFilter("/*")
public class MyFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws ServletException, IOException {
        System.out.println("filter过滤器执行了");
        // 解决中文乱码问题
        response.setContentType("text/html;charset=UTF-8");
        // 放行
        chain.doFilter(request, response);
    }
}

```

过滤器的使用细节：

配置方式：

配置文件方式：

```xml
<filter>
<filter-name>filter1</filter-name>
<filter-class>com.mao.filter.Filter1</filter-class>
</filter>
<filter-mapping>
<filter-name>filter1</filter-name>
<filter-pattern>/*</filter-pattern>
</filter-mapping>
```

注解方式：

**@WebFilter(“拦截路径”)**

```java
// 具体的说，urlPatterns表示拦截路径 filterName表示拦截器名称
@WebFilter(urlPatterns = "/*",filterName = "filter1")
```



多个过滤器的使用顺序：

如果有多个过滤器，取决于过滤器映射（也就是filter-mapping）的顺序



### 过滤器的生命周期

- 创建

  当应用加载实例化对象并执行 init 初始化方法

- 服务

  对象提供服务的过程，执行 doFilter 方法

- 销毁

  当应用卸载时或服务器停止时对象销毁，执行destroy方法

### 过滤器的五种拦截行为

Filter过滤器默认拦截的是请求，但是在实际开发中，我们还有请求转发和请求包含，以及由服务器触发调用的全局错误页面。默认情况下过滤器是不参与过滤的，想要使用，就需要我们手动配置。

```xml
<filter>
        <filter-name>filter2</filter-name>
        <filter-class>com.mao.web.controller.filter.Filter2</filter-class>
    <!--        开启异步-->
        <async-supported>true</async-supported>
    </filter>
    <filter-mapping>
        <filter-name>filter2</filter-name>
        <url-pattern>/*</url-pattern>
        <!--过滤请求，默认值-->
        <dispatcher>REQUEST</dispatcher>
        <!-- 过滤全局错误页面，当由服务器调用全局错误页面时，过滤器工作-->
        <dispatcher>ERROR</dispatcher>
        <!-- 过滤请求转发，请求转发时 过滤器工作-->
        <dispatcher>FORWARD</dispatcher>
        <!-- 过滤请求包含：只能过滤动态包含，jsp的include指令是静态包含-->
        <dispatcher>INCLUDE</dispatcher>
        <!-- 过滤异步类型，要求我们在filter标签中配置开启异步支持-->
        <dispatcher>ASYNC</dispatcher>
    </filter-mapping>
    <!--    配置全局错误页面-->
    <error-page>
        <!-- 发生什么异常类型时跳转到错误页面-->
        <exception-type>java.lang.Exception</exception-type>
        <location>/error.jsp</location>
    </error-page>
    <error-page>
        <!--出现错误状态码时，也进行跳转-->
        <error-code>404</error-code>
        <location>/error.jsp</location>
    </error-page>
```



## Listener 监听器

**观察者设计模式：**所有的监听器都是基于观察者设计模式的

**三个组成部分：**

- 事件源：触发事件的对象
- 事件： 触发的动作，封装了事件源
- 监听器： 当事件源触发事件以后，可以完成功能

在程序中，我们可以对： 对象的创建和销毁，域对象中属性的变化，会话相关内容进行监听。

Servlet规范提供了8个监听器对象，监听器都是接口的形式提供，具体功能需要我们自己来完成。

**监听对象的监听器**:

- ServletContextListener: 用于监听ServletContext 对象的创建和销毁

核心方法：

| 返回值 | 方法名                                     | 作用                 |
| ------ | ------------------------------------------ | -------------------- |
| void   | contextInitialized(ServletContentEvent sc) | 对象创建时执行该方法 |
| void   | contextDestroyed(ServletContentEvent sc)   | 对象销毁时执行该方法 |

**参数：ServletContentEvent **

​	代表事件对象

事件洗中封装了事件源，也就是ServletContext

真正的事件指的是创建或销毁ServletContext对象的操作



- HTTPSessionListener：用于监听 HttpSession对象的创建和销毁





## 笔记

### 属性描述器ProPertyDescriptor

属性描述器的作用就是获取该字节码文件的某个属性的get和set方法。

```java
PropertyDescriptor pd = new PropertyDescriptor("属性名称",字节码文件);
// 获取该属性的set方法
Method method = pd.getWriteMethod();
method.invoke(对象，方法的参数)；
// 获取该属性的get方法
pd.getReaderMethod();
```

### 请求包含

在实际开发中，我们可能需要把两个Servlet的内容合并到一起来响应浏览器，而同学们都知道HTTP协议的特点是一请求，一响应的方式。所以绝对不可能出现有两个Servlet同时响应方式。那么我们就需要用到请求包含，把两个Servlet的响应内容合并输出。

| 返回值            | 方法名                            | 说明             |
| ----------------- | --------------------------------- | ---------------- |
| RequestDispatcher | getRequestDispatcher(String name) | 获取请求调度对象 |
| void              | include(req,resp)                 | 实现包含         |

```java
@WebServlet("/include1")
public class RequestInclude1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("11111111111111111111");
    //    请求包含
        RequestDispatcher rd = req.getRequestDispatcher("/include2");
        req.setAttribute("name","mao");
        rd.include(req,resp);
        resp.getWriter().println("1111");
    }
}

@WebServlet("/include2")
public class RequestInclude2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("2222222222222222222222");
        String name = (String) req.getAttribute("name");
        System.out.println(name);
        resp.getWriter().println("222");
    }
}
```

**响应的结果是响应到页面 222 然后 111.。响应结果只有一个，会将请求包含中被包含的部分响应在前面，然后是自己需要响应的部分。**



### Response

#### 设置缓存

| 返回值 | 方法名                                         | 说明                 |
| ------ | ---------------------------------------------- | -------------------- |
| void   | setDateHeader(String name,long time)  毫秒单位 | 设置消息头，添加缓存 |

```java
@WebServlet("/respDateHeader")
public class RespDateHeader extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html; charset=UTF-8");
        String news = "啊哈哈哈，";
        System.out.println("我被请求了。。。。");
        // 设置缓存  只有数据发生改变才会发生新的请求
        // 一小时以后失效
        resp.setDateHeader("Expires",System.currentTimeMillis()+60*60*1000);
        resp.getWriter().write(news);
    }
}
```

**注意：这种setDateHeader()方法的设置缓存的技术，只有在老版本的IE中，才生效。高级的缓存技术以后学习的时候在做记录。**

#### 定时刷新

| 返回值 | 方法名                              | 说明                                           |
| ------ | ----------------------------------- | ---------------------------------------------- |
| void   | setHeader(String name,String value) | 设置消息头，定时刷新页面（控制浏览器发起请求） |

```java
@WebServlet("/respTimeout")
public class RespTimeout extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 定时刷新技术
        String str = "哈哈哈，密码出错误，5秒后页面跳转到登录页面";
        resp.setContentType("text/html; charset=UTF-8");
        // 写数据
        resp.getWriter().write(str);
        // 定时刷新  Refresh 表示刷新 5 表示五秒后刷新 url 地址，需要加上虚拟地址（项目根路径）
        resp.setHeader("Refresh", "5;url=/study/login.html");
    }
}
```

#### 请求重定向

请求重定向：客户端的一次请求到达后，发现需要借助其他Servlet来实现功能。

特点： 浏览器地址栏发生改变，两次请求，请求域对象中不能共享数据，可以重定向到其他服务器。

重定向实现原理：

设置响应状态码为 302

`resp.setStatus(302)`

设置响应的资源路径（响应到哪里去，通过响应消息头 location 来指定）

`resp.setHeader("lcoation", "/study/redirect")`

**请求重定向方法：**

`resp.sendRedirect("地址")`

#### 文件下载

核心：

1. 采用字节输入流的方式读取需要关联的文件

2. 设置消息头（响应头Content-Type）的值为 `application/octet-stream`。以字节流的方式响应给客户端。告知客户端响应正文类型

3. 设置响应头以下载的方式打开附件`resp.setHeader("Content-Disposition", "attachment;filename=1.jpg");` 通知客户端已下载的方式接受数据

   ```java
    // 字节输入流，读取关联的文件
           String filePath = getServletContext().getRealPath("/images/1.jpg");
           BufferedInputStream bis = new BufferedInputStream(new FileInputStream(filePath));
           // 设置响应头支持的类型
           // application/octet-stream 代表应用字节流响应头
           resp.setContentType("application/octet-stream");
           // 设置响应头以下载的方式打开附件
           // Content-Disposition 消息头的名称  处理的形式
           // attachment;filename=1.jpg 消息头参数，附件的形式进行处理，并指定的下载文件的名称
           resp.setHeader("Content-Disposition", "attachment;filename=1.jpg");
           // 获取字节输出流对象
           ServletOutputStream os = resp.getOutputStream();
           byte[] bytes = new byte[1024];
           int len;
           // 循环读写
           while((len = bis.read(bytes))!=-1){
               os.write(bytes,0,len);
           }
           // 释放资源
           bis.close();
   ```

   

### 文件上传

使用Apache提供的commons-fileupload 组件完成文件上传操作

页面部分：

页面提供文件上传的表单:修改form表单的enctype属性

```html
<form id="editForm" action="" method="post" enctype="multipart/form-data">
    <input type="file" class="form-control" placeholder="题目图片" name="picture"/>
</form>

```

**后台**：可以使用的技术有很多，在此处我们使用apache提供的commons-fileupload组件完成文件上次操作，后台的操作步骤如下

- 确认请求操作是否支持文件上传

- 创建磁盘工厂对象，用于将页面上传的文件保存到磁盘中

- 获取servet文件上传核心对象

- 读取数据

- 对读取到数据中的文件表单进行操作，并将内容写到指定位置

  ```JAVA
   //1.确认该操作是否支持文件上传操作，enctype="multipart/form-data"
      if(ServletFileUpload.isMultipartContent(request)){
          //2.创建磁盘工厂对象
          DiskFileItemFactory factory = new DiskFileItemFactory();
          //3.Servlet文件上传核心对象
          ServletFileUpload fileUpload = new ServletFileUpload(factory);
          //4.从request中读取数据
          List<FileItem> fileItems = fileUpload.parseRequest(request);
  
          for(FileItem item : fileItems){
              //5.当前表单是否是文件表单
              if(!item.isFormField()){
                  //6.从临时存储文件的地方将内容写入到指定位置
                  item.write(new File(this.getServletContext().getRealPath("upload"),item.getName()));
              }
          }
      }
  ```

  
  
  
  
  
  
  ## 数据报表
  
  #### Excel报表技术：
  
  1. JXL：支持xls文件操作
  2. POI：支持xls与xlsx文件操作
  
  使用POI，制作Excel报表
  
  #### 导入POI依赖坐标
  
  ```xml
  <!--POI-->
  <dependency>
      <groupId>org.apache.poi</groupId>
      <artifactId>poi</artifactId>
      <version>4.0.1</version>
  </dependency>
  <dependency>
      <groupId>org.apache.poi</groupId>
      <artifactId>poi-ooxml</artifactId>
      <version>4.0.1</version>
  </dependency>
  <dependency>
      <groupId>org.apache.poi</groupId>
      <artifactId>poi-ooxml-schemas</artifactId>
      <version>4.0.1</version>
  </dependency>
  ```
  
  #### POI写Excel
  
  ```java
  @Test
      public void testWriteByPoi() throws IOException {
          // 获取到对应的Excel文件，工作薄文件
          XSSFWorkbook wb = new XSSFWorkbook();
          // 创建工作表（Sheet），使用默认名称（一般是sheet0,sheet1.。。）
          XSSFSheet sheet = wb.createSheet();
  
          // 创建工作表中的行对象(第几行)（行数从0开始）
          XSSFRow row = sheet.createRow(0);
          // 创建工作表行对象中的列对象（列属于行）（第几列）（列也是从0开始）
          XSSFCell cell = row.createCell(1);
          // 在列中（实际上是单元格中）写上数据（写数据使用set方法）
          cell.setCellValue("毛毛");
          // 创建工作表并指定名称
          wb.createSheet("工作表1");
          // 上面创建的Excel都是在内存中创建的，需要将其写入到具体的文件中
          // File文件对象创建对象的路径是项目根目录下开始
          // 要操作的文件不能被打开，必须在关闭情况下才能完成POI操作
          // 该文件对象是作为excel文件内容的输出文件
          File file = new File("test.xlsx");
          // 创建文件输出流对象，包装对应的目标文件
          OutputStream os = new FileOutputStream(file);
          // 将工作蒲写入指定的文件中，内存中的workbook写入到流中
          wb.write(os);
          // 关闭输出流
          os.close();
          // 关闭工作薄
          wb.close();
      }
  ```
  
  #### POI读Excel
  
  ```java
  @Test
      public void readExcel() throws IOException {
          // 获取要读取的工作蒲对象
          Workbook wb = new XSSFWorkbook("test.xlsx");
          // 获取工作表 (按照索引获取工作表)，0表示第一张工作表
          Sheet sheetAt = wb.getSheetAt(0);
          // 获取工作表中的行
          Row row = sheetAt.getRow(0);
          // 获取行中的列（单元格）
          Cell cell = row.getCell(1);
          // 获取单元格中的值
          // 根据数据类型获取数据
          String stringCellValue = cell.getStringCellValue();
          System.out.println(stringCellValue);
          // 关闭工作蒲
          wb.close();
      }
  ```
  
  #### 使用POi简单制作的模板
  
  ```java
   @Test
      public void testProjectPoi() throws IOException {
          // 做出一个模板文件（导入题库）
          // 创建工作薄
          Workbook wb = new XSSFWorkbook();
          // 创建工作表
          Sheet sheet = wb.createSheet("题目数据文件");
  
          // 设置通用配置
          // 设置列的宽度(设置所有列的宽度，属于工作表的范围)
          // sheet.setColumnWidth(4,10000);
  
          // 水平方向对齐
          CellStyle cs_field = wb.createCellStyle();
          cs_field.setAlignment(HorizontalAlignment.CENTER);
          // 设置表格线
          // BorderStyle.THIN 是最细的线
          cs_field.setBorderTop(BorderStyle.THIN);
          cs_field.setBorderLeft(BorderStyle.THIN);
          cs_field.setBorderRight(BorderStyle.THIN);
          cs_field.setBorderBottom(BorderStyle.THIN);
  
          // 制作标题
  
          // 使用addMergedRegion方法来合并行和列
          // 需要传入一个 CellCellRangeAddress 对象
          // 这个对象有四个参数（开始行，结束行，开始列，结束列）
          // 合并单元格（合并列），是表对象操作单元格的合并
          sheet.addMergedRegion(new CellRangeAddress(1, 1, 1, 12));
          Row row_1 = sheet.createRow(1);
          Cell cell_1_1 = row_1.createCell(1);
          cell_1_1.setCellValue("在线试题导出信息");
          // 使用工作簿对象创建一个excel的单元格样式对象
          CellStyle cs_title = wb.createCellStyle();
          // 设置具体的样式
          // 水平对齐
          cs_title.setAlignment(HorizontalAlignment.CENTER);
          // 垂直居中
          cs_title.setVerticalAlignment(VerticalAlignment.CENTER);
  
          cs_title.setBorderTop(BorderStyle.THIN);
          cs_title.setBorderLeft(BorderStyle.THIN);
          cs_title.setBorderRight(BorderStyle.THIN);
          cs_title.setBorderBottom(BorderStyle.THIN);
  
          // 设置单元格的样式
          cell_1_1.setCellStyle(cs_title);
  
          // 制作表头
          String[] fields = {
                  "题目ID",
                  "所属公司ID",
                  "所属目录ID",
                  "题目简介",
                  "题干描述",
                  "题干配图",
                  "题目分析",
                  "题目类型",
                  "题目难度",
                  "是否经典题",
                  "题目状态",
                  "审核状态"
          };
          Row row_2 = sheet.createRow(2);
          // 循环给单元格赋值字段
          for (int i = 0; i < fields.length; i++) {
              Cell cell_2_temp = row_2.createCell(1 + i);
              cell_2_temp.setCellValue(fields[i]);
              // 水平方向对齐
              cell_2_temp.setCellStyle(cs_field);
          }
  
          // 制作数据区
          List<Question> questionList = new ArrayList<>();
          // 索引，单元格所在列
          int row_index = 0;
          // 循环给单元格赋值字段
          for (Question question : questionList) {
              // 每次循环列自动到下一列
              Row row_temp = sheet.createRow(3 + row_index++);
              int cell_index = 0;
  
              // 第一个数据列
              Cell cell_data_1 = row_temp.createCell(1 + cell_index++);
              cell_data_1.setCellValue(question.getId());
              cell_data_1.setCellStyle(cs_field);
  
              // 第二个数据列
              Cell cell_data_2 = row_temp.createCell(1 + cell_index++);
              cell_data_2.setCellValue(question.getCompanyId());
              cell_data_2.setCellStyle(cs_field);
  
              // 第三个数据列
              Cell cell_data_3 = row_temp.createCell(1 + cell_index++);
              cell_data_3.setCellValue(question.getCatalogId());
              cell_data_3.setCellStyle(cs_field);
  
              // 第四个数据列
              Cell cell_data_4 = row_temp.createCell(1 + cell_index++);
              cell_data_4.setCellValue(question.getRemark());
              cell_data_4.setCellStyle(cs_field);
  
              // 第五个数据列
              Cell cell_data_5 = row_temp.createCell(1 + cell_index++);
              cell_data_5.setCellValue(question.getSubject());
              cell_data_5.setCellStyle(cs_field);
  
              // 第六个数据列
              Cell cell_data_6 = row_temp.createCell(1 + cell_index++);
              cell_data_6.setCellValue(question.getPicture());
              cell_data_6.setCellStyle(cs_field);
  
              // 第七个数据列
              Cell cell_data_7 = row_temp.createCell(1 + cell_index++);
              cell_data_7.setCellValue(question.getAnalysis());
              cell_data_7.setCellStyle(cs_field);
  
              // 第八个数据列
              Cell cell_data_8 = row_temp.createCell(1 + cell_index++);
              cell_data_8.setCellValue(question.getType());
              cell_data_8.setCellStyle(cs_field);
  
              // 第九个数据列
              Cell cell_data_9 = row_temp.createCell(1 + cell_index++);
              cell_data_9.setCellValue(question.getDifficulty());
              cell_data_9.setCellStyle(cs_field);
  
              // 第十个数据列
              Cell cell_data_10 = row_temp.createCell(1 + cell_index++);
              cell_data_10.setCellValue(question.getIsClassic());
              cell_data_10.setCellStyle(cs_field);
  
              // 第十一个数据列
              Cell cell_data_11 = row_temp.createCell(1 + cell_index++);
              cell_data_11.setCellValue(question.getState());
              cell_data_11.setCellStyle(cs_field);
  
              // 第十二个数据列
              Cell cell_data_12 = row_temp.createCell(1 + cell_index++);
              cell_data_12.setCellValue(question.getReviewStatus());
              cell_data_12.setCellStyle(cs_field);
          }
  
          // 创建工作表中的行（对象）
          // 创建工作表中的列（单元格）
  
          // 将内存中的数据写入到文件中
          // 该文件对象是作为excel文件内容的输出文件
          File file = new File("test.xlsx");
          // 创建文件输出流对象，包装对应的目标文件
          OutputStream os = new FileOutputStream(file);
          // 将工作蒲写入指定的文件中，内存中的workbook写入到流中
          wb.write(os);
          // 关闭流对象
          os.close();
          wb.close();
      }
  
  ```
  



## 树形控件

### 树形控件结构分析

实现技术：

比较常见的：

	1. dTree
 	2. tdtree
 	3. zTree：功能最强的

#### 对于网站上的功能，如何搬到自己程序中

1. 观察整体的页面结构
2. 去除无效的基础信息
3. 去除页面无效的基础信息
4. 分析页面js内容
5. 分析结构所使用的数据
6. 简化页面的内容书写



