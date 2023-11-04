## MyBatis简介

### 1.简介

官方文档：https://mybatis.org/mybatis-3/zh/index.html

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

### 2.持久层框架对比

**JDBC**

- SQL夹杂在Java代码中耦合度高，导致硬编码内伤
- 维护不易且实际开发需求中SQL有变化，频繁改的情况多见
- 代码冗长，开发效率低

**Hibernate和JPA**

- 操作简便，开发效率高
- 程序中的长难复杂SQL需要绕过框架
- 内部自动生成的SQL，不容易做特殊优化
- 基于全映射的全自动框架，大量字段的POJO进行部分映射时比较困难
- 反射操作太多，导致数据库性能下降

**MyBatis**

- 轻量级，性能出色
- SQL和Java代码编码分开，功能边界清晰。Java代码专注业务、SQL语句专注数据
- 开发效率稍逊于Hibernate

开发效率：Hibernate>MyBatis>JDBC

运行效率：JDBC>MyBatis>Hibernate

### 3.快速案例

- 引入依赖

  ```xml
  <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.13</version>
  </dependency>
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.33</version>
  </dependency>
  ```

- 创建接口

  ```java
  public interface EmployeeMapper {
  
      Employee queryById(Integer id);
  
      int deleteById(Integer id);
  
  }
  ```

- xml配置对应的EmployeeMapper.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--    namespace : mapper对应的全限定符-->
  <mapper namespace="org.cbliu.mapper.EmployeeMapper">
  
  <!--    每个标签对于接口的一个方法 注意:mapper接口方法不能重载-->
  
      <select id="queryById" resultType="org.cbliu.pojo.Employee">
          select emp_id empId,emp_name empName,emp_salary empSalary from t_emp where emp_id = #{id}
      </select>
      <delete id="deleteById">
          delete from t_emp where emp_id = #{id}
      </delete>
  </mapper>
  ```

- mybitis-config.xml配置

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "https://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
  <!--                数据库连接信息-->
                  <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                  <property name="url" value="jdbc:mysql://localhost/cbliu"/>
                  <property name="username" value="root"/>
                  <property name="password" value="root"/>
              </dataSource>
          </environment>
      </environments>
      <mappers>
  <!--        mapper注册-->
          <mapper resource="mappers/EmployeeMapper.xml"/>
      </mappers>
  </configuration>
  ```

- 使用

  ```java
  //1.读取配置文件
  InputStream stream = Resources.getResourceAsStream("mybatis-config.xml");
  //2.创建sqlSessionFactory
  SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(stream);
  //3.根据sqlSessionFactory创建sqlSession（每次业务创建一个，用完就释放）
  SqlSession sqlSession = sqlSessionFactory.openSession();
  //4.获取接口的代理对象（代理技术） 调用代理对象的方法，就会查找mapper接口的方法
  EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
  Employee employee = mapper.queryById(1);
  System.out.println(employee);
  //5.提交事务
  sqlSession.commit();
  sqlSession.close();
  ```

## MyBatis基本使用

### mybatis日志输出配置

```xmll
    <settings>
<!--        开启日志，system控制台输出-->
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
```

### SQL参数

```xml
<!-- 
	#{key}: 占位符 + 赋值
	${key}: 字符串拼接
	推荐使用#{key}可防止注入攻击
	占位符?只能替代值的位置，不能替代容器名(标签，列名，sql关键字)
	例sql. select * from table where &{column} = #{columnValue}
-->
<select id="queryById" resultType="org.cbliu.pojo.Employee">
    select emp_id empId,emp_name empName,emp_salary empSalary from t_emp where emp_id = #{id}
</select>
```

### 数据输入

```xml
<!-- 1.传入单个简单类型,key随意写，一般情况写参数名-->
<delete id= "xxx">
    delete from table where id = #{id}
</delete>
<!-- 2.传入单个实体参数类型,key=字段名-->
<update id= "insertXxx">
    insert into table(column...) values(#{field1},#{field2}...)
</update>
<!-- 3.传入多个简单类型
		方案1(推荐)：List<Xxx> queryXxx(@Param("a") String key,@Param("b") String key2);
			key为@Param("value")的value
		方案2：mybatis默认机制，按形参列表递增，#{arg0},#{arg1}...或者#{param1},#{param2}...
-->
<seletc id="queryXxx">
	select column... from table where column1=#{a} and column2=#{b}
</seletc>

```

### 数据输出

```xml
<!-- 1.返回单个简单类型,resultType:类的全限定符，别名(首字母小写)-->
<seletc id="queryXxx" resultType="java.lang.String 或 string">
	select column... from table where id=#{id}
</seletc>
<!-- 2.返回单个自定义类型
		要求列名和自定义类字段名相同才能映射
  		可开启自动映射配置：即驼峰命名aaBb => aa_bb, <setting name="mapUnderscoreToCamelCase" value="true"/>
-->
<seletc id="queryXxx" resultType="xxx">
	select column1 field1,column2 field2,... from table where id=#{id}
</seletc>
<!-- 3.返回集合类型
		resultType指定集合的范形
-->
<seletc id="queryXxx" resultType="string">
	select column from table where salary=#{salary}
</seletc>
<seletc id="queryAll" resultType="employee">
	select * from table
</seletc>
<!-- 4.返回插入数据的主键
		自增长主键回显 mysql auto_increment
		useGeneratedKeys=true 
		keyColumn="emp_id" 主键列的值
		keyProperty="empId" 接收主键列值的字段
-->
<insert id="xxx" useGeneratedKeys=true keyColumn="emp_id" keyProperty="empId">
	insert into table (name,salary) values(#{name},#{salary})
</insert>

```

自定义别名(在配置文件xml)

```xml
<typeAilases>
    <!-- 单个定义 -->
	<typeAlias type="com.cbliu.Xxx" alias="xxx" />
    <!-- 批量将指定包下的类赋予别名，别名为类首字母小写
 		 若包下个别类需要特殊指定别名：在类上添加注解@Alias("alias")
	-->
    <typeAlias package="com.cbliu.xx" />
</typeAilases>
```

非自增长主键维护

```xml
<insert id="insertTeacher">
    	<!-- 插入之前，先指定一段sql语句，生成一个主键值
 				order:BEFORE/AFTER 插入sql语句执行时机
				resultType: 返回值类型
				keyProperty: 查询结果给那个属性赋值
		-->
    	<selectKey order="BEFORE" resultType="string" keyProperty="tId">
            select replace(uuid(),'-','')
    	</selectKey>
        insert into teacher(t_id,t_name) values(#{tId},#{tName});
</insert>
```

resultMap自定义映射

```xml
<resultMap id="tMap" type="Xxx">
    <id column="t_id" property="tId"/>
    <result column="t_name" property="tName"/>
</resultMap>
<select id="xxx" resultMap="tMap"> 
    slect * from table where id = #{id}
</select>
```

## MyBatis多表映射

自定义resultMap

## 动态SQL

