---
title: Markdown语法
date: 2023-02-23 16:49:35
categories: 博客
tags: ["Markdown"]
---

Markdown语法的记录，方便以后使用。
<!-- more -->

## 标题

```markdown
# h1   //一级标题 对应 <h1> </h1>
## h2   //二级标题 对应 <h2> </h2>
### h3  //三级标题 对应 <h3> </h3>
#### h4  //四级标题 对应 <h4> </h4>
##### h5  //五级标题 对应 <h5> </h5>
###### h6  //六级标题 对应 <h6> </h6>
```

## 高亮

Markdown提供了一个特殊符号 > 用于段首进行强调，被强调的文字部分将会高亮显示
> 这段文字将会被高亮显示

## 插入链接或图片

```markdown
[点击跳转至Google](https://www.google.com)
![图片](https://****)

修改尺寸和居中显示
<div align=center><img src="*****" alt="描述" width="500"></div>
```

## 列表

```markdown
无序列表使用 * 或 + 或 - 标识
有序列表使用数字加 . 标识，例如：1.
注意：标识和文字之间要有一个空格

- 无序列表1
- 无序列表2
- 无序列表3

1. 有序列表1
2. 有序列表2
3. 有序列表3
```

## 分隔线

有时候，为了排版漂亮，可能会加入分隔线。

```markdown
---
```

效果如下

---

## 内容强调

### 加粗&斜体

```markdown
*这里是斜体*
_这里是斜体_

**这里是加粗**
__这里是加粗__

***这里是加粗并斜体***
___这里是加粗并斜体___
```

### 删除线

```markdown
这样来~~删除一段文本~~
```

### 高亮显示

```markdown
使用<code>突出背景色</code>来强调字符
比如`突出背景色`来显示强调效果
```

### 文字居中和大小颜色调整

```markdown
<font size="5" color="red" face="黑体">
{% cq %}
  人生乃是一面镜子，  
  从镜子里认识自己，  
  我要称之为头等大事，  
  也只是我们追求的目的！
{% endcq %}
</font>
```

## 代码块

代码块语法遵循标准 markdown 代码，使用\`\`\`开始 ，\`\`\`结束。

## 插入表格

```markdown
列1   | 列2 | 列3 
----- | --- | ---- 
第1行 | 12  | 13  
第2行 | 22  | 23  
第3行 | 32  | 33

| 左对齐    |  右对齐 | 居中 |
| :-------- | -------:| :--: |
| Computer  | 5000 元 |  1台 |
| Phone     | 1999 元 |  1部 |
```

## 标签

### note标签

通过 note 标签可以为段落添加背景色，语法如下：

```markdown
{% note class %}
文本内容 (支持行内标签)
{% endnote %}

也可以用下面的方式来标记
<div class="note default"><p>default</p></div>
```

支持的 class 种类包括 `default` `primary` `success` `info` `warning` `danger`。
效果如下：
{% note default %}
文本内容 (支持行内标签)
{% endnote %}
<div class="note success"><p>文本内容 (支持行内标签)</p></div>

### lable标签

通过 label 标签可以为文字添加背景色，语法如下：

```markdown
{% label [class]@text  %}

使用示例：
I heard the echo, {% label default@from the valleys and the heart %}
Open to the lonely soul of {% label info@sickle harvesting %}
Repeat outrightly, but also repeat the well-being of
Eventually {% label warning@swaying in the desert oasis %}
{% label success@I believe %} I am
{% label primary@Born as the bright summer flowers %}
Do not withered undefeated fiery demon rule
Heart rate and breathing to bear {% label danger@the load of the cumbersome %}
Bored
```

支持的 class 种类包括 `default` `primary` `success` `info` `warning` `danger`。
效果如下：  
I heard the echo, {% label default@from the valleys and the heart %}
Open to the lonely soul of {% label info@sickle harvesting %}
Repeat outrightly, but also repeat the well-being of
Eventually {% label warning@swaying in the desert oasis %}
{% label success@I believe %} I am
{% label primary@Born as the bright summer flowers %}
Do not withered undefeated fiery demon rule
Heart rate and breathing to bear {% label danger@the load of the cumbersome %}
Bored

### button按钮

通过 button 标签可以快速添加带有主题样式的按钮，语法如下：

```markdown
{% button /path/to/url/, text, icon [class], title %}
```

使用示例如下：

```markdown
{% btn #, 文本 %}
{% btn #, 文本 & 标题,, 标题 %}
{% btn #, 文本 & 图标, home %}
{% btn #, 文本 & 大图标 (固定宽度), home fa-fw fa-lg %}
```

展示效果如下：  
{% btn #, 文本 %}
{% btn #, 文本 & 标题,, 标题 %}
{% btn #, 文本 & 图标, home %}
{% btn #, 文本 & 大图标 (固定宽度), home fa-fw fa-lg %}

### tab 标签

tab 标签用于快速创建 tab 选项卡，语法如下

```markdown
{% tabs [Unique name], [index] %}
  <!-- tab [Tab caption]@[icon] -->
  标签页内容（支持行内标签）
  <!-- endtab -->
{% endtabs %}
```

其中，各参数意义如下：

* Unique name: 全局唯一的 Tab 名称，将作为各个标签页的 id 属性前缀
* index: 当前激活的标签页索引，如果未定义则默认选中显示第一个标签页，如果设为 - 1 则默认隐藏所有标签页
* Tab caption: 当前标签页的标题，如果不指定则会以 Unique name 加上索引作为标题
* icon: 在标签页标题中添加 Font awesome 图标

使用示例如下：

```markdown
{% tabs Tab标签列表 %}
  <!-- tab 标签页1 -->
    标签页1文本内容
  <!-- endtab -->
  <!-- tab 标签页2 -->
    标签页2文本内容
  <!-- endtab -->
  <!-- tab 标签页3 -->
    标签页3文本内容
  <!-- endtab -->
{% endtabs %}
```

展示效果如下：

{% tabs Tab标签列表 %}
  <!-- tab 标签页1 -->
    标签页1文本内容
  <!-- endtab -->
  <!-- tab 标签页2 -->
    标签页2文本内容
  <!-- endtab -->
  <!-- tab 标签页3 -->
    标签页3文本内容
  <!-- endtab -->
{% endtabs %}
