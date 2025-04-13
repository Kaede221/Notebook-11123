## 简介

正则虽小, 但是**不好学**, 有一定的难度的. 正则表达式不只限于 Python, 而是独立于编程语言, 用于处理复杂文本的强大的高级文本操作工具. 正则表达式来源于 Perl 语言, 其他的编程语言也是支持了 Perl 的这个正则语言. 基本上, 语法相似度高达 **90%**.

正则对于字符串的操作, 无非就是分割, 匹配, 查找和替换. 也就是四个字: **模糊查询**

比如说, 我现在有这样的一个字符串

```python
s = "yyt always loves lc but lc loves yyt forever"
```

我们可以使用字符串的一个方法 `.find()` 来查找是否出现, 并且获取出现的位置.

```python
s = "yyt always loves lc but lc loves yyt forever"
print(s.find("yyt")) # 0
print(s.find("lc")) # 17
```

这叫做**精准查询**, 不是正则. 这个例子中看不太出来, 不过我们可以换一个字符串:

```python
s = "11 always loves 123 but 123 loves 11 forever and today is 2025.4.13"
```

我想要找到所有的数字, 如果数据量很大, 我们就不能用人眼找了. 这里就需要使用正则表达式!

---

在 Python 中, 如果需要使用正则表达式, 需要引入一个标准库的模块 `re`. 我们需要匹配, 那么先输入匹配的规则, 再指定匹配的字符串, 即调用函数: `re.findall(规则, 字符串)`.

>  这里什么意思暂时不用知道, 看看就 OK

```python
import re
s = "11 always loves 123 but 123 loves 11 forever and today is 2025.4.13"
# 利用正则, 寻找所有的数字
ret = re.findall(r"\d+", s)
print(ret)
```

该段代码会输出一个列表: `['11', '123', '123', '11', '2025', '4', '13']`, 这就是正则表达式的用法了: **模糊的, 快速的匹配一个字符串中的相应字符**.

## 元字符

正则表达式其实就是两个知识点, 一个是元字符, 另一个是方法. 方法其实只有几个而已, 元字符反而是**最重要的**. 下面会记录最常用的 11 种元字符, 全部都需要掌握, 记住名字.

### 通配符

写出来其实就是一个句点号: `.`, 它可以匹配除了换行符 `\n` 以外的任何符号. 这里提供这样一个有很多单词组成的字符串, 大部分单词都是以 a 开头以 e 结尾:

```python
s = "age ape apple angle agree alone alive aside awake appl\ne"
```

正则表达式的匹配, 我们可以进行精准匹配, 比如我就要寻找 apple:

```python
import re
s = "age ape apple angle agree alone alive aside awake appl\ne"
ret = re.findall(r"apple", s)
print(ret) # ['apple']
```

这样可以, 但是没有必要; 更多的, 我们还是要进行模糊的匹配. 比如说, 我改一下, 查询 `a.e`, 则代表我要查询:

- 第一个字符是 a
- 第二个字符无所谓
- 第三个字符是 e

的单词. 调用如下代码, 可以得到结果.

```python
import re
s = "age ape apple angle agree alone alive aside awake appl\ne"
ret = re.findall(r"a.e", s)
print(ret) # ['age', 'ape', 'ake']
```

> [!note] 注意
> 这里的 ake 其实来自于 awake, 并不是错了, 而是匹配到了这一部分内容
> 另外, 假如有这样一个单词: `a\ne`, 也是不会被匹配的, 因为通配符无法匹配换行符

那么同理, 我想要匹配 `a...e`, 则会返回 `['apple', 'angle', 'agree', 'alone', 'alive', 'aside', 'awake']`, 可以使用代码进行验证.

总而言之, 一个点 `.`, 表示的其实就是任何字符 (除了换行符).

### 字符集

顾名思义, 也可以匹配字符, 但是范围更小一些了. 字符集写作 `[]`, 它会匹配一个中括号中的, 出现的任意字符符号, 字符之间不需要分隔符且数量不限. 不过这代表的是, 只要**符合其中的一个就算**.

例如这样一个字符串:

```python
s = "yys yyt ywt yot yyo yyk y1t"
```

我只想要匹配 y 开头, t 结尾, 中间只能是 y 或者 1, 则可以使用字符集的形式进行匹配:

```python
import re
s = "yys yyt ywt yot yyo yyk y1t"
ret = re.findall(r"y[y1]t", s)
print(ret) # ['yyt', 'y1t']
```

> [!info] 注意
> 匹配的规则是: 中间是 y **或者** 1, 所以如果你匹配字符串 `yy1t`, 并不符合规则.
> 如果你写的匹配表达式为 `y,1`, 也是不对的, 这样如果有字符串 `y,t`, 也会匹配成功

---

但如果我想要匹配很多东西, 比如 a~z 的所有小写字符呢? 可以使用字符集的一个 `-` 来表示一段范围. 比如我有如下的匹配, 寻找中间是小写字母的字符串:

```python
import re
s = "y12 yy1 y3t 4y5 666 yyt"
ret = re.findall(r"y[a-z].", s)
print(ret) # ['yy1', 'yyt']
```

同理, 大写字符也是一样的, 直接使用 `[A-Z]` 即可. 这样相较于上面的通配符, 就进行了一个范围的缩小.

```python
import re
s = "y12 yA1 y3t 4Z5 666 yyt"
ret = re.findall(r".[A-Z].", s)
print(ret) # ['yA1', '4Z5']
```

数字也是一样的, 从小到大, 就是 `[0-9]`, 这样就匹配了所有的数字.

```python
import re
s = "y12 yA1 y3t 4Z5 666 yyt"
ret = re.findall(r".[0-9].", s)
print(ret) # ['y12', 'A1 ', 'y3t', ' 4Z', ' 66']
```

> [!warning] 注意
> 这里由于我们使用了通配符 `.`, 所以空格也是会被识别的, 只要连续三个字符中间是数字, 就会被匹配返回, 所以出现了类似于 `A1 ` 这样的东西.

另外, 字符集中还有一种方式, 叫做取反. 比如我希望, 只要一个字符串中间的字符不是数字, 就成立, 则可以使用取反的写法. 取反写作 `^`, 会让当前字符集的内容取反. 如下, 会取出所有的, 中间不是数组的连续三个字符, **包括换行符**.

```python
import re
s = "y12 yA1 y3t 4Z5 666 yyt a\n1"
ret = re.findall(r".[^0-9].", s)
print(ret) # ['2 y', '1 y', '3t ', '4Z5', '6 y', 'yt ', 'a\n1']
```

---

综上, 如果我现在想要取出一段字符串中的所有非英文数字字符, 就可以直接使用取反+区间的方式了. 下面是一个案例:

```python
import re
s = "H3ll0 w@r|d y&t and L("
ret = re.findall(r"[^a-zA-Z0-9]", s)
print(ret) # [' ', '@', '|', ' ', '&', ' ', ' ', '(']
```

### 重复元字符

在之前的内容中, 匹配的都是一个字符, 就算是字符集, 也仅仅匹配的是一个字符. 但是我们可能会有如下需求: **匹配所有 a 开头的, e 结尾的单词.** 这个情况下, 我们之前学的就无法胜任了; 这里就可以使用重复元字符来实现.

重复元字符一共有四个: `{}`, `*`, `+`, `?`

> [!note] 四个字符的用法
> - `{n,m}` 数量范围贪婪符, 指定左边元字符的数量范围, 

