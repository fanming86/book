# 1-request请求

---
**HTTP协议以"请求－回复"的方式工作。客户发送请求时，可以在请求中附加数据。服务器通过解析请求，就可以获得客户传来的数据，并根据URL来提供特定的服务**

### GET 请求

**1、视图文件 `polls/views.py` **  
>创建一个视图 index 用于首次访问，即发起请求的页面  
>search 视图，用于接收请求，并将请求的内容返回

```python
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.

def index(request):
    return render(request,'index.html')

# 接收请求数据
def search(request):
    if 'q' in request.GET:
        message = '你搜索的内容为: ' + request.GET['q']
    else:
        message = '你提交了空表单'
    return HttpResponse(message)
```
**2、在templates 目录下创建一个 `index.html` 文件**
>页面使用from表单，用来发起request请求

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>search page</title>
</head>
<body>
    <form action="/search" method="get">
        <input type="text" name="q">
        <input type="submit" value="搜索">
    </form>
</body>
</html>

```
**3、 `polls/urls.py`  配置路由**
```python
from django.urls import path
from polls import views

urlpatterns = [
    path('index/', views.index),
    path('search/',views.search)
]

```

**4、请求页面 `http://127.0.0.1:8000/index`**

###POST请求
>上面我们使用了GET方法。视图显示和请求处理分成两个函数处理
提交数据时更常用POST方法。我们下面使用该方法，用一个URL和函数，同时显示视图和处理请求

**1、创建html 文件 `templates/post.html`**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>post test</title>
</head>
<body>
    <form action="/searchPost" method="post">
             <!--csrf 全称是 Cross Site Request Forgery-->
        <input type="text" name="q">
        <input type="submit" value="Submit">
    </form>

    <p>{{ result }}</p>
</body>
</html>

```
* 这里的p 标签中引用一个变量，用来接收返回值  
* *这是Django提供的防止伪装提交请求的功能。POST 方法提交的表格，必须有此标签*


**2、views.py 添加视图 search_post**
>使用search_post 视图同时处理get 和post 请求

```python
# 接收POST请求数据
def search_post(request):
    ctx = {}
    if request.POST:
        ctx['result'] = request.POST['q']
    return render(request, "post.html", ctx)
```
* ctx 为一个空字典，用来存放post 请求的数据
* 当第一次请求时，即为get 请求，render方法返回 post.html 页面，ctx仍为空  
* 当发起 post 请求时，向字典 ctx 中添加内容

**3、配置路由 urls.py**
```django
path('searchPost',views.search_post),
```

**4、请求页面 `http://127.0.0.1:8000/searchPost`**
