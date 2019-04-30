---
title: Django-ORM之数据字段
categories: Python
date: 2018-10-03 00:00:00
tags:
- Python
- Django-ORM
---

# 常用的数据字段

```python
from django.db import models

models.AutoField()
# 自增类型　为int()型，若不指定primary_key，则django自动生成一个id列，为AutoField()

models.FloatField()
# 浮点型

models.BooleanField()
#　布尔类型，默认不能为空

models.CharField()
# 字符类型，为varchar(len)类型，必须指定　max_length

models.DateTimeField()
# datatime 类型　year-month-day hour:mintues:second
```

# 其他数据字段

```python
models.EmailField()
models.DecimalField()
models.UUIDField()
models.URLField()
# 其实，这些的存储方式，本质上都是字符串，当在admin里面注册之后，在Django的管理网站页面，才会看到效果
```

# 字段合集

```python
   AutoField(Field)
        - int自增列，必须填入参数 primary_key=True

    BigAutoField(AutoField)
        - bigint自增列，必须填入参数 primary_key=True

        注：当model中如果没有自增列，则自动会创建一个列名为id的列
        from django.db import models

        class UserInfo(models.Model):
            # 自动创建一个列名为id的且为自增的整数列
            username = models.CharField(max_length=32)

        class Group(models.Model):
            # 自定义自增列
            nid = models.AutoField(primary_key=True)
            name = models.CharField(max_length=32)

    SmallIntegerField(IntegerField):
        - 小整数 -32768 ～ 32767

    PositiveSmallIntegerField(PositiveIntegerRelDbTypeMixin, IntegerField)
        - 正小整数 0 ～ 32767
    IntegerField(Field)
        - 整数列(有符号的) -2147483648 ～ 2147483647

    PositiveIntegerField(PositiveIntegerRelDbTypeMixin, IntegerField)
        - 正整数 0 ～ 2147483647

    BigIntegerField(IntegerField):
        - 长整型(有符号的) -9223372036854775808 ～ 9223372036854775807

    BooleanField(Field)
        - 布尔值类型

    NullBooleanField(Field):
        - 可以为空的布尔值

    CharField(Field)
        - 字符类型
        - 必须提供max_length参数， max_length表示字符长度

    TextField(Field)
        - 文本类型

    EmailField(CharField)：
        - 字符串类型，Django Admin以及ModelForm中提供验证机制

    IPAddressField(Field)
        - 字符串类型，Django Admin以及ModelForm中提供验证 IPV4 机制

    GenericIPAddressField(Field)
        - 字符串类型，Django Admin以及ModelForm中提供验证 Ipv4和Ipv6
        - 参数：
            protocol，用于指定Ipv4或Ipv6， 'both',"ipv4","ipv6"
            unpack_ipv4， 如果指定为True，则输入::ffff:192.0.2.1时候，可解析为192.0.2.1，开启此功能，需要protocol="both"

    URLField(CharField)
        - 字符串类型，Django Admin以及ModelForm中提供验证 URL

    SlugField(CharField)
        - 字符串类型，Django Admin以及ModelForm中提供验证支持 字母、数字、下划线、连接符（减号）

    CommaSeparatedIntegerField(CharField)
        - 字符串类型，格式必须为逗号分割的数字

    UUIDField(Field)
        - 字符串类型，Django Admin以及ModelForm中提供对UUID格式的验证

    FilePathField(Field)
        - 字符串，Django Admin以及ModelForm中提供读取文件夹下文件的功能
        - 参数：
                path,                      文件夹路径
                match=None,                正则匹配
                recursive=False,           递归下面的文件夹
                allow_files=True,          允许文件
                allow_folders=False,       允许文件夹

    FileField(Field)
        - 字符串，路径保存在数据库，文件上传到指定目录
        - 参数：
            upload_to = ""      上传文件的保存路径
            storage = None      存储组件，默认django.core.files.storage.FileSystemStorage

    ImageField(FileField)
        - 字符串，路径保存在数据库，文件上传到指定目录
        - 参数：
            upload_to = ""      上传文件的保存路径
            storage = None      存储组件，默认django.core.files.storage.FileSystemStorage
            width_field=None,   上传图片的高度保存的数据库字段名（字符串）
            height_field=None   上传图片的宽度保存的数据库字段名（字符串）

    DateTimeField(DateField)
        - 日期+时间格式 YYYY-MM-DD HH:MM[:ss[.uuuuuu]][TZ]

    DateField(DateTimeCheckMixin, Field)
        - 日期格式      YYYY-MM-DD

    TimeField(DateTimeCheckMixin, Field)
        - 时间格式      HH:MM[:ss[.uuuuuu]]

    DurationField(Field)
        - 长整数，时间间隔，数据库中按照bigint存储，ORM中获取的值为datetime.timedelta类型

    FloatField(Field)
        - 浮点型

    DecimalField(Field)
        - 10进制小数
        - 参数：
            max_digits，小数总长度
            decimal_places，小数位长度

    BinaryField(Field)
        - 二进制类型
```



