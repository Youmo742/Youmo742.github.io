---
title: Form类及参数(2)
categories: Python
time: 2018-10-11 00:00:00
tags:
- Django Form参数
---

# Django内置字段及参数

```python
Field
	required=True		是否允许为空，默认为不能为空
    widget=None			html插件 # widget=widgets.xxx,生成何种标签
    label=None			生成label标签或现实内容 obj.tagname.label
    initial=None		初始值
    help_text=None		显示帮助信息
    label_suffix=None	label内容后缀
    validators=[]		自定义验证规则
    disabled=False		是否允许编辑
    """
    lable,initial,helo_text,lable_suffix合起来使用
    可以生成html标签
    {{obj.as_p}}
    """
```

