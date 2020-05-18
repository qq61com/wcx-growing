是什么？

用在web层框架，是spring的一部分

### Spring MVC执行流程

1. 发送请求，首先到DispatcherServlet，使用处理器映射器找到Controller
2. 返回找到controller，使用处理器适配器让controller里面的方法执行
3. controller方法执行返回**ModelAndView**，ModelAndView中有设置的数据和跳转路径
4. 使用视图解析器把ModelAndView里面的数据传递到设置的页面中进行显示

### 组件说明

#### DispatcherServlet：前端控制器

用户请求到达前端控制器，它相当于mvc模式中的c，DispatcherServlet是整个流程控制的中心，由它调用其他组件，处理用户请求，DispatcherServlet的存在降低了组件之间的耦合性

#### HandlerMapping：处理器映射器

HandlerMapping负责根据用户请求到的Handler（处理器），springmvc提供了不同的映射器实现不同的映射方式，例如：配置文件、实现接口、注解

#### Handler：处理器

后端控制器

#### HandlAdapter：处理器适配器

对处理器进行执行，是适配器模式的应用，通过扩展适配器可以堆更多的处理器进行执行

#### View Resolver：视图解析器

将结果生成View视图，

#### View：视图

常用jsp

三大组件：处理器映射器、处理器适配器、视图解析器

handler（action）、view（jsp）需要开发

### 简化配置

```xml
<!--spring-config-->
<!--开启组件注解扫描-->
    <context:component-scan base-package="com.wcx"/>
    <!--配置处理器映射器和处理器适配器-->
    <mvc:annotation-driven/>
    <!--配置视图解析器-->
    <bean id="jspViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
        <!--配置跳转页面的路径-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--配置页面的后缀名-->
        <property name="suffix" value=".jsp"/>
    </bean>
```

```java
/* @Controller
	类{
 		@RequestMapping("/list") //定义请求url到处理器功能方法的映射
			方法{}
	}
*/
@Controller
public class UserController {

    @RequestMapping("/list")
    public String list(Model model) {

        List<User> list = new ArrayList();
        User u1 = new User("1", "xx", "12345");
        User u2 = new User("2", "qq", "12345");
        list.add(u1);
        list.add(u2);
        /*
         * 方法一
         * 传入HttpRequest对象
         * request.setAttributu();
         * */
        model.addAttribute("list", list);

        return "list";
    }
}

```

```
<!--配置web.xml-->
<servlet>
        <servlet-name>spring</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:bean1.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>


    <servlet-mapping>
        <servlet-name>spring</servlet-name>
        <!--/-->
        <url-pattern>*.action</url-pattern>
    </servlet-mapping>
```

### 参数绑定

方式一

```java
@RequestMapping("/login")
public String form1(HttpServletRequest request){
    String name =request.getParameter("username");
    String password = request.getParameter("password");
    request.setAttribute("info",name);
    System.out.println(name+password);
    return null;
}
```

获取表单数据，用pojo接收

#### 解决post乱码问题

```xml
<!--web.xml配置过滤器-->
<filter>
    <filter-name>Character</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>Character</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

### 高级参数绑定

传输对象/集合类型

### springmvc上传文件

1. 表单必须使用post提交
2. 表单里由文件上传项，<input type= "file" name ="file">
3. form标签里设置属性值enctype 为mutlipart/form-data

需要jar包：commons-fileupload、commons-io

```xml
<!--配置上传解析器-->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!--设置上传大小无限制-->
        <property name="maxUploadSize" value="-1"/>
    </bean>
```

```java
@RequestMapping("/form")
public String form1(String [] love,MultipartFile file){

    System.out.println(Arrays.toString(love));
    System.out.println("名称："+file.getOriginalFilename());
    File serverfile = new File("e:\\a"+file.getOriginalFilename());
    if (serverfile.exists()){
        serverfile.mkdirs();
    }
    try {
        file.transferTo(serverfile);
    } catch (IOException e) {
        e.printStackTrace();
    }
    return "form";
}
```

上传同一个文件默认覆盖

- 底层对fileupload封装
- 需要配置上传解析器
- 在表单页面中让表单页面满足三个要求
- 提交表单到action，上传就是文件复制的过程
- 可以设置上传文件的大小，在上传解析器里面进行配置，-1无限制

### RESTful风格支持

#### 是什么？

试一个资源定位及资源操作的风格，是对http协议的诠释

资源定位：互联网所有的事物都是资援，要求url中没有动词，只有名词。没有参数

```
<!--web.xml-->
<servlet-mapping>
    <servlet-name>spring</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

### 拦截器

实现HandlerInterceptor

三个方法，请求之前，未返回视图前，返回视图后

配置方式/代码处理直接放行

### JSON数据交互

#### @ResponseBody

```java
@RequestMapping("/list")
public @ResponseBody
User list(HttpServletResponse response ){

    User user = new User();
    user.setId(2);
    User userById = userService.getUserById(user);

    return userById;
}
```

```java
@RequestMapping("/findAll")
public String findAll(HttpServletResponse response){
    List<User> all = userService.findAll();
    String s = JSON.toJSONString(all);
    try {
        response.setContentType("text/html;charset=utf-8");
        response.getWriter().write(s);
    } catch (IOException e) {
        e.printStackTrace();
    }
    return null;
}
```

#### @RequestBody

获取前端提交过来的json，封装到对象里

```java
@RequestMapping("/userRequest")
public String userRequest(@RequestBody User user){

    System.out.println(user);

    return null;
}
```

#### FastJSON

 String s = JSON.toJSONString(all);

让类中某些属性不参与转换：@JSONField(serialize=false)



```
//对象变json
String s = JSON.toJSONString(all);
//json变对象
JSONObject.parseObject(s,User.class);
```

```
//集合互转
List<User> list = new ArrayList<>();
String s1 = JSON.toJSONString(list);
List<User> list1 = JSONArray.parseArray(s1, User.class);
```