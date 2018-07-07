---
title: Struts2 Study
date: 2017-09-05 10:22:21
categories: 编程
tags: [Struts2框架]
---

# Hello World Using Struts 2

#### outline

Suppose you want to create a simple "Hello World" example that display a welcome message. After setting up an empty basic Struts2 web application, to create a "Hello World", to creat a "Hello World" example, you need to do four things:

1. Create a class to store the welcome message (the model)
2. Create a server page to present the message (the view)
3. Create an Action class to  control the interaction between the user , the model , and the view (the controller)
4. Creat a mapping (==struts.xml==) to couple the Action class and view 

#### The Code

Let's modify the basic-struts project to add the following:

- a model class to store our message
- a view that displays our message
- an Action class to act as the controller
- a configuration that ties everything together

### Step 1 - Create The Model Class MessageStore.java

Create the ==MessageStore== class in ==src/==

**MessageStore.java**

```Java
package com.isaac.message;

public class MessageStore {
    private String message;
    public MessageStore(){
        message = "Hello Struts User";
    }
    public String getMessage(){
        return message;
    }
}

```

Note the use of public getter method to allow access to the private message String attribute.



### Step 2 - Create The Action Class HelloWorldAction.java

We need an Action class to act as the Controller. The Action class responds to a user action (in this example that action will be clicking an HTML hyperlink and sending a specific URL to the Servlet container). One or more of the Action class's methods are excuted and a String result is returned. Based on the value of the result, a specific view page(in this example that view page is HelloWorld.jsp) is rendered.

Note the package and import statements below.

```Java
package com.isaac.action;

import com.isaac.message.MessageStore;
import com.opensymphony.xwork2.ActionSupport;

public class HelloWorldAction extends ActionSupport{
    private MessageStore messageStore;
    @Override
    public String execute() throws Exception{
        messageStore = new MessageStore();
        return SUCCESS;
    }
    public MessageStore getMessageStore(){
        return messageStore;
    }
}

```

The Struts 2 framework will create an obejct of the ==HelloWorldAction== class and call the execute method in response to a user's action (clicking on a hyperlink that sends a specific URL to the Servelet container).

In this example, the execute method creates an object of class ==MessageStore== and then returns the String constant ==SUCCESS==.

### Step 3 - Create The View HelloWorld.jsp

We need a server page to present the message that is stored in the model class ==MessgeStore==. Create the below JSP in ==/web==

**HelloWorld.jsp**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$HelloWorld$</title>
  </head>
  <body>
      This is result.jsp
  </body>
</html>
```

### Step 4 - Add The Struts Configuration In struts.xml

We need a mapping to tie the URL, the ==HelloWorldAction== class(controller), and the HelloWorld.jsp(the view) together. The mapping tells the Struts 2 framework whick class will respond to the user's action(the URL), which method of that class will be executed, and what view to render based on the String result that method returns.

Edit the ==struts.xml== file to add the action mapping.

**struts.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
        "http://struts.apache.org/dtds/struts-2.3.dtd">
<struts>
    <package name="default" namespace="/" extends="struts-default">
        <action name="helloworld" class="com.isaac.action.HelloWorldAction">
            <result>/index.jsp</result>
        </action>
    </package>
</struts>
```



## How the Code Works

