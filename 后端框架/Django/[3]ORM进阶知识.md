经过上周的django学习与项目实战，想必对其也有了足够的了解。
[django基础知识](https://blog.csdn.net/QAZJOU/article/details/125989130?spm=1001.2014.3001.5501)
[项目实战](https://blog.csdn.net/QAZJOU/article/details/126100420?spm=1001.2014.3001.5501)

本次django进阶知识重要 **围绕数据库管理操作为主** ，同时讲述了部分关于组件中间件的内容，希望有所帮助。

其实在经过学习django后，学习java servlet ，springboot/cloud等会比较快的，接下来同样巩固django,学习之余以java为主要向导，层层深入！

### 目录

* [admin管理工具](https://editor.csdn.net/md/?articleId=126227188#admin_8)

  * [使用管理工具](https://editor.csdn.net/md/?articleId=126227188#_10)
  * [复杂模型](https://editor.csdn.net/md/?articleId=126227188#_42)
  * [自定义表单](https://editor.csdn.net/md/?articleId=126227188#_71)
* [ORM单表实例](https://editor.csdn.net/md/?articleId=126227188#ORM_80)
* [数据库添加：](https://editor.csdn.net/md/?articleId=126227188#_111)
* [查找](https://editor.csdn.net/md/?articleId=126227188#_139)
* [删除](https://editor.csdn.net/md/?articleId=126227188#_170)
* [修改](https://editor.csdn.net/md/?articleId=126227188#_178)
* [ORM多表实例](https://editor.csdn.net/md/?articleId=126227188#ORM_192)
* [插入数据](https://editor.csdn.net/md/?articleId=126227188#_233)
* [关联管理器（对象调用）](https://editor.csdn.net/md/?articleId=126227188#_270)
* [ORM查询](https://editor.csdn.net/md/?articleId=126227188#ORM_297)
* [ORM 多表实例](https://editor.csdn.net/md/?articleId=126227188#ORM__313)
* [聚合查询](https://editor.csdn.net/md/?articleId=126227188#_314)
* [分组查询](https://editor.csdn.net/md/?articleId=126227188#_324)
* [F()查询](https://editor.csdn.net/md/?articleId=126227188#F_375)
* [Q()查询](https://editor.csdn.net/md/?articleId=126227188#Q_388)
* [Django Form组件](https://editor.csdn.net/md/?articleId=126227188#Django_Form_402)
* [用户认证（Auth)](https://editor.csdn.net/md/?articleId=126227188#Auth_513)
* [cookies与session](https://editor.csdn.net/md/?articleId=126227188#cookiessession_541)
* [cookies](https://editor.csdn.net/md/?articleId=126227188#cookies_544)
* [session](https://editor.csdn.net/md/?articleId=126227188#session_686)
* [django中间件](https://editor.csdn.net/md/?articleId=126227188#django_690)
* [自定义中间件](https://editor.csdn.net/md/?articleId=126227188#_704)
* [视图（FBV与CBV)](https://editor.csdn.net/md/?articleId=126227188#FBVCBV_748)

# admin管理工具

一般来说，admin是管理数据库信息。

## 使用管理工具

django提供了基于web的管理工具，django.contrib是其中一部分，可以在Installed_apps看见。
通常在urls.py中自动设置好了
/helloworld/views.py

```python
	url(r'^admin/',admin.site.urls),
```

然后在访问localhost:8000/admin/即可。
提醒，前面的数据库建表要使用命令建表，否则会出现异常情况。
可以通过命令

```python
$ python manage.py createsuperuser
username:
email:
password:
...
superuser created successfully
```

为了让admin管理某个数据模型，需要注册该数据模型到admin.(以Test为例）

```python
# /TestModel/admin.py
from django.contrib import admin

from TestModel.models import  Test
# Register your models here.

admin.site.register(Test)
```

## 复杂模型

管理页面功能强大，完全有能力处理更加复杂的数据模型。
现在TestModel/models.py增加一个更加复杂的数据模型：

```python
class Contact(models.Model):
    name = models.CharField(max_length=200)
    age = models.IntegerField(default=0)
    email = models.EmailField()
    def __unicode__(self):
        return self.name

class Tag(models.Model):
    contact = models.ForeignKey(Contact,on_delete=models.CASCADE,)
    name = models.CharField(max_length=50)
    def __unicode__(self):
        return self.name
```

这里多了2个表，Tag以Contact为外键，在admin.py注册多个模型

```python
admin.site.register([Test,Contact,Tag])
```

在此之前需要将表的内容写进数据库

```python
$ python manage.py makemigrations TestModel  # 让 Django 知道我们在我们的模型有一些变更
$ python manage.py migrate TestModel   # 创建表结构
```

## 自定义表单

自定义管理页面来取代默认的页面，比如在add页面只想显示name和email,修改admin.py

```python
class ContactAdmin(admin.ModelAdmin):
    fields = ('name', 'email')
 
admin.site.register(Contact, ContactAdmin)
admin.site.register([Test, Tag])
```

# ORM单表实例

先创一个project

```python
django-admin startproject app01
# 记得在Installed_app 加入app01

# __init__.py:
import pymysql
pymysql.install_as_MySQLdb()

```

创建模型

```python
# app01/models.py
class Book(models.Model): 
    id = models.AutoField(primary_key=True) # id 会自动创建,可以手动写入
    title = models.CharField(max_length=32) # 书籍名称
    price = models.DecimalField(max_digits=5, decimal_places=2) # 书籍价格 
    publish = models.CharField(max_length=32) # 出版社名称 
    pub_date = models.DateField() # 出版时间
```

使用命令执行命令

```python
$ python3 manage.py migrate   # 创建表结构

$ python3 manage.py makemigrations app01  # 让 Django 知道我们在我们的模型有一些变更
$ python3 manage.py migrate app01   # 创建表结构
```

## 数据库添加：

规则配置：

```python
# app01/urls.py
from django.contrib import admin
from django.urls import path
from . import views
 
urlpatterns = [
    path('add_book/', views.add_book),
]
```

使用**模型实例化对象**

```python
# app01/views.py
def add_book(request):
    book = models.Book(title="菜鸟教程",price=300,publish="菜鸟出版社",pub_date="2008-8-8") 
    book.save()
    return HttpResponse("<p>数据添加成功！</p>")
```

使用ORM提供的objects.create() (推荐）

```python
	books = models.Book.objects.create(title="如来神掌",price=200,publish="功夫出版社",pub_date="2010-10-10")

```

## 查找

使用all()来查询所有内容，返回的QuerySet()类型，里面放的是模型类的对象，可以用索引下标取出模型类的对象。

```python
def search_book(request):
	books = models.Book.objects.all()
	for i in books:
		print (i.title)
```

filter() 方法用于查询符合条件的数据，pk表示primary key =3相当于id =3 （不用id因为有内置函数）

```python
	books = models.Book.obeject.filter(pk=5)
	# 查询价格为200或者300的数据 
	：filter 中运算符号只能使用等于号 = ，不能使用大于号 > ，小于号 < ，等等其他符号。
__in 用于读取区间，= 号后面为列表 。__gt大于。__lt小于，__range 范围。 __contains包含，
__startwith
    books = models.Book.objects.filter(price__in=[200,300])
```

exclude 方法用于查询不符合条件的数据
get()方法用于查询符合条件的返回模型类的对象只能有一个。如果没有或者超过会有异常。
order_by() 对查询结果进行排序。

```python
	books= models.Book.objects.order_by("price")  # 升序排列 "-price" 降序排列
```

count() 查询所有数据的数量
first() 返回第一条数据
exist() 判断查询的结果集是否含有数据

## 删除

使用object,返回的是受影响的行数

```python
	books=models.Book.objects.filter(pk=8).first().delete()
```

注意，删除一个对象的时候会删除与它相关的外键对象

## 修改

使用对象.属性 = 更改的属性值，对象.save()

```python
	books = models.Book.objects.filter(pk=7).first()
	books.price= 400
	books.save()
```

或者使用update () 推荐

```python
	books= models.Book.objects.filter(pk__in=[7,8]).update(price=888)
```

# ORM多表实例

表和表之间的关系可以分为：一对一，一对多，多对多。
创建模型

```python
# models.py
from django.db import  models

class Book(models.Model):
    title = models.CharField(max_length=32)
    price = models.DecimalField(max_digits=5, decimal_places=2)
    pub_date = models.DateField()
    publish = models.ForeignKey("Publish", on_delete=models.CASCADE)
    authors = models.ManyToManyField("Author")


class Publish(models.Model):
    name = models.CharField(max_length=32)
    city = models.CharField(max_length=64)
    email = models.EmailField()


class Author(models.Model):
    name = models.CharField(max_length=32)
    age = models.SmallIntegerField()
    au_detail = models.OneToOneField("AuthorDetail", on_delete=models.CASCADE)


class AuthorDetail(models.Model):
    gender_choices = (
        (0, "女"),
        (1, "男"),
        (2, "保密"),
    )
    gender = models.SmallIntegerField(choices=gender_choices)
    tel = models.CharField(max_length=32)
    addr = models.CharField(max_length=64)
    birthday = models.DateField()
```

通过上述模型可以知道一共创建了4个表。
其中外键在一对多的多中设置：models.ForeignKey(“关联类名”,on_delete=models.CASCADE)

## 插入数据

mysql添加数据

```python
insert into app01_publisher(name,city,email) values ("1","2","3")
```

ORM添加数据
一对多（外键），获取对象，给属性传出对象

```python
	pub_obj = models.Pulish.objects.filter(pk=1).first()
	book= models.Book.object.create(title="1"....)
```

获取对象的id,传出对象的id

```python
	pub_obj = models.Pulish.objects.filter(pk=1).first()
	pk=pub_obj.pk
	book=models.objects.create("",publish_id=pk)
```

多对多（manytomanyfield):在第三张表新增数据
获取作者对象，获取书籍对象，给属性add方法

```python
	# 获取作者对象
	chong= models.Author.objects.filter(name="123").first()
	ying= models.Author.objects.filter(name="345").first()
	# 获取书籍对象
	book =models.Book.objects.filter(title="1234").first()
	book.authors.add(chong,ying)
```

传对象id形式

```python
	chong= models.Author.objects.filter(name="123").first()
	pk=chong.pk
	book= models.Book.objects.filter(titile="12").first()
	book.authors.add(pk)
```

## 关联管理器（对象调用）

前提：多对多(双向），一对多（只能反向）
格式：

```python
正向：属性名
反向：小写类名加——set
```

add() 用于多对多，把指定模型对象添加到关联对象集，若是在一对多，只能传对象，不能传id

```python
# 传对象
book_obj= models.Book.objects.get(id=10)
author_list=models.Author.objects.filter(id__gt=2)
book_obj.author.add(*author_list) # 将id大于2的作者添加作者集合

#传对象id
book_obj.authors.add(*[1,3]) # 将id=1,id=3对象添加到集合
```

反向：小写表名_set

```python
ying= models.Author.objects.filter(name="123").first()
book= models.Book.objects.filter(title="456").first()
ying.book_set.add(book)
```

## ORM查询

查询主键为10书籍的出版社的城市（正向）

```python
book=models.Book.objects.filter(pk=10).first()
res=book.publish.city

```

查找123出版社的书籍名

```python
pub=models.Publish.objects.filter(name="123").first()
res = pub.book_set.all()
for i in res:
	print(i.title)
```

# ORM 多表实例

## 聚合查询

聚合查询是对一组值执行计算并返回单个值
aggregate()是一个终止子句，生成的一个汇总值，相当于count
aggregate(别名 = 聚合函数名("属性名称“))

```python
from django.db.models import Avg,Max,Min,Count,Sum # 引入函数
res = models.Book.objects.aggregate(Avg("price"))
print(res,type(res))                                                                                                                                                                                         
```

## 分组查询

一般都要用到聚合函数。annotate
准备模型

```python
# models.py
class Emp(models.Model): 
    name = models.CharField(max_length=32) 
    age = models.IntegerField()
    salary = models.DecimalField(max_digits=8, decimal_places=2)
    dep = models.CharField(max_length=32) 
    province = models.CharField(max_length=32)

class Emps(models.Model): 
    name = models.CharField(max_length=32) 
    age = models.IntegerField() 
    salary =     models.DecimalField(max_digits=8, decimal_places=2) 
    dep = models.ForeignKey("Dep", on_delete=models.CASCADE)
    province = models.CharField(max_length=32) 
class Dep(models.Model): 
    title = models.CharField(max_length=32) 
  
```

> 统计每一个出版社最便宜的书的价格

```python
res= models.Publish.objects.values("name").annotate(in_price=Min("book_price")
print(res)
```

统计每一本书的作者个数

```python
res= models.Book.objects.annotate(c=Count("authors__name")).values("title","c)
print(res)
```

统计以菜开头的书的作者个数

```python
res= models.Book.objects.filter(title__startwith="菜").annotate(c=Count("authors_name")).values("title","c")
```

根据一本图书作者数量的多少对查询集进行降序

```python
res=models.Book.objects.annotate(c=Count("authors_name")).order_by("-c").values("title","c")
```

查询各个作者的书的总价格

```python
res= models.Author.objects.annotate(all=Sum("book__price")).values("name","all")
print(res)
```

## F()查询

F()的实例在查询中引用字段，来比较同一个model实例不同字段的值。
想要对**两个字段的值**做比较就得用F()

```python
from django.db.models import F
# 用法： F("字段名称")

```

查询工资大于年龄的人：

```python
book=models.Emp.objects.filter(salary__gt=F("age")).value("name","age")
```

## Q()查询

```python
from django.db.models import Q
# Q(条件判断）
Q(title_startswith="菜“）
```

之前构造的过滤器多个条件关系书and,如果执行复杂的查询（or),就可以使用Q
查询价格大于350或者名称以菜开头的书籍的名称和价格

```python
res=models.Book.objects.filter(Q(price__gt=350)|Q(title__startwith="菜")).values("title","price")
```

# Django Form组件

对页面进行初始化，生成html标签，也可以对提交的数据进行检验。

```python
# app01/My_forms.py
from django import forms
from django.core.exceptions import ValidationError
from app01 import models

class EmpForm(forms.Form):
    name = forms.CharField(min_length=4, label="姓名", error_messages={"min_length": "你太短了", "required": "该字段不能为空!"})
    age = forms.IntegerField(label="年龄")
    salary = forms.DecimalField(label="工资")
```

app01/views.py

```python
from django.shortcuts import render, HttpResponse
from app01.My_Forms import EmpForm
from app01 import models
from django.core.exceptions import ValidationError
# Create your views here.


def add_emp(request):
    if request.method == "GET":
        form = EmpForm()
        return render(request, "add_emp.html", {"form": form})
    else:
        form = EmpForm(request.POST)
        if form.is_valid():  # 进行数据校验
            # 校验成功
            data = form.cleaned_data  # 校验成功的值，会放在cleaned_data里。
            data.pop('r_salary')
            print(data)

            models.Emp.objects.create(**data)
            return HttpResponse(
                'ok'
            )
            # return render(request, "add_emp.html", {"form": form})
        else:
            print(form.errors)    # 打印错误信息
            clean_errors = form.errors.get("__all__")
            print(222, clean_errors)
        return render(request, "add_emp.html", {"form": form, "clean_errors": clean_errors})
```

add_emp.html

```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
 
<h3>添加员工</h3>
 
{#1、自己手动写HTML页面#}
<form action="" method="post">
    <p>姓名：<input type="text" name="name"></p>
    <p>年龄：<input type="text" name="age"></p>
    <p>工资：<input type="text" name="salary"></p>
    <input type="submit">
</form>
 
{#2、通过form对象的as_p方法实现#}
{#<form action="" method="post" novalidate>#}
{#    {% csrf_token %}#}
{#    {{ form.as_p }}#}
{#    <input type="submit">#}
{#</form>#}
 
{#3、手动获取form对象的字段#}
{#<form action="" method="post" novalidate>#}
{#    {% csrf_token %}#}
{#    <div>#}
{#        <label for="id_{{ form.name.name }}">姓名</label>#}
{#        {{ form.name }} <span>{{ form.name.errors.0 }}</span>#}
{#    </div>#}
{#    <div>#}
{#        <label for="id_{{ form.age.name }}">年龄</label>#}
{#        {{ form.age }} <span>{{ form.age.errors.0 }}</span>#}
{#    </div>#}
{#    <div>#}
{#        <label for="id_salary">工资</label>#}
{#        {{ form.salary }} <span>{{ form.salary.errors.0 }}</span>#}
{#    </div>#}
{#    <input type="submit">#}
{#</form>#}
 
 
{#4、用for循环展示所有字段#}
{#<form action="" method="post" novalidate>#}
{#    {% csrf_token %}#}
{#    {% for field in form %}#}
{#        <div>#}
{#            <label for="id_{{ field.name }}">{{ field.label }}</label>#}
{#            {{ field }} <span>{{ field.errors.0 }}</span>#}
{#        </div>#}
{#    {% endfor %}#}
{#    <input type="submit">#}
{#</form>#}
 
</body>
</html>
```

# 用户认证（Auth)

创建用户对象的三种方法：
create()：创建一个普通用户，密码是明文的。
create_user()：创建一个普通用户，密码是密文的。
create_superuser()：创建一个超级用户，密码是密文的，要多传一个邮箱 email 参数。

```python

from django.contrib import auth # 认证模块
from django.contrib.auth.models import User # 对应数据库
User.objects.create_user(username='runbooo',password='123')
```

验证用户的用户名和密码使用 authenticate() 方法，从需要 auth_user 表中过滤出用户对象。
使用前要导入：

```python
from django.contrib import auth # 认证模块
def login(request):
    if request.method == "GET":
        return render(request, "login.html")
    username = request.POST.get("username")
    password = request.POST.get("pwd")
    valid_num = request.POST.get("valid_num")
    keep_str = request.session.get("keep_str")
    if keep_str.upper() == valid_num.upper():
        user_obj = auth.authenticate(username=username, password=password)
        print(user_obj.username)
```

# cookies与session

cookies是存储在客户端计算机上的文本文件，保留了各种跟踪信息。识别返回用户包含以下步骤：1.服务器脚本向浏览器发送一组cookies 2.浏览器将信息存储在本地计算机 3.当下一次发送请求的时候，浏览器会把cookies发送给服务器，服务器用来识别用户。
http是无状态协议，服务器会自动不保留任何记录，但仍有三种方式维持会话

## cookies

一个服务器分配一个唯一的session id作为cookie.在开发中，使用session来完成会话跟踪，session底层依赖cookie技术

```python
# 设置cookie
rep.set_cookir(key,value)
rep.set_signed_cookie(key,value,salt)

# 获取cookie
request.COOKIES.get(key)

#删除cookie
rep=HttpResponse|| render || redirect
rep.delete_cookie(key)
```

models.py

```python
class UserInfo(models.Model):
    username = models.CharField(max_length=32)
    password = models.CharField(max_length=64)
```

urls.py

```python
from django.contrib import admin
from django.urls import path
from cookie import views
urlpatterns = [
    path('admin/', admin.site.urls),
    path('login/', views.login),
    path('index/', views.index),
    path('logout/', views.logout),
    path('order/', views.order)
```

views.py

```python
def login(request):
    if request.method == "GET":
        return render(request, "login.html")
    username = request.POST.get("username")
    password = request.POST.get("pwd")

    user_obj = models.UserInfo.objects.filter(username=username, password=password).first()
    print(user_obj.username)

    if not user_obj:
        return redirect("/login/")
    else:
        rep = redirect("/index/")
        rep.set_cookie("is_login", True)
        return rep
      
def index(request):
    print(request.COOKIES.get('is_login'))
    status = request.COOKIES.get('is_login') # 收到浏览器的再次请求,判断浏览器携带的cookie是不是登录成功的时候响应的 cookie
    if not status:
        return redirect('/login/')
    return render(request, "index.html")


def logout(request):
    rep = redirect('/login/')
    rep.delete_cookie("is_login")
    return rep # 点击注销后执行,删除cookie,不再保存用户状态，并弹到登录页面
  
def order(request):
    print(request.COOKIES.get('is_login'))
    status = request.COOKIES.get('is_login')
    if not status:
        return redirect('/login/')
    return render(request, "order.html")
```

login.html

```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
 
<h3>用户登录</h3>
<form action="" method="post">
    {% csrf_token %}
    <p>用户名： <input type="text" name="username"></p>
    <p>密码： <input type="password" name="pwd"></p>
    <input type="submit">
</form>
 
 
</body>
</html>
```

index.html

```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
 
 
<h2>index 页面。。。</h2>
 
 
<a href="/logout/">注销</a>
 
</body>
</html>

```

order.html

```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
 
 
<h2>order 页面。。。</h2>
 
 
<a href="/logout/">注销</a>
 
</body>
</html>
```

## session

session:保存在服务端的键值对。
原理：1.第一次请求获取登录页面login 2.输入正确，服务器响应浏览器index页面和一个键为sessionid,值为cookie,即set_cookie 3.服务器内部在django.session表中记录一条数据（key,data,date) 4.第三次请求资源时，携带cookie，服务器从表中根据随机字符串取出数据，使用。

# django中间件

中间件是修改request或者response对象的钩子。浏览器从请求到响应的过程中，django需要通过很多中间件来处理。其作用为：修改请求，修改响应。一般配置在settings.py /MIDDLEWARE列表中。

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

## 自定义中间件

自定义中间件，分别是：

```python
process_request(self,request)
process_view(self,request,view_func,view_args,view_kwargs)
process_exception(self,request,exception)
process_response(self,request,response)
```

app01/middlewares.py

```python
from django.utils.deprecation import MiddlewareMixin

class MD1(MiddlewareMixin):
	pass
```

settings.py /MIDDLEWARE:

```python
	'app01.middlewares.MD1'
```

自定义中间件类的方法有process_request,process_response.
middlewares.py

```python
class MD1(MiddlewareMixin):
    def process_request(self, request):
       print("md1  process_request 方法。", id(request)) #在视图之前执行
```

process_request 方法，返回值可以是None也可以是HttpResponse对象。如果是None，正常流程走，交给下一个中间件处理；如果是对象，将不执行后续视图函数，直接以其为起点，倒序执行中间件。

```python
class MD1(MiddlewareMixin):
    def process_request(self, request):
        print("md1  process_request 方法。", id(request)) #在视图之前执行


    def process_response(self,request, response): :#基于请求响应
        print("md1  process_response 方法！", id(request)) #在视图之后
        return response
```

# 视图（FBV与CBV)

FBV：基于函数的视图，在视图里使用函数处理请求
CBV: 基于类的视图，就是在视图里使用类处理请求。
前面都是使用FBV方法来处理请求的。
而基于类的视图

```python
from django.views import View
```

urls.py

```python
urlpatterns = [
	path("login/",views.Login.as_view()),
]
```

url是将一个请求分配给可调用的函数，而不是一个类。通过父类View的静态方法as_view().as_view基于类的外部接口，返回一个视图函数，调用后请求会传递给dispatch()。

```python
# views.py
from django.shortcuts import render,HttpResponse
from django.views import View

class Login(View):
    def get(self,request):
        return HttpResponse("GET 方法")

    def post(self,request):
        user = request.POST.get("user")
        pwd = request.POST.get("pwd")
        if user == "runoob" and pwd == "123456":
            return HttpResponse("POST 方法")
        else:
            return HttpResponse("POST 方法 1")
```
