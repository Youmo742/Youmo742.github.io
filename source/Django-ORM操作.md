---
title: Django-ORM操作:开始使用model
categories: Python
date: 2018-10-02 00:00:00
tags:
- Django-Mysql
- Python
- Django-ORM
---

# 配置Django使用MSyql数据库

在项目的`setting.py`文件中，将默认的`default`改为：

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',  # 或者使用mysql.connector.django
        'NAME': 'test',
        'USER': 'root',
        'PASSWORD': '123456',
        'HOST':'localhost',
        'PORT':'3306',
    }
}
```

然后在项目的`init.py`文件中，添加：

```python
import pymysql

pymysql.install_as_MySQLdb()
# 将Django默认的数据库引擎换为pymysql
```

创建`app`

```python
django-admin startapp app_test
# 或者 python3 startapp app_test
```

然后，在`setting.py`中，:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_test',# 这是新建的app
]
```

在这里可能会提示`mysqlcilent`的version 不够，这时候，需要安装python的mysql支持

`pip3 insatll mysqlclient`

在保存，还会遇到一个错误，django抛出一个异常，直接找到错误的地方，把那句注释掉就可以了。

# 开始使用Django的Model模型

**ORM** 将数据库的操作变为了类和对象的关系，在Django的ORM中，一个类就是一张数据表，类的字段就是数据库的字段，封装的具体的细节，只需要以类和对象的方法去操作就好了。

## 开始创建表

在`app`的`model.py`文件中，进行类的创建，

*类只是数据表，对于数据库(模式)，Django不会自己创建，需要自己创建好数据库后，在setting中配置，然后，在app的model中，创建表*

****

```python
from django.db import models
# Create your models here.


class Test(models.Model):
    name = models.CharField(max_length=20)
    sex = models.CharField(max_length=20)
```

然后，执行

```python
python manage.py makemigrations
python manage.py migrate
```

这样，便在`test`数据库中，创建了`app_test_test`数据表，并且，这张表有三个字段，分别为：

```mysql
id int(11) auto increment #django 自动创建的主键，若自己指定，则按照指定的来
name varchar(20)
sex varchar(20)
```

## 插入数据项

* 可以直接在Mysql命令行或者banch中创建。

* 当然，使用django的方法创建。

配置好路由关系，在`views.py`中:

```python
def db_op(request):

    from app_test import models

    models.Test.objects.create(name='老王',sex="女")
    #
    models.Test.objects.create(name='老王', sex="男")
    
    return HttpResponse(name_list)
```

在浏览器中，输入相应的url，使执行db_op函数，在数据库中，就可以看到数据插入。