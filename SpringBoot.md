## 快速案例

**1.引入依赖**

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.1.4</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

2.启动类

启动类内置tomcat

```java
@SpringBootApplication
public class Main {
    public static void main(String[] args) {
        SpringApplication.run(Main.class,args);
    }
}
```

**3.配置文件**

配置文件在resource文件夹下，名字为application.properties或者application.yml

```properties
#springboot提供的配置，修改程序的参数，key是固定的
server.port=80
server.servlet.context-path=/hhh

#自定义配置，使用:@Value("${cbliu.name}")
cbliu.name=bin
cbliu.age=18
```

```yml
server:
  port: 8088
  servlet:
    context-path: /boot
#使application-xxx.yml也同时生效,外部的配置key和本身的重复时，外部覆盖自身
spring:
	profiles:
		active: xxx,xxxx
	web:
		resource:
			static-locations: classpath:/xxx
			#配置静态资源文件夹，一旦配置了默认的配置就失效了
			#默认配置(static,public,resources,META-INF/resources).外部访问静态资源时不需要写静态资源文件夹

#自定义配置
cbliu:
  name: bin
  password: 145
  gfs:
    - hh
    - ee
    - gg
```

读取值

```java
@Data
@Component
@ConfigurationProperties(prefix = "cbliu")
//@Value("${cbliu.gfs}")只能读取单个值，不能读集合
//@ConfigurationProperties可以批量读取，且可以读取集合,字段名等于key驱动prefix的值
public class User {

    //@Value("${cbliu.name}")
    private String userName;

    //@Value("${cbliu.password}")
    private String password;

   //@Value("${cbliu.gfs}")
    private List<String> gfs;

}

```

## 整合mybatis

引入依赖

```xml
 <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.1.4</version>
 </parent>
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>3.0.2</version>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
        <version>3.1.4</version>
    </dependency>

    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-3-starter</artifactId>
        <version>1.2.20</version>
    </dependency>

    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.33</version>
    </dependency>
</dependencies>
```

## maven打包插件

java -jar xxx.jar部署运行

-Dspring.port=9888 -Dspring.profiles.active=xxx,yyy（添加参数指定）

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>2.5.15</version>
        </plugin>    
    </plugins>
</build>
```
