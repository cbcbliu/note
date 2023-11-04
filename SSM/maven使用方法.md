### 基本构成

依赖传递：导入依赖时，会自动导入依赖的依赖

依赖冲突：发现已经存在的依赖时会终止依赖传递，避免循环传递

依赖下载失败：1.删除本地仓库的.lastupdate文件 2.检查输入是否有误 3.检查仓库

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">  
  <modelVersion>4.0.0</modelVersion>  
  <!--    gav 三个属性定位识别项目  packaging：打包方式 war-web项目，jar-普通项目，pom-不打包-->
  <groupId>com.cbliu</groupId>  
  <artifactId>maven-javase-project-01</artifactId>  
  <version>1.0-SNAPSHOT</version>  
  <packaging>war</packaging>

<!--  声明版本号-->
  <properties>
    <!--    声明版本号之后在其他地方引用${version}    -->
    <jackson.version>2.15.3</jackson.version>
  </properties>

<!--  第三方依赖
      dependencies:依赖集合
      dependency每个依赖项目：gav定位识别
      获取第三方依赖的方式：
        1.maven提供的官网查询 mvnrepository.com(速度慢)
        2.安装插件maven-search
       拓展：
          1.提取版本号，同一管理
          2.可选属性scope
            scope -> 引入依赖的作用域
            默认值：compile(main、test、打包运行)
            test：只能在test里用 例如：junit
            runtime：只有打包和运行  例如：mysql
            provide：main、test  例如：servlet,httpServlet   
-->
  <dependencies>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>${jackson.version}</version>
      <scope>compile</scope>
    </dependency>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13.2</version>
    </dependency>
  </dependencies>


</project>


```

### 常用命令

**注意：命令执行时需要在项目根目录**

mvn clean ：清理编译或打包后的项目结构，删除target文件夹

mvn compile：编译项目，生成target文件

mvn test：执行测试源码

mvn package：打包项目，生成war/jar文件 

mvn install：打包后上传到maven本地仓库(本地部署)

mvn deploy：只打包，上传到maven私服仓库(私服部署)

*mvn clean install常用*

### Maven继承和聚合性

#### 继承

作用：子工程会继承父工程的配置信息，在父工程中统一管理项目中的依赖信息，进行统一版本管理

例.

父工程：

```xml
<!-- 父工程不打包，也不写代码 -->
<packaging>pom</packaging>
<!-- dependencyManagement声明依赖，不会下载依赖，可以被子工程继承版本号 -->
<dependencyManagement>
    <dependencies>
        <dependency>...</dependency>
    </dependencies>
</dependencyManagement>
```

子工程：

```xml
<parent>  gav三组值  </parent>
<!-- 只需要artifactId，groupId和version继承父工程，也可以自己写覆盖 -->
<artifactId>example</artifactId>
<dependencies>
    	<!-- 如果父工程有声明的依赖，不需要写版本号，写了会进行覆盖	-->
        <dependency>...</dependency>
    </dependencies>

```

#### 聚合

父工程执行构建时，会统一执行构建子工程并优化构建顺序(例如子工程间有依赖关系时，先构建被依赖的)

父工程：

```xml
<!-- 统一管理子模块 -->
<modules>
	<module>...</module>
</modules>
```

