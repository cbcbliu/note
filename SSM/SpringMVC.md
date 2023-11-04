## SpringMVC

## 简介

Spring Web MVC是基于Servlet API构建的原始web框架，从一开始就包含在Spring framework中。正式名称“Spring Web MVC”来自其源模块的名称(spring-webmvc)，但它通常被称为"Spring MVC"。

## 快速案例

引入依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.0.13</version>
</dependency>
<dependency>
    <groupId>jakarta.platform</groupId>
    <artifactId>jakarta.jakartaee-web-api</artifactId>
    <version>10.0.0</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>6.0.13</version>
</dependency>
		<!--josn 相关-->
		<dependency>
            <groupId>org.eclipse</groupId>
            <artifactId>yasson</artifactId>
            <version>3.0.3</version>
        </dependency>
        <dependency>
            <groupId>javax.json</groupId>
            <artifactId>javax.json-api</artifactId>
            <version>1.1.4</version>
        </dependency>
        <dependency>
            <groupId>org.glassfish</groupId>
            <artifactId>javax.json</artifactId>
            <version>1.1.4</version>
        </dependency>
```

配置类

```java
@Controller
public class HelloController {

    @RequestMapping("springmvc/hello")
    @ResponseBody//直接返回字符串，不找视图
    public String hell(){
        System.out.println("HelloController.hello");
        return "hello springmvc";
    }

}

@Configuration
@ComponentScan("com.cbliu.controller")
public class MvcConfig {
    
    @Bean
    public RequestMappingHandlerMapping handlerMapping(){
        return new RequestMappingHandlerMapping();
    }

    @Bean
    public RequestMappingHandlerAdapter handlerAdapter(){
        return new RequestMappingHandlerAdapter();
    }

}
public class SpringMvcInit extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[0];
    }
    //设置项目对应的配置
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{MvcConfig.class};
    }
    //配置springmvc内部自带的servlet访问地址
    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
```

## 参数接收

### param参数

- 直接接收，参数名相同，可以不传，不会报错

```java
@RequestMapping("data")
public String data(String name ,int age){
    System.out.println("name = " + name + ", age = " + age);
    return "name = " + name + ", age = " + age;
}
```

- @ResquestParam指定参数名，required默认true

```java
@RequestMapping("data2")
@ResponseBody
public void data2(@RequestParam(value = "account") String username ,
                    @RequestParam(required = false) int page){
    
}
```

- 集合接值，一个key多个值(必须加RequestParam注解)

```java
	@RequestMapping("data3")
    @ResponseBody
    public String data3(@RequestParam List<String> list){
        System.out.println("list = " + list);
        return "ok";
    }
```

- 实体对象接值(参数对应实体对象的字段)

```java
	@RequestMapping("data4")
    @ResponseBody
    public String data4(User user){
        System.out.println("user = " + user);
        return "ok";
    }
```

### path参数

```java
@RequestMapping("/{account}/{password}")
public String login(@PathVariable String account, 
                    @PathVariable String password){
        return "";
}
```

### json接收

```java
@PostMapping("data")
public String data(@RequestBody User user){
    System.out.println("user = " + user);
    return user.toString();

}
```

### Cooike接收

```java
@RequestMapping("data")
public String data(@CookieValue(value = "cookieName") String value){
    System.out.println("value = " + value);
    return value;

}
//模拟存cookie
@GetMapping("save")
public String save(HttpServletResponse response){
    Cookie cookie = new Cookie("cookieName","root");
    response.addCookie(cookie);
    return "ok";

}
```

### 请求头header接收

```java
@GetMapping("data")
public String data(@RequestHeader("Host") String host){
    System.out.println("host = " + host);
    return "host = " + host;

}
```

## Rsetful风格

路径url为名词对应资源不含动作，使用POST/DELETE/PUT/GET四种请求方式分别对应增、删、改、查

## 全局异常处理

```java
@RestControllerAdvice
public class Xxx{
	@ExceptionHandler(XxxException.class)
    public Object xxxHandler(){
        String message = e.getMessage();
        return message;
    }
}
```

## 参数校验jsr303

