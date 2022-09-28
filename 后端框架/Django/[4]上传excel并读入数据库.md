在web开发中，难免会遇到**批量导入信息**的操作。一般来说，给一个excel模版然后自己在本地填写完成后提交，并上传到对应的数据库中。在此之前，我也看了不少人的代码，但是运行起来都或多或少存在问题，所以索性写了这篇博客，以供参考和学习。

当然在上传到数据库中后需要对表的内容进行对比，然后筛选出相应的内容，这将是我后面需要写的内容了，这一部分其实没什么好说的了，就是简单的读，可以使用多种方法来进行。

关于django-admin startprocject项目等架构，包括设置settings.py和ORM建表命令等请参考我之前的博客内容，不再做过多的阐释了。这是我的测试项目文件目录。
![在这里插入图片描述](https://img-blog.csdnimg.cn/9a672226d1f9416299e58bdab9e6aee6.png)

在视图部分，建了跳转页面与上传文件的视图函数，在视图函数中也包含了对 **数据库的操作** （见models.py)
需要上传的excel文件，**注意格式是 xlsx**
![在这里插入图片描述](https://img-blog.csdnimg.cn/d9e1e44900014045a2b6419d4564c7d2.png)

views.py  **注意xlrd需要下载1.2.0** ，如果版本过高存在无法读取xlrd的情况。其中为了方便，我在中途打印了里面的数据，以测试是否正常。

```python
import xlrd
from django.db import transaction

from django.shortcuts import render,HttpResponse,redirect
from TestDjan import models

def upload(request):
    return render(request,"upload.html")

def excel_upload(request):
    if request.method == "POST":
        file=request.FILES['my_file']
        print(file)
        type_file = file.name.split('.')[1]
        print(type_file)
        if type_file == 'xlsx':
            read_file = xlrd.open_workbook(filename=None,file_contents=file.read())
            file_table = read_file.sheets()[0]
            print(file_table)
            file_table_rows = file_table.nrows
            print(file_table_rows)

            try:
                with transaction.atomic():
                	# 读表格数据，从第二行开始，一般第一行都是说明
                    for i in range(1,file_table_rows):
                        print(i)
                        a=file_table.cell(i,0)
                        print(a.value)
                        b= file_table.cell(i,1)
                        print(b.value)
                        c=file_table.cell(i,2)
                        print(c.value)
                        models.test_excel_info.objects.create(name=a.value,pcnum=b.value,cause=c.value)
            except Exception as e:
                return render(request,"upload1.html",{'msg':e})
            return render(request,"upload1.html",{'msg':"suss"})
        return HttpResponse(render(request,"upload1.html",{'msg':"no"}))
    return HttpResponse(render(request,"upload1.html",{'msg':"none"}))
```

models.py ，这里是简单的一个表结构，不要忘记使用建表命令。

```python
from django.db import models

class test_excel_info(models.Model):
    name = models.CharField('名称',max_length=20)
    pcnum = models.CharField('电脑编号',max_length=30)
    cause = models.CharField('理由',max_length=30)

```

urls.py

```python
	path('upload/',views.upload),
    path('upload1',views.excel_upload),
```

下面是简单的前端测试代码：
upload.html,这是此页面
![在这里插入图片描述](https://img-blog.csdnimg.cn/d190f6edf5f9404da4c6b91475a3ecb7.png)

```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/upload1" method="post" enctype="multipart/form-data">

        {% csrf_token %}
        <input type="file" name="my_file">
        <input type="submit" value="提交">
    </form>
    <a href="/download_template" >点击下载模版文件</a>
</body>
</html>
```

选择浏览文件，点击上传后的页面，符合前面的逻辑。
![在这里插入图片描述](https://img-blog.csdnimg.cn/02395c9d34d34a68b8d83e24652837d4.png)
upload1.html

```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
{{ msg }}
</body>
</html>
```

进入到suss说明已经成功了，可以选择在数据库中查看是否已经导入完毕：
![在这里插入图片描述](https://img-blog.csdnimg.cn/5abae7305e544fcc8e02e1dedfeae1c5.png)
当然了，这是上传的excel工作，为了 **保证实际工作中不引起格式的混乱** ，将在前端页面中展示相应的下载模版文件选项。
views.py

```python
def download_template(request):
    file = open('Templates/test.xlsx', 'rb') # 路径不要写错了。
    response = FileResponse(file)
    response['Content-Type'] = 'application/octet-stream'
    response['Content-Disposition'] = 'attachment;filename="test.xlsx"'
    return response
```

urls.py

```python
	path('download_template/',views.download_template),
```

前端页面也在upload.html给出。

以上就是在django框架体系中对excel下载与上传内容。
