## 介绍

> [!note]
> 可以直接前往本项目的[Github仓库](https://github.com/iamjazz/Md5collision)下载该程序.

## 使用方式

下载后, 可以添加到环境变量方便使用, 或者单独放在一个目录里面; 下面将会直接使用.

可以使用命令 `.\fastcoll.exe -h` 查看所有可选的参数:

![[attachments/Pasted image 20250412124844.png]]

首先, 我们在目录中, 创建一个txt文件, 里面内容可以随便写, 比如下面写 **kaede** . 随后执行如下命令即可:

```shell
.\fastcoll.exe -p .\test.txt -o .\output1.txt .\output2.txt
```

然后目录中就会出现这样的两个文件了!

![[attachments/Pasted image 20250412125525.png]]

查看发现, 两个文件的MD5是相同的, 至此就实现了MD5碰撞:

![[attachments/Pasted image 20250412125840.png]]

随后, 我们需要传参, 可以使用如下php脚本:

```php
<?php
highlight_file(__FILE__);
$a = $_GET['a'];
$b = $_GET['b'];
echo "ans1: " . urlencode(file_get_contents($a)) . "\n";
echo "ans2: " . urlencode(file_get_contents($b)) . "\n";
```

读取两个文件即可得到两个url参数. 不妨尝试一下强类型的绕过:

```
ans1:
kaede%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%ED%2C%02p%237%C3%E1%CA%DD%80s%3Dtg%5D%14%5B%E2%ED%2B%3E%16O%0Fr%80%89%B5%A5%23%8F%96%11%0B%EAL%5E%8B%60%CF%09%40%82%F1%C1Ma%0B%9Fm%CD%D3%21MB%916%8F%BE56%89%9A%F5%CF%F6%9B%C1%C5l%AA%18n%80io%AE%1A%10%98%2F%05Z%27%C6F%A7%D3%1A%FEG%EC%07%DE%EBNm%9E%94%F2%16%00%13%B8sDwo%04W%AA%FEP%11%C2%04%AD%E0%D1%B3%CCo%23%B4%0A9%82
ans2:
kaede%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%ED%2C%02p%237%C3%E1%CA%DD%80s%3Dtg%5D%14%5B%E2m%2B%3E%16O%0Fr%80%89%B5%A5%23%8F%96%11%0B%EAL%5E%8B%60%CF%09%40%82%F1ANa%0B%9Fm%CD%D3%21MB%916%8F%3E56%89%9A%F5%CF%F6%9B%C1%C5l%AA%18n%80io%AE%1A%10%98%2F%05%DA%27%C6F%A7%D3%1A%FEG%EC%07%DE%EBNm%9E%94%F2%16%00%13%B8sDwo%84V%AA%FEP%11%C2%04%AD%E0%D1%B3%CCo%A3%B4%0A9%82
```

使用题目验证, 绕过成功!

![[attachments/Pasted image 20250412131009.png]]