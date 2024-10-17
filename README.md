
### 一、前言


随着 Solon 3\.0 版本发布，新添加的 SqlUtils 接口，用于操作数据库，SqlUtils 是对 Jdbc 原始接口的封装。适合 SQL 极少或较复杂，或者 ORM 不适合的场景使用。


### 二、SqlUtils 使用


#### 1、引入依赖



```
<dependency>
    <groupId>org.noeargroupId>
    <artifactId>solon-data-sqlutilsartifactId>
dependency>

```

#### 2、新建数据库表（for MySql）



```
CREATE TABLE `book`  (
  `id` bigint(20) UNSIGNED ZEROFILL NOT NULL AUTO_INCREMENT,
  `name` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  `author` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 2 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = DYNAMIC;

```

#### 3、定义实体类


使用了 lombok 的注解。



```
@Data
public class Book {
    private Long id;
    private String name;
    private String author;
}

```

#### 4、添加数据源配置



```
solon.dataSources:
  book!: # ‘!’结尾表示默认
    class: "com.zaxxer.hikari.HikariDataSource"
    jdbcUrl: jdbc:mysql://localhost:3306/book?useUnicode=true&characterEncoding=utf8&autoReconnect=true&rewriteBatchedStatements=true
    driverClassName: com.mysql.cj.jdbc.Driver
    username: root
    password: 123456

```

#### 5、注入 SqlUtils 并使用


注入



```
@Component
public class BookDao {
    @Inject
    private SqlUtils sqlUtils;
}

```

查询操作



```
public List getAllBooks() {
    return sqlUtils.sql("select * from book")
                   .queryRowList()
                   .toBeanList(Book.class);
}


```

新增操作



```
public Long addBook(Book book) {
    return sqlUtils.sql("INSERT INTO book (name , author) VALUES (?,?)", book.getName(), book.getAuthor())
                   .updateReturnKey();
}

```

更新操作



```
public int updateBook(Book book) {
    return sqlUtils.sql("UPDATE book SET name=?, author=? WHERE id=?", book.getName(), book.getAuthor(), book.getId())
                   .update();
}

```

### 总结


使用 SqlUtils 可以完成基本的数据库操作，有更好的透明性。当然，持久化数据库操作 ORM 是更方好方案。具体根据开发决定。


 本博客参考[蓝猫机场加速器](https://dahelaoshi.com)。转载请注明出处！
