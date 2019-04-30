---
title: XML
categories: XML
date: 2018-06-14 21:34:14
tags:
- XML学习笔记
- XML语法
- XML格式
- dtd约束
---

# 格式及语法

## 开头

`<?xml version="1.0" encoding="UTF-8">`

必须以此 格式 开头，让浏览器或者解释器知道这是一个XML文件。

## 结构

1. 有且仅有一个称为 根元素的节点 。
2. 标签的格式为`<lable>discribe</lable>`，不能以数字开头。
3. 标签可以嵌套，但有开始标签，必须有结束标签。或者直接为空标签`</lable>`

## 语法和细节

1. 特殊符号

   大多数符号可以用，但特殊的`< > " '`,这本身是XML构成的部分。

   * 双引号内部可以嵌套单引号，单引号内部也可以嵌套双引号，不可以自己嵌套自己。

   * 当必须这样做的时候，引用实体，用特殊的处理方式显示。

     | 符号    | 表示意思 |
     | ----- | ---- |
     | &lt   | <    |
     | &gt   | >    |
     | &amp  | &    |
     | &apos | '    |
     | &quot | "    |

2. **CDATA节**

   当有大量的特殊字符需要引用的时候，便需要使用CDATA节。它就是为了原样保存字符串内容的。

   格式`<![CDATA[@#$%^&BJHbdjcj]]>`。

# DTD约束

## 概述

1. DOCUMENT TYPE DEFINE,即文档格式定义，为了解决XML太过自由的问题，可以约束XML文档该出现哪些内容，哪些不能出现。

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <class>
   <stu>
   	<name>
   		<firstname>Bob</firstname>
   		<lastname>Smith</lastname>
   	</name>
   	<sex>男</sex>
   	<intro>Nice</intro>
   	<name>
   		<firstname>Jily</firstname>
   		<lastname>John</lastname>
   	</name>
   	<sex>女</sex>
   	<!--<Square>100<Square>-->
   </stu>
   </class>
   ```

   自然，在学生中，是不能出现Square元素的，但是，写了，没有什么东西可以让它不使用。

## DTD文件

```xml
<!ELEMENT class (stu+)>
<!--class为根元素，（stu+）表示，至少有一个元素-->
<!ELEMENT stu(name,sex,intro)>
<!ELEMENT name(#PCDATA)>
<!--#PCDATA 表示可以使用任何文本，但不能包含任何子元素，（比如name）
    #EMPTY 表示不能有任何内容
    #ANY 可以包含任何在DTD中已经定义过的元素（比如 sex）
-->
<!ELEMENT sex(#PCDATA)>
<!ELEMENT intro(#PCDATA)>
```

dtd文件中，`,`表示顺序，如果使用顺序出错，不予以通过。

`（）`用来给元素分组。

`+`元素必须出现，至少一次

`*`可以不出现

* 元素属性及其约束

  | 属性值       | 属性的选项       |
  | --------- | ----------- |
  | #REQURIED | 必须给属性设置     |
  | #IMPLIED  | 属性的值是可选     |
  | #FIXED    | 属性的值是固定的一个值 |

| 属性类型       | 值                      |
| ---------- | ---------------------- |
| CDATA      | 任意的字符串                 |
| ID         | 唯一的一个标识符，不能重复，不i能以数字开头 |
| (att\|att) | 值为枚举类型，事先定义好的一些        |
| ENTITY     | 实体，定义好实体，在 这里引用即可      |

* 元素属性的约束最好放在对应元素的下面。

* 语法：

  * `<ATTLIST 元素 属性 属性要求 属性特点>`


  * ```xml
    <!ELEMENT NEWSPAPER (ARTICLE+)>
    <!ELEMENT ARTICLE (HEADLINE,BYLINE,LEAD,BODY,NOTES)>
    <!ELEMENT HEADLINE (#PCDATA)>
    <!ELEMENT BYLINE (#PCDATA)>
    <!ELEMENT LEAD (#PCDATA)>
    <!ELEMENT BODY (#PCDATA)>
    <!ELEMENT NOTES (#PCDATA)> 

    <!ATTLIST ARTICLE AUTHOR CDATA #REQUIRED>
    <!ATTLIST ARTICLE EDITOR CDATA #IMPLIED>
    <!ATTLIST ARTICLE DATE CDATA #IMPLIED>
    <!ATTLIST ARTICLE EDITION CDATA #IMPLIED>

    <!ENTITY NEWSPAPER "Vervet Logic Times"><!--定义实体-->
    <!ENTITY PUBLISHER "Vervet Logic Press">
    <!ENTITY COPYRIGHT "Copyright 1998 Vervet Logic Press">
    ```

保存为*.dtd

## 在XML中引用DTD

在XML中使用DTD的方式有两种，一种为内部DTD，一种为外部DTD。

1. 内部DTD就是直接在XML文档中定义DTD内容。

2. 外部DTD

   - 引入本地DTD

     ```xml
     <!DCOTYPE 根元素 SYSTEM "本地DTD地址">
     ```

   - 引入公共DTD，别人写好的

     `<!DOCTYPE 根元素 PUBLIC "URL">`

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!--dtd约束文件-->
   <!DOCTYPE class SYSTEM "Mysecond.dtd">
   <class>
   <stu>
   	<name>
   		<firstname>Bob</firstname>
   		<lastname>Smith</lastname>
   	</name>
   	<sex>男</sex>
   	<intro>Nice</intro>
   	<name>
   		<firstname>Jily</firstname>
   		<lastname>John</lastname>
   	</name>
   	<sex>女</sex>
   	<!--<Square>100<Square>-->
   </stu>
   </class>
   ```






