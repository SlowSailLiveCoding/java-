# java-
一个java的学习记录文档

#### 1.jsoup选择器

select方法本来是element的，但是document是继承element所以可以用document来选择。

select方法的几个速记点：

1. https://www.open-open.com/jsoup/selector-syntax.htm 官方的cookbook做自己的参考手册
2. Jsoup.parse 是html和xml的解析手段，可以直接传入字符串的html，也可以使用new file的方式指定文件路径传入。

```java
String path1 = JsoupDemo1.class.getClassLoader().getResource("FirstXML.xml").toURI().getPath();Document parse = Jsoup.*parse*(new File(path1),"utf-8");
```

3.解析文件之后得到的是Document对象,可以使用dom方法和选择器来进行数据抽取。

```java
Elements name = parse.getElementsByTag("name");
String text = name.text();

Elements head = document.select("head");
System.out.println(head);
```

4.对于选择器的具体语法可以在https://jsoup.org/apidocs/里面查询selector 类，然后可以看到Selector syntax。 https://jsoup.org/apidocs/org/jsoup/select/Selector.html。

#### 2.java的命名规范

1.类名称是单词首字母大写，比如PaymentOrder。

2.方法第一个单词首字母小写，后面单词的首字母大写。如sendMessage。

3.常量名：全大写，下划线分割

4.包名小写

#### 3.XPath

1.可以用来解析xml和html。

2.使用Jsoup的Xpath需要额外导入jar包。

3.参考文档：     https://www.w3cschool.cn/xpath/

#### 4.tomcat

##### 4.1 安装bug

1黑窗口一闪而过：

​	原因： 没有正确配置JAVA_HOME环境变量       

​	解决方案：正确配置JAVA_HOME环境变量

2.启动报错：  

   原因：端口占用    

   解决： 暴力：找到占用的端口号，并且找到对应的进程，杀死该进程         

​				温柔：修改自身的端口号 找到 conf/server.xml 将 <Connector port="8888" protocol="HTTP/1.1"            					   connectionTimeout="20000"            redirectPort="8445" />       

​                 一般会将tomcat的默认端口号修改为80。80端口号是http协议的默认端口号。            * 好处：在访问时，就不用输入端口号

##### 4.2 关闭

   1.正常关闭：      * bin/shutdown.bat      * ctrl+c   

   2.强制关闭：      * 点击启动窗口的×

##### 4.3项目部署，建议直接结合开发工具

1.手动：https://www.bilibili.com/video/BV11741127ic?p=114

2.结合idea：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629192617521.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NUTV8zMnN0YXJ0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629192625304.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629192633379.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NUTV8zMnN0YXJ0ZXI=,size_16,color_FFFFFF,t_70)
#### 5.Servlet

##### 1.简介

Servlet实质上就是接口，定义了java类被浏览器访问到tomcat的规则。
将来我们自定义一个类，实现servlet类接口，重写方法。

##### 2.在web.xml写注释配置servlet
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629192509654.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629192514460.png)

其中，pattern定义的是浏览器的输入模式，然后name定义的是servlet类的名字，利用这个名字在上面找到servlet的类。当浏览器输入地址的时候，通过主机端口找到对应的资源。在webxml中找demo的资源，利用资源名找到对应的类，然后通过反射机制，将全类名对应的字节码加载进内存，创建对象，调用service方法。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629192416630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NUTV8zMnN0YXJ0ZXI=,size_16,color_FFFFFF,t_70)

##### 3.在tomcat已经运行的情况下浏览器没能访问服务

自己tomcat一直没有找到路径的原因是因为采用了war_exploded方式，所以正确的路径是 http://localhost:8080/TomcatPractice_war_exploded/hello。

在tomcat的按钮可以按下去的时候，尤其是本地的控制台已经提示成功连接的时候，说明tomcat和程序已经跑起来了。这个时候如果浏览器无法访问服务就要考虑是否是输错了地址。

##### 4.sevlet的初始化方法

默认情况下只有在某个类被访问的时候才会调用，所以只是将服务器打开是不会使用初始化方法的。在xml中可以指定servlet的创建时机。

```java
<servlet>
<servlet-name>hello</servlet-name>
<servlet-class>slowsail.javaweb.servlet.HelloServlet</servlet-class>
<!-- 指定servlet的创建时机
1.第一次访问的时候创建配置值为负数。
2.在服务器启动的时候创建则配置为非负数。

-->
<load-on-startup> -1</load-on-startup>
</servlet>
```

servelet的init方法只执行一次，说明servelet是单例的。

service destroy只有在服务器正常关闭的时候才会执行。在servlet销毁之前执行。

##### 5.sevlet的线程安全问题

 多个用户同时访问时可能存在线程安全问题：解决方案：尽量不在servlet对象中定义成员变量，而是在方法中定义局部变量。即使定义了成员变量，也不要对其赋值。

##### 6.方便的注解配置

在类中进行注解。

```java
@WebServlet(urlPatterns = "/demo")
public class SevletDemo implements Servlet {
```

其中注解也可以简写

其中注解也可以简写

```java
@WebServlet("/demo")
public class SevletDemo implements Servlet {
```

##### 7.Servlet的体系结构 

Servlet -- 接口    |  GenericServlet -- 抽象类    |  HttpServlet -- 抽象类

 **GenericServlet**：将Servlet接口中其他的方法做了默认空实现，只将service()方法作为抽象将来定义Servlet类时，可以继承GenericServlet，实现service()方法即可
 **HttpServlet**：对http协议的一种封装，简化操作 

 **1. 定义类继承HttpServlet**   

 **2. 复写doGet/doPost方法**

区别于servlet接口，上述的两个类都是抽象类，所以继承用的是extends。

