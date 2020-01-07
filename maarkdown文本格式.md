

<!-- MarkdownTOC -->

- [HashMap 简介](#hashmap-简介)
- [底层数据结构分析](#底层数据结构分析)
  - [JDK1.8之前](#jdk18之前)
    - 1. aa
    - 2. bb
  - [JDK1.8之后](#jdk18之后)
- [HashMap源码分析](#hashmap源码分析)
  - [构造方法](#构造方法)
  - [put方法](#put方法)
  - [get方法](#get方法)
  - [resize方法](#resize方法)
- [HashMap常用方法测试](#hashmap常用方法测试)

<!-- /MarkdownTOC -->

## 文本对齐方式   

```
<p align="left">居左文本</p>
<p align="center">居中文本</p>
<p align="right">居右文本</p>
```

## 如何在 GitHub 录入数学公式   

GitHub Flavored Markdown 曾经是支持 LaTeX 的，但是现在不支持了……因此若想在 README 或者评论中插入数学公式，就得使用另外的方式。

CodeCogs 提供了一个[在线 LaTeX 编辑器](https://link.jianshu.com/?t=https://www.codecogs.com/latex/eqneditor.php)，可以将输入的数学公式转换为图片，并自动生成 HTML 代码（也支持其他格式）。

比如输入下面的代码：

S = 1 / ( (1 - p) + p / n )

便可以得到：

<img src="https://latex.codecogs.com/gif.latex?S&space;=&space;\frac{1}{(1-p)&plus;\frac{p}{n}}" title="S = \frac{1}{(1-p)+\frac{p}{n}}" />   

由于 Markdown 允许嵌入 HTML，所以直接把生成的代码粘贴过去就可以了。


## 目录功能   
```
<!-- MarkdownTOC -->

- [HashMap 简介](#hashmap-简介)
- [底层数据结构分析](#底层数据结构分析)
  - [JDK1.8之前](#jdk18之前)
  - [JDK1.8之后](#jdk18之后)
- [HashMap源码分析](#hashmap源码分析)
  - [构造方法](#构造方法)
  - [put方法](#put方法)
  - [get方法](#get方法)
  - [resize方法](#resize方法)
- [HashMap常用方法测试](#hashmap常用方法测试)

<!-- /MarkdownTOC -->

> 感谢 [changfubai](https://github.com/changfubai) 对本文的改进做出的贡献！

## HashMap 简介
HashMap 主要用来存放键值对，它基于哈希表的Map接口实现</font>，是常用的Java集合之一。 

JDK1.8 之前 HashMap 由 数组+链表 组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）.JDK1.8 以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）时，将链表转化为红黑树（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树），以减少搜索时间，具体可以参考 `treeifyBin`方法。

## 底层数据结构分析
### JDK1.8之前
JDK1.8 之前 HashMap 底层是 **数组和链表** 结合在一起使用也就是 **链表散列**。**HashMap 通过 key 的 hashCode 经过扰动函数处理过后得到 hash  值，然后通过 `(n - 1) & hash` 判断当前元素存放的位置（这里的 n 指的是数组的长度），如果当前位置存在元素的话，就判断该元素与要存入的元素的 hash 值以及 key 是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。**
```

## 标题关联   
```
[第01讲 | 谈谈你对Java平台的理解？](#1)   

---
---
---
<h2 id="1">第1讲 | 谈谈你对Java平台的理解？</h2>

---

```





