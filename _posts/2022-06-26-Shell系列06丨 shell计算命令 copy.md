---
layout: post
title: 2022-06-26-Shell系列06丨 shell计算命令
tag: Shell
---

# 一.`expr`命令详解

## 1.介绍

expr是(evaluate expressions的缩写)，译为“表达式求值”。shell expr是一个功能强大，并且比较复杂的命令，它除了可以实现整数计算，还可以结合一些选项对字符串进行处理，例如计算字符串长度、字符串比较、字符串匹配、字符串提取等。

## 2.求值表达式的用法

```
expr 算术运算符表达式
# 例如： expr 1 + 1 返回：2
# 例如： expr \( 10 + 10 \) \* 2 + 100  返回140
```

## 3.字符串的用法

计算字符串的长度语法:  expr length 字符串

```
expr length  "abc"   返回3
```

截取字符串语法:  expr substr 字符串 start end

```
expr substr "abcd" 1 2  返回：ab
```

获取第一个字符在字符串中出现的位置语法: expr index 被查找字符串 需要查找的字符串

```
expr index  "abcd"  c 返回：3
```

正则表达式匹配语法1：expr match 字符串 正则表达式

```
# 正则表达式默认带 ^ ,代表以什么开头
# 返回值为符合匹配字符的长度，否则返回为0
正则表达式通配符"."代表任意一个字符
正则表达式通配符"*"代表签名的字符可以出现0到多次
".*m" 含义为匹配字符串中m前面的字符串长度

expr match "agc" ".*g"   返回：2
```

正则表达式匹配语法2，功能和语法1一样：expr 字符串 ： 正则表达式

```
# 正则表达式默认带 ^ ,代表以什么开头
# 返回值为符合匹配字符的长度，否则返回为0

expr "agc" : ".*g"    返回：2
```

# 二.`(())`命令详解

## 1.介绍

双小括号`(())`,用于进行数学运算表达式的执行，将数学运算表达式放在
`((` 和 `))` 之间。

可以使用 `$` 获取 `(())` 表达式命令的结果，这和使用`$`获得变量值是一样的。

## 2.语法

```
((表达式))
```
## 3.用法

![20230205155952](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20230205155952.png)

演示：

![20230205160751](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20230205160751.png)


# 三.`let`命令详解

## 1.目标

使用`let`进行整数的数学运算赋值

## 2.介绍

`let`命令和`(())`在数值计算方面功能一样，但是没有`(())`功能强大，`let`只能用于赋值计算，不能直接输出，不可以和条件判断一起使用。

## 3.语法

```
let 赋值表达式
```

![20230205161847](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20230205161847.png)

## 4.小结

`let`作用：用于赋值，是最简洁的整数运算赋值命令

计算赋值用法：let变量名=整数运算表达式

多个表达式计算赋值用法：let 变量名1=整数运算表达式1 变量名2=整数运算表达式2 ...

# 四.`$[]`命令详解

## 1.目标

使用`$[]`进行整数的数学运算

## 2.介绍

和`(())`、`let` 命令类似，`$[]`也只能进行整数运算，但是只能多单个表达式的计算求值与输出。

## 3.语法

```
$[表达式]
```

> 1.`$[]`会对表达式进行计算，并取得计算结果
> 
> 2.表达式内部不可以赋值给变量

```
a=$[1+6]
echo $a
b=$[a-1]
echo $b

echo "$[1+6],$[a-1],$[a+b]"
```

## 小结

![20230205170717](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20230205170717.png)


# 五.`bc`命令详解

## 1.目标

- 理解bc命令的作用
- 能够在bc中进行互动式的数学运算
- 能够在shell中进行非互动式的bc管道运算
- 能够在shell中非互动式的输入重定向bc运算

## 2.介绍

Bash shell内置了对整数运算的支持，但是并不支持浮点运算，而linux bc(basic calculator)命令可以很方便的进行浮点运算。bc命令是linux简单的计算器，能进行进制转换与计算。能转换的进制包括十六进制、十进制、八进制、二进制等。可以使用的运算符号包括(+)加法、(-)减法、(*)乘法、(/)除法、(^)指数、(%)余数等。

