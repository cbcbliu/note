## 概述

MySQL是一个开放源代码的关系型数据库管理系统

## SQL概述

### SQL的分类

DDL: 数据定义语言 CREATR/ALTER/DROP/RENAME/TRUNCATE

DML: 数据操作语言 INSERT/DELETE/UPDATE/SELECT

DCL: 数据控制语言 COMMIT/ROLLBACK/SAVEPOINT/GRANT/REVOKE

### 规则规范

#### 大小写规范

MySQL在windows环境下大小写不敏感

在Linux环境下大小写敏感

- 数据库名、表名、表的别名、变量名是严格区分大小写
- 关键字、函数名、列名、列的别名是忽略大小写

<font color='red'>推荐统一书写规范</font>

- 数据库名、表名、表的别名、列名、列的别名等都小写
- SQL 关键字、函数名、绑定变量等都大写

#### 注释

注释单行：#(MySQL特有)、--加空格

```sql
#xxx(MySQL特有)
-- hhh必须加一个空格
```

注释多行：

```sql
/* xxx
   xxx
*/
```

导入.sql文件(命令行执行)：

```cmd
source 文件的全路径名
```

#### 命名规则

- 数据库、表名不得超过30个字符，变量名限制为29个
- 必须只能包含 A–Z, a–z, 0–9, _共63个字符
- 数据库名、表名、字段名等对象名中间不要包含空格
- 同一个MySQL软件中，数据库不能同名；同一个库中，表不能重名；同一个表中，字段不能重名 必须保证你的字段没有和保留字、数据库系统或常用方法冲突。如果坚持使用，请在SQL语句中使 用`（着重号）引起来
- 保持字段名和类型的一致性，在命名字段并为其指定数据类型的时候一定要保证一致性。假如数据 类型在一个表里是整数，那在另一个表里可就别变成字符型了

举例：

```sql
#以下两句是一样的，不区分大小写
show databases;
SHOW DATABASES;
#创建表格
#create table student info(...); #表名错误，因为表名有空格
create table student_info(...);
#其中order使用``飘号，因为order和系统关键字或系统函数名等预定义标识符重名了
CREATE TABLE `order`();
select id as "编号", `name` as "姓名" from t_stu; #起别名时，as都可以省略
select id as 编号, `name` as 姓名 from t_stu; #如果字段别名中没有空格，那么可以省略""
select id as 编 号, `name` as 姓 名 from t_stu; #错误，如果字段别名中有空格，那么不能省略""

```

### 基本的SELECT语句