# 字段的可选参数

- **空值：Field.null**

如果为True，Django将在数据库中将空值存储为NULL。默认值是 False。

```python
field1 = models.CharField('字段1',max_length=20,null=True)
```

- **bank：Field.blank**

  如果为True，则该字段允许为空白。 默认值是 False。

```python
field2 = models。CharField('字段2',max_length=20,blank=True)
```

- **choices：Field.choices**

  它是一个可迭代的结构(比如，列表或是元组)，由可迭代的二元组组成(比如[(A, B), (A, B) …])，用来给这个字段提供选择项。如果设置了 choices ，默认表格样式就会显示选择框，而不是标准的文本框，而且这个选择框的选项就是 choices 中的元组。

```python
SIZE_TYPE = (
    ('SMALL','Small'),
    ('MIDDLE','Middle'),
    ('BIG','Big')
)
field3 = models.CharField('字段3',max_length=20,choice = SIZE_TYPE,default = 'SMALL')123456
```

- **db_column：Field.db_column**

  数据库中用来表示该字段的名称。如果未指定，那么Django将会使用Field名作为字段名.

```python
field4 = models.CharField('字段4',max_length=20,blank=True,db_column='column_field4')
```

- **db_index：Field.db_index**

若值为 True, 则 django-admin sqlindexes 将会为此字段输出 CREATE INDEX 语句。（为此字段创建索引）

```python
field5 = models.CharField('字段5',max_length=20,blank=True,db_index=True)
```

- **默认：Field.default**

  该字段的默认值. 它可以是一个值或者一个可调用对象. 如果是一个可调用对象，那么在每一次创建新对象的时候，它将会调用一次.

```python
field6 = models.CharField('字段6',max_length=20,default='')
```

- **可编辑：Field.editable**

  如果设为False, 这个字段将不会出现在 admin 或者其他 ModelForm. 他们也会跳过 模型验证. 默认是True.

```python
field7 = models.CharField('字段7',max_length=20,default='',editable=False)
```

- **help_text：Field.help_text**

  额外的 ‘help’ 文本将被显示在表单控件form中。即便你的字段没有应用到一个form里面，这样的操作对文档化也很有帮助。

```python
field8 = models.DateField('字段8',default='2017-12-12',help_text='Please use the following format:<em>YYYY-MM-DD</em>.')
```

- **primary_key（主键）：Field.primary_key**

  若为 True, 则该字段会成为模型的主键字段。

```python
field9 = models.IntegerField('字段9',primary_key=True,suto_created=True,default=1)
```

- **unique：Field.unique 如果为 True, 这个字段在表中必须有唯一值.**

```python
field10 = models.CharField('字段10',max_length=20,blank=true,unique=False)
```

# 字段间的关系

## 1. Foreign_key

```python
class Classes(models.Model):
    name = models.CharField(max_length=32)

class Student(models.Model):
    name = models.CharField(max_length=32)
    theclass = models.ForeignKey(to="Classes",on_delete=models.CASCADE)
# to 要关联的表
# to_field 要关联表的字段
# on_delete 此字段必须存在，表示，当删除数据时，想关联的数据怎么办
```

当正向查询时

```python
models.Student.objects.first().theclass_id.all()
# 这样，便查询和这个学生所有有关联的classes
```

当反向查询时

```python
model.Classes.objects.first().student_set.all()
# 查询所有和当前班级有关联的学生
```

## 2.OneToOneField

一对一字段。

通常一对一字段用来扩展已有字段

## 3.ManyToManyField

用于表示多对多的关联关系。在数据库中通过第三张表来建立关联关系。

通常来说，这个字段不会使用，都是自己创建第三张表，将其关联起来。																																														