## 3.语法

```
bc [options] [参数]
```

**options**

![20230205172947](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20230205172947.png)

**参数**

文件：指定包含计算任务的文件

演示：bc执行计算任务的文件

创建task.txt，编辑内容,一行一个表达式

```
12*13+12
56+22*22
```

执行命令：bc -q task.txt ，执行完成后`quit`退出

**内置变量**

![20230205173808](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20230205173808.png)

**内置数学函数**

![20230205173906](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20230205173906.png)


## 4.作用

- 1.bc中互动式的数学运算
- 2.shell中非互动式的管道运算
- 3.shell中非互动式的输入重定向运算

## 5.互动式的数学运算

演示

```
bc -q
1+2
# 3

10/3
# 3

# 保存两位小数
scale=2;10/3
# 3.33

# 获取上次结果
last
# 3.33

# 把7转为二进制
obase=2;7
# 111

# 输入二进制，输出十进制，这时候结果是有问题的，需要`quit`退出重新进入`bc`
ibase=2;obase=10;111   # 会以为111是十进制，所以会原样返回111
# 退出
quit
bc -q
ibase=2;obase=10;111   # 返回正确的值  7
```

内置函数必须使用`-l`(mathlib)标准数学库

```
bc -ql
e(2)
# 7.38905609893065022723
```

## 6.非互动式的管道运算

### 1.介绍

在shell脚本中，可以借助管道使用bc计算器。

### 2.直接进行bc的表达式计算输出

语法：

```
echo "expression" | bc [options]
```
> "expression" 表达式必须符合bc命令要求的公式
>
> "expression" 表达式里面可以引用shell变量
>
> 例如： shell变量 `a=2` 在表达式里面引用的语法：`$a`

演示

```
echo "9*9" | bc
# 81

result=$(echo "1+1" | bc)
echo $result
# 2

echo "scale=3;10/3" | bc
# 3.33

echo "scale=3;10/3;last*2"| bc
# 3.333
# 6.666

# 内置函数必须使用`-l`(mathlib)标准数学库
echo "e(2)" | bc -l
# 7.38905609893065022723

echo "obase=2;7" | bc -l
# 111

a=2
echo "b=$a+2;b" | bc
# 4
```

### 3. 将bc计算结果赋值给shell变量

语法：

```
# 第一种方式
var_name="echo "expression" | bc [options]"

# 第二种方式
var_name=$(echo "expression" | bc [options])

```

> `$()` 与 `` 功能一样，都是执行里面的命令
>
> 区别：
>
> ``是所有linux系统支持的方式，兼容性较好，但是容易与引号产生混淆
>
> `$()`不是所有linux系统都支持的方式，兼容性较差，但是不容易产生混淆

演示：

```
b=$(echo "1+1" | bc)
echo $b
# 2

b=`echo "1+2" | bc`
echo $b
# 3
```

## 7.非互动式的输入重定向运算

### 1.介绍

将计算表达式输出给bc去执行，特点类似于文件中输入，可以输入多行表达式，更加清晰

### 2.语法

```
# 第一种方式
var_name=`bc [options] << EOF
第一行表达式1
第二行表达式2
...
EOF
`

# 第二种方式
var_name=$(bc [options] << EOF
第一行表达式1
第二行表达式2
...
EOF
)
```

> `var_name` 是shell变量的名字
>
> `bc` 执行bc的命令
>
> `EOF..EOF` 输入流的多行表达式 
>
> 含义：将EOF中间多行表达式输入给到bc去执行，将bc执行结果给到shell变量var_name

演示:

第一种方式
```
b=`bc -l << EOF
1+1
2+2
3+3
EOF
`

echo $b   
# 多行表达式会生成多个结果，会拼成字符串返回给b
# 2 4 6
```
第二种方式
```
b=$(bc -l << EOF
scale=2;10/3
EOF
)

echo $b
# 3.33
```


