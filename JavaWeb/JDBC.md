# JDBC

全称 J**a DataBase Connectivity J**a数据库连接

jdbc是用j**a语言操纵关系型数据库的一套API

1. 在官网下载数据库对应的驱动 要下载对应版本的jar包
2. 在j**a项目中创建一个lib文件夹 与src平缓的 把jar包塞进去
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
