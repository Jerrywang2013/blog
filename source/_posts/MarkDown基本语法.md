---
title: MarkDown基本语法
date: 2024-04-23 21:25:07
tags:
---


一、前言
    由于有些语法无法在博客园展示，推荐使用[Typora解锁全套](https://www.typora.io/)
    推荐使用[jupyter](https://www.cnblogs.com/nickchen121/p/10722733.html)
    [markdown数学公式大全](https://www.cnblogs.com/nickchen121/p/11746655.html)

二、MD工具
* **离线工具**：
[typora](https://www.cnblogs.com/guoxuanhan/p/16841068.html)：markdown编辑器和阅读器，分割简单多种主题，支持windows/linux/macOS。
* **在线编辑器**：
[马克飞象](https://maxiang.io/)
[dingus](http://daringfireball.net/projects/markdown/dingus)



二、MD基本语法
* 2.1 标题
**代码**：
```
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
```
**效果**：
   # 一级标题
   ## 二级标题
   ### 三级标题
   #### 四级标题
* 2.2 加粗
语法：使用**或者__
**代码**：
```
**我被加粗了**
```
**效果**：
&nbsp;&nbsp;&nbsp;&nbsp;我被**加粗**了倾斜

* 2.3 斜体
语法：使用*或者_
**代码**：
```
*我被倾斜了*
```
**效果**：
&nbsp;&nbsp;&nbsp;&nbsp;*我被倾斜了*

* 2.4 高亮
语法：使用*或者_
        我被==高亮==
* 2.5 代码引用(>)
* 2.6 代码引用(``` \`\`\` ```)
* 2.7 代码引用(\`)
* 2.8 插入连接
Markdown支持两种链接： **_内联_** 和 **_引用_**
* 内联
**代码**：
```
    This is [an example](http://example.com/ "Here is Title") inline link
```
**效果**：
This is [an example](http://example.com/ "Here is Title") inline link

* 引用
   <>
   []()
* 2.9 插入图片链接   
* 2.11 有序列表
    1. one
    2. two
* 2.12 无序列表
    * one
    * two
* 2.13 水平分割线
**代码**:
```
---- 或者 *** 或者 * * * 或者 - - -
```
**效果**:
----

* 2.14 表格
* 2.15 数学公式（行内嵌）
* 2.16 数学公式（块状）
----

### 表格
| Item      |    Value | Qty  |
| :-------- | --------:| :--: |
| Computer  | 1600 USD |  5   |
| Phone     |   12 USD |  12  |
| Pipe      |    1 USD | 234  |

### 流程图
flow
st=>start: Start
e=>end
op=>operation: My Operation
cond=>condition: Yes or No?

st->op->cond
cond(yes)->e
cond(no)->op


以及时序图:

sequence
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: I am good thanks!