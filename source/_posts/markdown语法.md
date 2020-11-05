---
title: markdown语法
date: 2019-04-19 01:09:47
categories: 
- 语法
---

markdown语法学习笔记

<!-- more -->

# 标题

> \#  This is an H1
>
> \#\#  This is an H2 r

# 区块引用

> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
>
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
> Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
>
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
> id sem consectetuer libero luctus adipiscing.

 ```markdown
>  This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
>  consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
>  Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
 
>  Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
>  id sem consectetuer libero luctus adipiscing.
 ```

# 列表

> * red
> * green
> * blue

``` markdown
* red
* green
* blue
或
+ red
+ green
+ blue、
或
- red
- green
- blue
```

> 1. red
> 2. green
> 3. blue

``` markdown
1. red
2. green
3. blue
```

# 代码区块

# 分隔线

***

```` markdown
****
或
- - -
或
---------------------------
````

# 链接

## 行内式链接

这是一个[百度](https://www.baidu.com)

```markdown
这是一个[百度](https://www.baidu.com)
```

## 参考式链接

这是一个[百度][1] 

[1]: https://www.baidu.com	"百度地址"

````markdown
这是一个[百度][1]
[1]: https://www.baidu.com "百度地址"
````

# 强调

*single asterisks*

_single underscores_

**double asterisks**

__double underscores__

```markdown
*single asterisks*

_single underscores_

**double asterisks**

__double underscores__
```

# 代码

## 行内代码

use the `print()` function

```md
Use the `printf()` function.
```

## 代码区段

``There is a literal backtick (`) here.``

```
There is a literal backtick (`) here.
```

```
``There is a literal backtick (`) here.``
​```There is a literal backtick (`) here.```
```

# 图片

![Alt text](https://k.zol-img.com.cn/sjbbs/7692/a7691501_s.jpg"图片")

``` 
![Alt text](/path/to/img.jpg "Optional title")

Alt text:图片替代文字
“path/to/img.jpg”:图片地址
Optional title:图片title
```

==asd==

# 删除文本

~~此部分已被删除~~

```
~~~此部分已被删除~~~
```

# 选择框

- [x] 计划任务
- [ ] 完成任务

```
- [x] 计划任务
- [] 完成任务
```

# 表格

| 项目 | Value |
| :--: | :---- |
| 电脑 | $1600 |
| 手机 | $12   |
| 导管 | $1    |

```
项目     | Value
-------- | -----
电脑  | $1600
手机  | $12
导管  | $1
```

## 设定内容格式

使用`:---------:`居中
使用`:----------`居左
使用`----------:`居右

| 第一列         | 第二列         | 第三列         |
| -------------- | -------------- | -------------- |
| 第一列文本居中 | 第二列文本居右 | 第三列文本居左 |

```
| 第一列       | 第二列         | 第三列        |
|:-----------:| -------------:|:-------------|
| 第一列文本居中 | 第二列文本居右  | 第三列文本居左 | 
```

# 注脚

一个具有注脚的文本。[^2]

[^2]: 这是一个注脚

```
一个具有注脚的文本。[^2]

[^2]: 注脚的解释
```

# 流程图

[参考](http://adrai.github.io/flowchart.js/ )