Your browser sends to the web server a request for the URL [http://localhost:8080/helloworld.action](http://localhost:8080/helloworld.action).

1. The container receives from the web server a request for the resource ==hello.action==. According to the settings loaded from the web.xml, the container finds that all requests are being routed to ==org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter== , including the ==*.action== requets. The StrutsPrepareAndExecuteFilter is the entry point into the framework.
2. The framework looks for an action mapping named "helloworld", and it finds that this mapping corresponds to the class "HelloWorldAction". The framework instantiates the Action and calls the Action's ==execute== method.
3. The ==execute== method creates the MessageStore object and returns ==SUCCESS==. The framework checks the action mapping to see what page to load if ==SUCCESS== is returned. The framework tells the container to render as the response to the request, the resource HelloWorld.jsp
4. A pure HTML response is sent back to the browser.

## What to Remember

The framework uses Actions to process HTML forms and other requests. The ==Action== class returns a result-name such as ==SUCCESS==, ==ERROR== or ==INPUT==. Based on the mappings loaded from the struts.xml, a given result-name may select a page(as in this example), another action, or some other web resource(image, PDF).

When a server page is renderd, most often it will include dynamic data provided by the Action. To make it easy to display dynamic data, the framework provides a set of tags that can be used along with HTML markeup to create a server page.



# 配置文件

### 常量配置

### package元素

action配置包，Java代码中的“包”用来管理Java代码，放置命名冲突，package节点左右也一样，防止 action 冲突，在整个项目中指定一个唯一 name 属性

| 变量名       | 作用                                       |
| --------- | ---------------------------------------- |
| name      | 唯一标识                                     |
| namespace | 命名空间，影响所有该包下属的action访问路径                 |
| extends   | 指定继承 xx 配置文件，默认继承 strut-default.xml      |
| abstruct  | 定义为抽象包，只能别的包去继承，通常用于项目的默认配置可放在此包中(通用配置放在此抽象包) |

### Action元素

用于配置处理请求

| 变量名    | 作用               |
| ------ | ---------------- |
| name   | action的名字，影响访问路径 |
| class  | 指定 action 的全限定名  |
| method | 指定调用类的方法         |



### Result 元素

配置 action 的响应，一个 action 可配置多个

| 变量名  | 作用          |
| ---- | ----------- |
| name | 与类中方法的返回值一致 |
| type | 请求响应的类型     |



### 写作配置

对一个项目而言，通常配置文件可能会很多，此时可以采用包含 include 标签将各种配置文件分开存储

| 变量名                         | 作用       |
| --------------------------- | -------- |
| < include file="xxx.xml" /> | File文件路径 |



### default-action-ref 元素

默认 action 配置，通用防止URL输入错误，显示404错误页面，但该页面往往对用户不友好，同一用默认配置

### default-class-ref元素

```Xml
<default-action-ref name="default"></default-action-ref>
```

### global-results元素

全局结果集配置，可以配置多个action共享的结果集

```xml
 <global-results>
            <result name="error">/505.jsp</result>
 </global-results>
```



# 动态方法调用

通过 URL  指定调用的方法

## 动态配置

在不适用 action 的 method 通过 URL 指定嗲用后台的 xx 方法，使用action名!方法名，要实现通过URL 动态调用方法，需 "==struts.enable.DynaicMethodInvocation = true==" ，注意：在2.1以上的版本中默认为false

## 使用通配符

在 action 的 name 属性中可以使用 "*" 命名，那么在其他属性中需要使用 "{1}" 或者 "{2}" 或者 "{0}" 取出 URL 路径

```Xml
<action name="user_*" class="com.isaac.action.LoginAction" method="{1}">
            <result name="aaa">/login.jsp</result>
            <result name="success">/index.jsp</result>
</action>
```

范例：

| 命名              | 取值           |
| --------------- | ------------ |
| name = "user_*" | {1} 取第一个星号的值 |
| url=/user_add   | {1}=add      |

范例：

| 命名            | 取值                                       |
| ------------- | ---------------------------------------- |
| name = "*_ *" | {1} 取第一个星号，{2} 取第二个星号                    |
| url=/user_add | {1}=user 通常出现在class属性中，{2}=add通常出现在method属性中 |

{0} 所取出的数据就是类似如下效果：user_add

# 接受请求参数

Stust2 是 MVC 中的控制器（处理请求），那么在整个请求过程中，对参数的处理是Strust 的职责所在，接受和处理参数的分为属性驱动，模型驱动，域驱动

## 属性驱动

直接在 action 的 POJO 类中声明与表单元素一致的属性，并生产匹配的 get/set 方法，Struts会自动将表单数据提交到action中，并直接给相应属性赋值

范例：html 代码

```Html
<form action="login.action" method="post">
      用户名：<input type="text" name="username"><br/>
      密&nbsp;码：<input type="password" name="password"><br/>
      <input type="submit" value="提交" >
</form>
```

范例：Java代码

```Java
package com.Action;

import com.opensymphony.xwork2.ActionSupport;

public class LoginAction extends ActionSupport{
    //属性名与表单元素要一致
    private String username;
    private String password;
    private String rrors; //保存错误消息
    public String execute(){
        //逻辑判断 判断用户名和密码的正确性
        System.out.println(this);

        if(username.equals("admin") && password.equals("admin"))
            return SUCCESS;
        this.rrors = "用户名密码错误";
        return ERROR;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getRrors() {
        return rrors;
    }

    public void setRrors(String rrors) {
        this.rrors = rrors;
    }

    @Override
    public String toString() {
        return "LoginAction{" +
                "username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}

```



## 域驱动

通常情况下，表单元素往往对应一个实体，实体又往往放在实体层中，那么此时就需要在 action 中放入实体对象作为请求参数，域驱动的方式就是把实体中的对象作为 action 的属性

**范例**：html代码

```Html
<form action="login.action" method="post">
      用户名：<input type="text" name="user.username" value="${user.username}"><br/>
      密&nbsp;码：<input type="password" name="user.password"><br/>
      <input type="submit" value="提交" >
    </form>
```

**范例**：action 代码

```java
package com.Action;

import com.domain.User;
import com.opensymphony.xwork2.ActionSupport;

public class LoginAction extends ActionSupport{
    //属性名与表单元素要一致
    private User user;
    public String execute() {
        //逻辑判断 判断用户名和密码的正确性
        System.out.println(user);
        if (this.user.getUsername().equals("admin") && this.user.getPassword().equals("admin"))
            return SUCCESS;
        return "failure";
    }
    public User getUser() {
        return user;
    }
    public void setUser(User user) {
        this.user = user;
    }
}

```



## 模型驱动

Struts 2 中提供了一个叫模型驱动的拦截器，要使用词模型驱动填充参数，需 action 类实现 ModelDriven 接口

**范例**：html代码

```html
 <form action="login.action" method="post">
      用户名：<input type="text" name="username" value="${username}"><br/>
      密&nbsp;码：<input type="password" name="password"><br/>
      <input type="submit" value="提交" >
    </form>
```

**范例**：Java代码

```java
package com.Action;

import com.domain.User;
import com.opensymphony.xwork2.ActionSupport;
import com.opensymphony.xwork2.ModelDriven;

public class LoginAction extends ActionSupport implements ModelDriven<User>{
    //属性名与表单元素要一致
    private User user;

    public String execute() {

        //逻辑判断 判断用户名和密码的正确性
        System.out.println(user);

        if (this.user.getUsername().equals("admin") && this.user.getPassword().equals("admin"))
            return SUCCESS;
        return "failure";
    }

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }

    @Override
    public User getModel() {
        if(this.user==null)
            user = new User();
        return user;
    }
}

```



## 集合类参数

如果需要接受多个相同的对象参数，可以使用泛型集合，而不能采用数组方式



# 获取 ServletAPI

ServletAPI 中有些什么东西？ web容器提供的各种内置对象 request, session, application 等，在struts2 中要获取这些对象，可以通过三种方式：ActionContext(Strust建议的) , ServletActionContext(可以获取真正的HttpServletRequest等)

## 通过 ActionContext 获取

就是 action 的上下文，里面包含的每个请求的参数

**范例**：action代码

```Java
   public String execute(){
        ActionContext ctx = ActionContext.getContext();
        //request域中放入数据
        ctx.put("request","xixixixi");
        ///session域中放入数据
        ctx.getSession().put("session","hhh");
        //application 域中放入数据
        ctx.getApplication().put("application","eeee");
        return SUCCESS;
    }
```

**范例**：jsp代码

```Html
 <body>
    Request = ${requestScope.request}
    Session = ${sessionScope.session}
    Application = ${applicationScope.application}
 </body>
```

通过以上代码，发现我们可以给这些对象中放入数据，但是通过 ActionContext 操作的对象并不是真正的 HttpServletrerequest 、HttpServletResponse 只是 ActionContext 提供的一个 map 而已，这样设计是为了降低对 ServletAPI 的耦合度



##  通过 ServletActionContext 操作



## 通过实现特定接口获取

范例：action代码

```java
public void setSession(Map<String,Object> session){
  session.put("session","cccc");
  this.mysession = session;
}
```

范例：jsp代码

```html
request = ${requestScope.requeset}
session = ${sessionScope.session}
application = ${applicationScope.application}
```

 

# OGNL与值栈

## Strust 请求处理流程

Strust 接受请求并处理响应的流程

每次请求 Struts 会自动生成 ActionContext，这个对象就包含了请求所需的各种参数，并自动实例化Action类，调用自动的方法或者默认方法

在 ActionContext 中，全部是 Map 集合，主要包括六大对象：

request, session, application, attr, parameter, valuestack

## 值栈

值栈是 Struts 2 框架提供的一个存储区域，是一个栈结构，往往存储Action实例

观察值栈的内容，值栈只是ActionContext对象中六大集合中的一个

对值栈的操作