实际我们多用HttpServlet，因为实际上我们多用get和post方法。在service中我们常常需要判断到底是用何种方法，而HttpServlet只用我们实现在判断完是用post还是get之后的动作。





#### 1.一个httpServlet的多个地址定义

对于httpServlet可以定义一个数组，数组可以定义多个地址，即对于一个servlet可以通过多个地址访问。

如：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200630141700540.png)
路径定义规则：/xxx
						:多层路径：/xxx/xxxx
						:/xxx/*    其中 星符号表示统配符，即可以/xxx/可以任意字符。
			          如果说星的情况包含了其他路径，只有在其他路径访问不到的时候才会访问星。即通配的优先级是较低的。  			
                       ：*.something 注意不要加/。这是前面任意，后面加.something的意思。something是一个随便的单词。

#### 2.我们访问一个页面时http做的事情

http协议基于tcp协议。在http1.1中，http协议可以保持一段时间。注意，当我们访问一段简单的页面的时候，我们可能有多次request的过程。图片，html资源都是单独的请求。

下图是我们访问bing网站的时候，http协议传送的内容。可以利用火狐或者chrome的F12键查看。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200630142927842.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NUTV8zMnN0YXJ0ZXI=,size_16,color_FFFFFF,t_70)



#### 3.http：数据格式

**概念**：Hyper Text Transfer Protocol 超文本传输协议
**传输协议**：定义了，客户端和服务器端通信时，发送数据的格式
**特点**：

  		1. 基于TCP/IP的高级协议
        	2. 默认端口号:80
            3. 基于请求/响应模型的:一次请求对应一次响应
            4. 无状态的：每次请求之间相互独立，不能交互数据

	* 两版本的区别：
		* 1.0：每一次请求响应都会建立新的连接
		* 1.1：复用连接

* 请求消息数据格式
  **1. 请求行**
  	请求方式  请求url   请求协议/版本
  	GET       /login.html	    HTTP/1.1

  ```markdown
  请求方式：
  	HTTP协议有7种请求方式，常用的有2种
  		 GET：
  			1. 请求参数在请求行中，在url后。
  			2. 请求的url长度有限制的
  			3. 不太安全
  		POST：
  			1. 请求参数在请求体中
  			2. **请求的url长度没有限制的**
  			3. 相对安全
  ```

  **2. 请求头**：客户端浏览器告诉服务器一些信息
  	请求头名称: 请求头值

   * 常见的请求头：
     1. User-Agent：浏览器告诉服务器，我访问你使用的浏览器版本信息
        * 可以在服务器端获取该头的信息，解决浏览器的兼容性问题

   2. Referer：http://localhost/first.html

       * 指示请求来源

         * 作用：

              1 . 防盗链：判断流量来源

           2. 统计工作：比如广告，看广告的效果。

  **3. 请求空行**
  	空行，就是用于分割POST请求的请求头，和请求体的。
  **4. 请求体(正文)**：
  	* 封装POST请求消息的请求参数的

  * 字符串格式：
    POST /login.html	HTTP/1.1
    Host: localhost
    User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:60.0) Gecko/20100101 Firefox/60.0
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
    Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
    Accept-Encoding: gzip, deflate
    Referer: http://localhost/login.html
    Connection: keep-alive
    Upgrade-Insecure-Requests: 1

    username=zhangsan	

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200630152310216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NUTV8zMnN0YXJ0ZXI=,size_16,color_FFFFFF,t_70)

#### 4.html内容回顾：form表单是如何做的



```html
<form action="form_action.asp" method="get">
    First name: <input type="text" name="fname" />
    Last name: <input type="text" name="lname" />
    <input type="submit" value="Submit" />
</form>
```





#### 5.故障排除：部署tomcat后静态资源如html出现404的情况。

解决方法：html的路径配置错误。应当放在web文件夹中。



#### 6.Request对象：

1.原理：
1tomcat根据请求创建对应对象，tomcat会创建两个对象，request封装请求消息。
2.tomcat将request两个对象传递给service方法，并且调用service方法。
3.程序员可以操作两个对象，request是来接收消息，responce是做出响应的对象。

重点：**request和response都是服务器来创建的，我们就是在service中操作他们。**

- 获取请求方式 ：GET
     * String getMethod()  
- 获取虚拟目录
  - String getContextPath()
- 获取Servlet路径: 
  - String getServletPath()
- 获取get方式请求参数：name=zhangsan
  - String getQueryString()
- 获取请求URI：
  - String getRequestURI():	
  - StringBuffer getRequestURL()  :http://localhost/demo
- 获取协议及版本：HTTP/1.1
   * String getProtocol()
- 获取客户机的IP地址：
    * String getRemoteAddr()
- 获取请求头数据
  * 方法：
    * (*)String getHeader(String name):通过请求头的名称获取请求头的值
    * Enumeration<String> getHeaderNames():获取所有的请求头名称
- 获取请求体数据:
  * 请求体：只有POST请求方式，才有请求体，在请求体中封装了POST请求的请求参数
  * 步骤：
    1. 获取流对象
       *  BufferedReader getReader()：获取字符输入流，只能操作字符数据
       *  ServletInputStream getInputStream()：获取字节输入流，可以操作所有类型数据
          * 在文件上传知识点后讲解
    2. 再从流对象中拿数据
- 重点掌握：虚拟目录，URL.

#### 7.无法建立package的方法：右键–>new –> Mark Directory As –> Sources Root 就可以啦 ”

不过有一点，就是其实很多时候无法建立就是哪里之前不对或者确实不应该在这里建立啦（对于小白的我来说:)）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200630165047858.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NUTV8zMnN0YXJ0ZXI=,size_16,color_FFFFFF,t_70)
