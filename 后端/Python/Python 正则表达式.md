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