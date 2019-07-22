# 自定义admin站点
---
**如果只是在admin后台管理页面中展示及管理模型，需要在`admin.py`文件中使用`admin.site.register`注册模型**
```python
from django.contrib import admin
from polls.models import Book

# Register your models here.
admin.site.register(Book)
```

**添加关联的对象**

>在我们的Book模型中，publisher和authors 字段都对应了另外一个表  
当我们向后台注册 这两个模型后，在Book 的 publisher和authors 字段旁边会出现’添加‘ 的按钮  

```python
from django.contrib import admin

from polls.models import Book,Publisher,Author

# Register your models here.

admin.site.register(Book)
admin.site.register(Publisher)
admin.site.register(Author)
```
>每个使用 ForeignKey 关联到另一个对象的对象会自动获得这个功能。当你点击“添加”按钮时，你会见到一个包含“添加投票”的表单。  
如果你在这个弹出框中添加了一个投票，并点击了“保存”，Django 会将其保存至数据库，并动态地在你正在查看的“添加选项”表单中选中它


### 1、修改admin时区和语言支持
* settings.py 文件中
```python
LANGUAGE_CODE = 'zh-Hans'       #设置为中文

TIME_ZONE = 'Asia/Shanghai'     #修改时区为shanghai

```


### 2、定义模型及字段的名称
> models.py

```python
class Book(models.Model):
    name = models.CharField(max_length=300,verbose_name='书名')   #为该字段取一个别名，用于在admin管理页面显示
    pages = models.IntegerField()
    price = models.DecimalField(max_digits=10, decimal_places=2)
    authors = models.ManyToManyField(Author)
    publisher = models.ForeignKey(Publisher, on_delete=models.CASCADE)
    pubdate = models.DateField()

    class Meta:     #此类是模型的子类，有缩进
        verbose_name = '书籍'     #设置模型对象的直观、人类可读的名称。可以用中文
        verbose_name_plural = '书籍'   #模型对象复数形式的名称

    def __str__(self):
        return self.name
```
`verbose_name` 如果你不指定它，那么Django会使用小写的模型名作为默认值  
`verbose_name_plural`就是模型对象的复数名。英语有单数和复数形式，比如“Books”。在中文中通常不区分单复数，所以保持和verbose_name一致即可


### 3、使用ModelAdmin 来定制admin管理界面
>**真正用来定制admin的手段，大部分都集中在这些ModelAdmin内置的属性上  
>ModelAdmin非常灵活，它有许多内置属性，帮助我们自定义admin的界面和功能  
>所有的属性都定义在ModelAdmin的子类中  
ModelAdmin类是一个模型在admin页面里的展示方法
**

**常用方法有：**
```python
list_display = ('name','price','pubdate')   #设置横向展示的列数，这些字段不能使用关联字段
list_display_links = ('pubdate',)     # 设置需要添加<a>标签的字段
list_filter = ('create_date', 'category')   # 激活过滤器，这个很有用
list_per_page = 50  # 控制每页显示的对象数量，默认是100
filter_horizontal = ('authors',)  #给多选增加一个左右添加的框,此字段必须是一个多对多字段
search_fields = ('name',)  # 添加搜索框，根据给定字段来搜索

```

#### 3.1、admin.py

```python
from django.contrib import admin

from polls.models import Book,Publisher,Author

# Register your models here.
admin.site.register(Publisher)
admin.site.register(Author)

class BookAdmin(admin.ModelAdmin):
    list_display = ('name','price','pubdate')
    search_fields = ('name',)
    list_display_links = ('pubdate',)
    list_filter = ('pubdate', 'authors')
    list_per_page = 50
    filter_horizontal = ('authors',)

# 注册的时候，将原模型和ModelAdmin耦合起来
admin.site.register(Book,BookAdmin)

```
