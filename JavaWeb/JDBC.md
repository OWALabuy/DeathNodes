# JDBC简介

全称 J\*\*a DataBase Connectivity，J\*\*a数据库连接

jdbc是用j\*\*a语言操纵关系型数据库的一套API

1. 在官网下载数据库对应的驱动 要下载对应版本的jar包
2. 在j\*\*a项目中创建一个lib文件夹 与src平缓的 把jar包塞进去
3. 对那个jar包点右键 选择Add as Library
4. 创建一个类 开始写对应的代码
```java
package com.example;

import java.sql.Statement;
import java.sql.Connection;
import java.sql.DriverManager;

public class Main {
    public static void main(String[] args) throws Exception {
        //注册驱动
        Class.forName("com.mysql.jdbc.Driver");

        //获取数据库连接
        String url = "jdbc:mysql://127.0.0.1:3306/ShiFangStudio";
        String username = "root";
        String password = "#c8BF6AB";
        Connection conn = DriverManager.getConnection(url, username, password);

        //定义sql
        String sql = "insert into `member` values('正经的紫星辰', 610610415);";

        //获取执行sql的对象
        Statement stmt = conn.createStatement();

        //执行sql 返回值是影响的行数
        int count = stmt.executeUpdate(sql);

        //处理返回的结果
        System.out.println(count);

        //释放资源
        stmt.close();
        conn.close();
    }
}
```

我原先是用VSC的 因为迷你脚本写多了 习惯了VSC的风格和手感 可是16号我配置了半天 硬是连接不上 只好去下载了一个IntelliJ IDEA Community Edition 集成开发环境还是适合我这样的小菜鸡awa

运行代码的时候确保你的数据库连接正在运行 我已经成功连上我的数据库喽

![NBZX_2X5VU_` 5E GE0YD7F](https://github.com/OWALabuy/DeathNodes/assets/134919443/d9715e7b-02fc-4838-9935-413e657b9a18)


我在数据库中定义了一个放成员的名字和迷你号的表
```sql
SHOW DATABASES;

CREATE DATABASE `ShiFangStudio`;

USE `shifangstudio`;

CREATE TABLE `member`(
	`name` VARCHAR(20),
    `uin` INT PRIMARY KEY
);

insert into `member` values("欧阳闻奕", 528278703);

SELECT * FROM `member`;
```

# JDBC API

### DriverManager驱动管理类
#### 作用：
1.注册驱动
2.获取数据库连接
### Connection
#### 作用：
1.获取执行SQL的对象
- 普通执行SQL对象
```java
    Statement createStatement()
```
- 预编译SQL的执行SQL对象：防止SQL注入
```java
    PreparedStatement prepareStatement()
```
- 执行存储过程的对象
```java
    CallableStatement prepareCall()
```
2.管理事务
- MySQL事务管理

        开启事务：BEGIN;/ START TRANSACTION
        提交事务：COMMIT;
        回滚事务：ROLLBACK
        MySQL默认自动提交事务

- JDBC事务管理：Connection接口中定义了3个对应的方法

        开户事务：setAutoCommit(boolean AutoCommit) 参数是一个布尔值 true为自动提交事务 false为手动提交事务 执行这个方法时 开户事务
        提交事务：commit()
        回滚事务：rollback()
以下是一个示例代码
```java
public class Main {
    public static void main(String[] args) throws Exception {
        //注册驱动
        Class.forName("com.mysql.jdbc.Driver");

        //获取数据库连接
        String url = "jdbc:mysql://127.0.0.1:3306/ShiFangStudio";
        String username = "root";
        String password = "#c8BF6AB";
        Connection conn = DriverManager.getConnection(url, username, password);

        //定义sql
        String sql1 = "insert into `member` values('丶黎.奕凡.°', 363900548);";
        String sql2 = "insert into `member` values('正经的紫星辰', 610610415);";

        //获取执行sql的对象
        Statement stmt = conn.createStatement();

        //尝试开启一个事务 执行sql
        try{
            //开启事务
            conn.setAutoCommit(false);
            //执行SQL
            int count1 = stmt.executeUpdate(sql1);
            //处理返回的结果
            System.out.println(count1);

            int count2 = stmt.executeUpdate(sql2);
            System.out.println(count2);

            //如果没有问题 就提交事务
            conn.commit();
        } catch (Exception throwables){
            //执行过程中出现问题 就要回滚事务
            conn.rollback();
            throwables.printStackTrace();
        }

        //释放资源
        stmt.close();
        conn.close();
    }
}
```
### Statement
#### 作用：
1.执行SQL语句
#### 执行SQL语句：

    int executeUpdate(sql)：执行DML、DDL语句
    返回值：(1)DML语句影响的行数 (2)DDL语句执行后 执行成功也可能返回0

    ResultSet executeQuery(sql)：执行DQL语句
    返回值：ResultSet结果集对象
### ResultSet
- Result(结果集对象)作用：
1.封装了DQL查询语句的结果
```java
ResultSet stmt.executeQuery(sql)：执行SQL语句 返回ResultSet对象
```
- 获取查询结果
```
boolean next()：(1)将光标从当前位置向前移动一行 (2)判断当前行是否为有效行
返回值：
    true 有效行 当前行有数据
    false 无效行 当前行没有数据


xxx getXxx(参数)：获取数据
xxx:数据类型 如：int getInt(参数); String getString(参数)
    参数：
    int：列的编号 从1开始
    String：列的名称
```
- 使用步骤
1. 游标向下移动一行 并判断该行是否有数据：next()
2. 获取数据：getXxx(参数)
```java
//循环判断游标是否是最后一行末尾
while(rs.next()){
    //获取数据
    rs.getXxx(参数);
}
```
- 一个例子
```java
public class Main {
    public static void main(String[] args) throws Exception {
        //注册驱动 已经注册过了 就不用再次执行这个语句了
        //Class.forName("com.mysql.jdbc.Driver");

        //获取数据库连接
        String url = "jdbc:mysql://127.0.0.1:3306/ShiFangStudio";
        String username = "root";
        String password = "#c8BF6AB";
        Connection conn = DriverManager.getConnection(url, username, password);

        //定义sql
        String sql = "select * from `member`;";

        //获取执行sql的对象
        Statement stmt = conn.createStatement();

        //执行sql 获取一个ResultSet对象
        ResultSet rs = stmt.executeQuery(sql);

        //处理结果 遍历rs中的所有数据
        while(rs.next()){
            //获取数据
            //这两个函数的参数是数据库的表的列序号 第一列是成员的名字 第二列是迷你号
            String name = rs.getString(1);
            int UIN = rs.getInt(2);

            //分别打印出他们的名字和迷你号
            System.out.println(name);
            System.out.println(UIN);
            System.out.println("---------------");
        }

        //释放资源
        rs.close();//因为多开了一个rs对象 这个也要释放掉
        stmt.close();
        conn.close();
    }
}
```

其实它的参数也不一定要用列的序号的 用属性名字也是可以的awa
比如
```java
        //处理结果 遍历rs中的所有数据
        while(rs.next()){
            //获取数据
            //这两个函数的参数是数据库的表的属性名 第一列是成员的名字 第二列是迷你号
            String name = rs.getString("name");
            int UIN = rs.getInt("uin");

            //分别打印出他们的名字和迷你号
            System.out.println(name);
            System.out.println(UIN);
            System.out.println("---------------");
        }
```
### PreparedStatement