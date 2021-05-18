# Django 学习

## 1. 安装

```shell
$python -m django --version
```

如果安装成功，会显示版本信息

## 2. 创建一个工程

```shell
$django-admin startproject mysite
```

工程结构

```shell
$tree mysite
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

* mysite 外层的目录是工程容器，可以重命名。
* manage.py　Django project配置，具体信息可以参考[django-admin and manage.py](https://docs.djangoproject.com/en/3.2/ref/django-admin/).
* mysite:是实际工程的python包。
* mysite/setting.py: 当前Django project设置和配置。详见 [Django settings](https://docs.djangoproject.com/en/3.2/topics/settings/) 
* mysite/urls.py: [URL dispatcher](https://docs.djangoproject.com/en/3.2/topics/http/urls/).
* `mysite/asgi.py`: An entry-point for ASGI-compatible web servers to serve your project. See [How to deploy with ASGI](https://docs.djangoproject.com/en/3.2/howto/deployment/asgi/) for more details.
* `mysite/wsgi.py`: An entry-point for WSGI-compatible web servers to serve your project. See [How to deploy with WSGI](https://docs.djangoproject.com/en/3.2/howto/deployment/wsgi/) for more details.

## 3. 服务部署

```shell
$ python manage.py runserver
```

```shell
Performing system checks...

System check identified no issues (0 silenced).

You have unapplied migrations; your app may not work properly until they are applied.
Run 'python manage.py migrate' to apply them.

April 09, 2021 - 15:50:53
Django version 3.2, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

设计端口和IP

```shell
$python manage.py runserver 0:8000
```

> 0是0.0.0.0简写

## 4. 创建选举应用

**工程（projects）和应用（apps）**

一个工程可以有很多应用，一个应用可以包含在很多工程中。

一个应用可以是：weblog系统应用，数据库应用。

创建一个选举应用

```shell
$python manage.py startapp polls
```

```shell
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```

4.1 创建VIEW

polls/views.py

```python
from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

polls/urls.py

```python
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

将添加polls.urls到跟节点的URL配置中，具体指定在mysite/urls.py中

mysite/urls.py

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

> path参数
>
> route:urlPattlern,其不读取get,post的参数
>
> view:使用HttpRequest调用捕获到到的结构
>
> kwargs:传给views的参数
>
> name:给URL取个名字

```python
$python manage.py runserver
If you get an error page here, check that you’re going to http://localhost:8000/polls/ and not http://localhost:8000/.
```

## 5. 数据库初始化

mysite/settings.py:这是一个python的普通模块用于python模块级变量的django设置。

```python
# Database
# https://docs.djangoproject.com/en/3.1/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

默认使用SQLite，如果要切换到其他的数据库参照 [database bindings](https://docs.djangoproject.com/en/3.2/topics/install/#database-installation) ,并变更一下DATABASES'default'项目以满足数据库连接设置。

* ENGINE -可以置'django.db.backends.sqlite3'`, `'django.db.backends.postgresql'`, `'django.db.backends.mysql'`, or `'django.db.backends.oracle'
* NAME:database的名称，如果使用SQLite是数据库文件的绝对路径.
* 如果使用SQLites作为数据库，需要设置 [`USER`](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-USER), [`PASSWORD`](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-PASSWORD), and [`HOST`](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-HOST)。查看 [`DATABASES`](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-DATABASES)获取更多信息