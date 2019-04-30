---
title: Form类使用及参数(1)
categories: Python
time: 2018-10-10 00:00:00
tags:
- Django Form组件
---

# Form组件的基本使用

* 创建验证规则

```python
from django.forms import Form,fields


class LoginForm(Form):
    username=fields.CharField(min_length=6,max_length=18,required=True,
                              error_messages={
                                  'min_length':"至少6个字符"
                              })
    password=fields.CharField(min_length=6,max_length=18,required=True,
                              error_messages={
                                  'min_length': "至少6个字符"
                              })
"""
自建类，继承自Form类,定义规则    
"""
```

* 使用验证规则(1):使用Form 表单提交验证
```python
def login(request):
    obj=LoginForm(request.POST)
        if obj.is_valid():
            # cleaned_data 只是正确的信息
            # print(obj.cleaned_data)
            return HttpResponse("404")
        else:
            return render(request,'login.html',{'error_msg':obj.errors})
```

* 使用验证规则(2): 使用ajax方式提交
```python
"""
后台验证和Form提交的方式相同，不同的是，ajax提交，会保留页面输入的内容
js代码的结果处理需要特殊对待
"""
def ajaxlogin(request):
    result={'status':True,'msg':None}
    obj = LoginForm(request.POST)
    if obj.is_valid():
        pass
    else:
        result['status']=False
        result['msg']=obj.errors
    import json
    result=json.dumps(result)
    return HttpResponse(result)
"""
总体的思想是设置一个状态量，如果验证通过，状态设置为true,否则，设置为false,返回给js
"""
```

```javascript
# 需要引入jquery库
<script language="JavaScript">
    function submitForm() {
        $('.error1').remove();
        $.ajax(
            {
                url:'/ajaxlogin/',
                type:'POST',
                data:$('#loginF1').serialize(),
                dataType:"JSON",
                success:function (args) {
                    if(args.status)
                    {

                    }else {
                        $.each(args.msg,function (k,v) {
                            var tag=document.createElement('span');
                            tag.innerHTML=v[0];
                            tag.className='error1';
                            $('#loginF1').find('input[name="'+k+'"]').after(tag)
                        })
                    }
                }
            }
        )
    }
</script>
```

# Django Form类的常用字段

```python
# 1.CharField
# 最常用的字段,毕竟大多数需要用户输入的都是字符串
"""
class CharField(Field):
    def __init__(self, *, max_length=None, min_length=None, strip=True, empty_value='', **kwargs):
"""
username=fields.CharField(required=True)

# 2.IntegerFields
"""
class IntegerField(Field):
    widget = NumberInput
    default_error_messages = {
        'invalid': _('Enter a whole number.'),
    }
    re_decimal = re.compile(r'\.0*\s*$')

    def __init__(self, *, max_value=None, min_value=None, **kwargs):
"""
num=fields.IntegerFiels(required=True)

# 3.DataTimeField
# 时间日期格式，输入出生日期等
"""
class DateTimeField(BaseTemporalField):
    widget = DateTimeInput
    input_formats = formats.get_format_lazy('DATETIME_INPUT_FORMATS')
    default_error_messages = {
        'invalid': _('Enter a valid date/time.'),
    }
"""
```

