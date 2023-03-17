# REST 和 Python：职业的工具


> 翻译：马金池   
> 原文：Python and REST APIs: Interacting With Web Services   
> 链接：https://realpython.com/api-integration-in-python/#rest-and-python-tools-of-the-trade   


在本节中，你将看到在 Python 中构建 REST APIs 的三种流行的框架 。每种框架都有优点和缺点，因此你必须评估哪种框架最适合你的需求 。为此，在下一节中，你将了解每种框架中的 REST API 。所有示例都将针对管理国家的集合类似于API 。   

每个国家将有以下字段：

- `name` 是国家的名称 。
- `capital` 是国家的首都 。
- `area` 是以平方公里为单位的国家面积 。

字段 `name`, `captial` 和 `area` 存储关于世界上某个特定国家的数据 。

大多数时候，从REST API发送的数据来自数据库 。[连接到一个数据库](https://realpython.com/flask-connexion-rest-api-part-2/)已经超出了本教程的范围 。对于下面的示例，你将存储你的数据在Python列表中 。例外的是Django REST框架的例子，它运行于Django创建的SQLite数据库 。


> **注意：** 建议你为每个示例创建单独的文件夹以分割源文件 。你还需要使用[虚拟环境](https://realpython.com/python-virtual-environments-a-primer/)来隔离依赖项 。

为了保持一致，你将使用国家作为所有三个框架的主要端点 。你还将使用JSON作为你的所有三种框架的数据格式 。

现在你已经了解了API的背景，你可以继续前进到下一节，你将在这里看到`Flask`的REST API 。


## Flask


[Flask](https://realpython.com/introduction-to-flask-part-1-setting-up-a-static-site/) 是一个用于构建web应用和REST API的Python微框架。Flask为你的程序提供了一个坚固的支柱，同时给你提供了许多设计选择。Flask的主要工作是处理HTTP请求并将它们路由到应用程序中的适当函数。

> **注意：** 在此章节中使用的代码使用新的 [Flask 2](https://palletsprojects.com/blog/flask-2-0-released/) 语法 。 如果你正在运行旧版本的Flask，请使用[@app.route("/countries")](https://flask.palletsprojects.com/en/2.0.x/api/#flask.Flask.route) 代替 [@app.get("/countries")](https://flask.palletsprojects.com/en/2.0.x/api/#flask.Flask.get) 和 [@app.post("/countries")](https://flask.palletsprojects.com/en/2.0.x/api/#flask.Flask.post) 。
>
> 在老版本中处理POST请求，你还需要在`app.route():`中添加`methods`参数：
>
> > Python
> > ```python
> > @app.route("/countries", methods=["POST])
> > ```
> 
> 这条路由处理在Flask 1中到`/countries`的POST请求 。

以下是 REST API 的示例 Flask 应用程序：
> Python
> ```python
> # app.py
> from flask import Flask, request, jsonify
> 
> app = Flask(__name__)
> 
> countries = [
>     {"id": 1, "name": "Thailand", "capital": "Bangkok", "area": 513120},
>     {"id": 2, "name": "Australia", "capital": "Canberra", "area": 7617930},
>     {"id": 3, "name": "Egypt", "capital": "Cairo", "area": 1010408},
> ]
> 
> def _find_next_id():
>     return max(country["id"] for country in countries) + 1
> 
> @app.get("/countries")
> def get_countries():
>     return jsonify(countries)
> 
> @app.post("/countries")
> def add_country():
>     if request.is_json:
>         country = request.get_json()
>         country["id"] = _find_next_id()
>         countries.append(country)
>         return country, 201
>     return {"error": "Request must be JSON"}, 415
> ```

这个应用程序定义API端点`/countries`来管理国家列表 。它处理两种不同类型的请求：   
1. `GET /countries` 返回countries的列表。
2. `POST /countries` 添加一个新的country到列表中。

> **注意：** 这个 Flask 应用程序包含的函数只处理到API端点的两种类型请求的函数，`/countries` 。在完整的 REST API 中，你需要扩展它以包含所有必需操作的函数。

你可以尝试通过使用 `pip` 来安装 `flask` 这个应用程序：
> Shell
> ```bash
> $ python -m pip install flask
> ```

安装Flask后，将代码保存在一个名为`app.py`的文件中 。要运行这个Flask应用程序，你首先需要设置一个名为`Flask_APP`的环境变量给`app.py` 。这告诉了Flask哪个文件包含了你的应用程序 。

在包含`app.py`的文件夹中执行如下命令：
> Shell
> ```bash
> $ export FLASK_APP=app.py
> ```

这在当前shell中将FLASK_APP设置为app.py 。可选地，你可以设置FLASK_ENV为development，development将放置Flask在调试模式中:  

> Shell
> ```bash
> $ export FLASK_ENV=development
> ```

除了提供有用的错误消息外，调试模式将会在所有代码更改后触发重新加载应用程序 。如果没有调试模式，每次更改之后你都必须重新启动服务器 。

> **注意：** 以上命令工作在macOS或Linux。 如果你在Windows上运行这个命令，那么你需要像这样在命令提示符中设置`FLASK_APP`和`FLASK_ENV`：
>
> > Windows Command Prompt
> > ```powershell
> > C:\> set FLASK_APP=app.py
> > C:\> set FLASK_ENV=development
> > ```
> > 
> > 现在FLASK_APP和FLASK_ENV已经在Windows shell中设置。

所有的环境变量都准备好了，现在你可以通过调用`flask run`来启动Flask开发服务器：
> Shell
> ```bash
> $ flask run
> * Serving Flask app "app.py" (lazy loading)
> * Environment: development
> * Debug mode: on
> * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
> ```

这将启动服务器运行应用程序。 打开你的浏览器并且访问`http://127.0.0.1:5000/countries`，你会看到如下的响应：
> JSON
> ```json
> [
>   {
>       "area": 513120,
>       "capital": "Bangkok",
>       "id": 1,
>       "name": "Thailand"
>   },
>   {
>       "area": 7617930,
>       "capital": "Canberra",
>       "id": 2,
>       "name": "Australia"
>   },
>   {
>       "area": 1010408,
>       "capital": "Cairo",
>       "id": 3,
>       "name": "Egypt"
>   }
> ]
> ```

这个JSON响应包含在`app.py`开头定义的三个`countries`。 查看一下代码来了解其工作原理：
> Python
> ```python
> @app.get("/countries")
> def get_countries():
>     return jsonify(countries)
> ```

这段代码使用了`@app.get()`，一个Flask路由[装饰器](https://realpython.com/primer-on-python-decorators/)，将GET请求连接到应用程序中的一个函数 。当您访问`/countries`时，Flask调用这个修饰函数来处理HTTP请求并[返回](https://realpython.com/python-return-statement/)一个响应 。 

在上面的代码中，`get_countries()`获取`countries`，`contries`是一个[Python列表](https://realpython.com/python-lists-tuples/)，并使用jsonify()将其转换为JSON。 这个JSON在响应中返回。

> **注意：** 大多数时候，你可以从一个Flask函数返回一个Python字典。 Flask会自动将任何Python字典转换为JSON。 你可以通过下面的函数看到这一点：
> > Python
> > ```python
> > @app.get("/country")
> > def get_country():
> >     return countries[1]
> > ```
> >
> > 在这个代码中，你返回来自`countries`的第二个字典。 Flask会将这个字典转换成JSON。 当你请求`/country`时，你会看到以下内容:  
> > JSON
> > ```json
> > {
> >     "area": 7617930,
> >     "capital": "Canberra",
> >     "id": 2,
> >     "name": "Australia"
> > }
> > ```
> > 这是你从`get_country()`返回的字典的JSON版本。
> > 在`get_countries()`中，你需要使用jsonify()，因为你要返回的是一个字典列表并且不仅仅是一个单一的字典。 Flask不会自动将列表转换成JSON。  

现在看看 `add_country()`。这个函数处理到 `/countries` 的POST请求，并允许你向列表中添加一个新国家。 它使用 Flask [请求](https://flask.palletsprojects.com/en/1.1.x/quickstart/#the-request-object)对象来获取当前HTTP请求的信息：

> Python
> ```python
> @app.post("/countries")
> def add_country():
>     if request.is_json:
>         country = request.get_json()
>         country["id"] = _find_next_id()
>         countries.append(country)
>         return country, 201
>     return {"error": "Request must be JSON"}, 415
> ```

该函数的功能如下：
1. 使用 [`request.is_json`](https://flask.palletsprojects.com/en/1.1.x/api/?highlight=is_json#flask.Request.is_json) 来检查请求是否为JSON
2. 使用 `request.get_json()` 创建一个新的 `country` 实例
3. 找到下一个id并且设置在 `country` 上  
4. 附加新的 `country` 给 `countries`
5. 在响应中返回 `country` 和 `201 Created` 状态码
6. 如果请求不是 JSON ，返回一个错误消息和`415 Unsupported Media Type`状态码

`add_country()` 也调用 `_find_next_id()` 来确定新 `country` 的 `id` ：
> Python
> ```python
> def _find_next_id():
>     return max(country["id"] for country in countries) + 1
> ```

这个帮助函数使用一个[生成器表达式](https://realpython.com/introduction-to-python-generators/)来选择所有的国家IDs，然后对它们调用 `max()` 以获得最大的值。 它将这个值加1以获得下一个要使用的ID。

你可以在shell中使用命令行工具 [`curl`](https://curl.se/) 来测试这个端点，该工具允许您从命令行发送HTTP请求。 在这里，你将添加一个新的 `country` 到 `countries` 列表：

> Shell
> ```bash
> $ curl -i http://127.0.0.1:5000/countries \
> -X POST \
> -H 'Content-Type: application/json' \
> -d '{"name":"Germany", "capital": "Berlin", "area": 357022}'
> 
> HTTP/1.0 201 CREATED
> Content-Type: application/json
> ...
> 
> {
>     "area": 357022,
>     "capital": "Berlin",
>     "id": 4,
>     "name": "Germany"
> }
> ```

curl 命令有一些有用的选项：

- **-X** 设置请求的HTTP方法。
- **-H** 向请求添加一个HTTP报头。
- **-d** 定义请求数据。

设置了这些选项后，curl在POST请求中发送JSON数据，并将 `Content-Type` 报头设置为 `application/json` 。REST API返回 `201 CREATED` 以及你添加的新 `country` 的JSON。

> **注意：** 在这个例子中，`add_country()` 不包含任何验证来确认请求中的JSON是否匹配 `countries` 的格式。如果你想验证JSON的格式在Flask，请查看 [flask-expects-json](https://pypi.org/project/flask-expects-json/) 。

您可以使用curl向 `/countries` 发送GET请求来确认添加了新 `country` 。如果你在curl命令中不使用 `-X` ，那么默认情况下它会发送一个GET请求：

> Shell
> ```bash
> curl -i http://127.0.0.1:5000/countries
> 
> HTTP/1.0 200 OK
> Content-Type: application/json
> ...
> 
> [
>     {
>         "area": 513120,
>         "capital": "Bangkok",
>         "id": 1,
>         "name": "Thailand"
>     },
>     {
>         "area": 7617930,
>         "capital": "Canberra",
>         "id": 2,
>         "name": "Australia"
>     },
>     {
>         "area": 1010408,
>         "capital": "Cairo",
>         "id": 3,
>         "name": "Egypt"
>     },
>     {
>         "area": 357022,
>         "capital": "Berlin",
>         "id": 4,
>         "name": "Germany"
>     }
> ]
> ```

这将返回系统中 `countries` 的完整列表，最新的 `country` 在底部。  

这只是Flask能力的一个取样。这个应用程序可以扩展为包括所有其他HTTP方法的端点。Flask还有一个大型的扩展生态系统，它为REST api提供了额外的功能，比如[数据库集成](https://realpython.com/flask-connexion-rest-api/)、[身份验证](https://realpython.com/flask-google-login/)和后台处理。

## Django REST Framework


另一个构建 REST APIs 的流行选择是 [Django REST框架](https://realpython.com/django-rest-framework-quick-start/) 。 Django REST框架是一个 [Django](https://realpython.com/get-started-with-django-1/) 插件，它在现有的Django项目上添加了REST API功能。  

要使用Django REST框架，你需要一个Django项目。 如果您已经有了一个模式，那么您可以将部分中的模式应用到您的项目中。 否则，按照下面的步骤，你将会构建一个Django项目并将其加入Django REST框架 。

首先，用 `pip` 安装 `Django` 和 `djangorestframework` ：
> Shell
> ```bash
> $ python -m pip install Django djangorestframework
> ```

这将安装 `Django` 和 `djangorestframework`。你现在可以使用 `django-admin` 工具来创建一个新的Django项目。运行以下命令启动项目：
> Shell
> ```bash
> $ django-admin startproject countryapi
> ```

这个命令在当前目录中创建一个名为countryapi的新文件夹。这个文件夹中包含了运行Django项目所需的所有文件。接下来，你将在项目中创建一个新的**Django应用程序**。 Django将项目的功能分解为应用程序。 每个应用程序管理项目的不同部分。

> **注意：** 在本教程中，您只会对Django的功能进行比较浅的了解。 如果您有兴趣了解更多内容，请查看可用的 [Django教程](https://realpython.com/tutorials/django/) 。

要创建应用程序，请将目录更改为 `countryapi` ，并运行以下命令:  
> Shell
> ```bash
> $ python manage.py startapp countries
> ```

这将在项目中创建一个新的 `countries` 文件夹。 此文件夹内是此应用程序的基本文件。

现在你已经创建了一个要使用的应用程序，你需要将它告诉Django。 在你刚刚创建的 `contries` 文件夹旁边是另一个名为 `countryapi` 的文件夹。 此文件夹包含项目的配置和设置。

> **注意：** 这个文件夹和Django在运行 `django-admin startproject countryapi` 时创建的根文件夹同名。

打开 `countryapi` 文件夹中的 `settings.py` 文件。 在 `INSTALLED_APPS` 中添加以下几行，告诉Django关于 `countries` 应用和Django REST框架的信息：

> Python
> ```python
> # countryapi/settings.py>
> INSTALLED_APPS = [
>     "django.contrib.admin",
>     "django.contrib.auth",
>     "django.contrib.contenttypes",
>     "django.contrib.sessions",
>     "django.contrib.messages",
>     "django.contrib.staticfiles",
>     "rest_framework",
>     "countries",
> ]
> ```

你已经为 `countries` 应用程序和 `rest_framework` 添加了一行。

你可能想知道为什么需要将 `rest_framework` 添加到应用程序列表中。你需要添加它，因为Django REST框架只是另一个Django应用程序。Django插件是打包和分发的Django应用程序，任何人都可以使用。

下一步是创建一个Django模型来定义数据的字段。 在 `countries` 应用程序内部，用以下代码更新 `models.py` ：
> Python
> ```python
> # countries/models.py
> from django.db import models
> 
> class Country(models.Model):
>     name = models.CharField(max_length=100)
>     capital = models.CharField(max_length=100)
>     area = models.IntegerField(help_text="(in square kilometers)")
> ```

这段代码定义了一个 `Country` 模型。 Django将使用这个模型来创建 `country` 数据的数据库表和列。  

运行以下命令让Django根据这个模型更新数据库：
> Shell
> ```bash
> $ python manage.py makemigrations
> Migrations for 'countries':
>   countries/migrations/0001_initial.py
>     - Create model Country
> 
> $ python manage.py migrate
> Operations to perform:
>   Apply all migrations: admin, auth, contenttypes, countries, sessions
> Running migrations:
>   Applying contenttypes.0001_initial... OK
>   Applying auth.0001_initial... OK
>   ...
> ```

这些命令使用Django迁移在数据库中创建一个新表。

这个表一开始是空的，但是如果有一些初始数据，这样你就可以测试Django REST框架了。 为此，你将使用 [Django fixture](https://realpython.com/django-pytest-fixtures/) 在数据库中加载一些数据。

复制并保存以下JSON数据到一个名为 `countries.json` 的文件中，并将其保存在 `countries` 目录： 
> JSON
> ```json
> [
>     {
>         "model": "countries.country",
>         "pk": 1,
>         "fields": {
>             "name": "Thailand",
>             "capital": "Bangkok",
>             "area": 513120
>         }
>     },
>     {
>         "model": "countries.country",
>         "pk": 2,
>         "fields": {
>             "name": "Australia",
>             "capital": "Canberra",
>             "area": 7617930
>         }
>     },
>     {
>         "model": "countries.country",
>         "pk": 3,
>         "fields": {
>             "name": "Egypt",
>             "capital": "Cairo",
>             "area": 1010408
>         }
>     }
> ]
> ```

这个JSON包含三个国家的数据库条目。 调用以下命令将该数据加载到数据库中:  
> Shell
> ```bash
> $ python manage.py loaddata countries.json
> Installed 3 object(s) from 1 fixture(s)
> ```

这向数据库添加了三行。

这样，Django应用程序就全部设置好了并且填充了一些数据。 现在可以开始向项目中添加Django REST框架了。

Django REST框架接受一个现有的Django模型并且将其转换为JSON作为REST API。它使用**模型序列化器**来实现这一点。模型序列化器告诉Django REST框架如何将模型实例转换为JSON，以及包含哪些数据。

您将从上面为 `Country` 模型创建序列化器。 首先在 `countries` 应用程序中创建一个名为 `serializer.py` 的文件。 一旦你这样做了，添加以下代码到 `serializer.py` ：

> Python
> ```python
> # countries/serializers.py
> from rest_framework import serializers
> from .models import Country
> 
> class CountrySerializer(serializers.ModelSerializer):
>     class Meta:
>         model = Country
>         fields = ["id", "name", "capital", "area"]
> ```

这个序列化器，`CountrySerializer` ，子类 `serializers.ModelSerializer` 可以根据 `Country` 的模型字段自动生成JSON内容。 除非指定，否则 `ModelSerializer` 的子类将在JSON中包含Django模型中的所有字段。 你可以通过将字段设置为希望包含的数据列表来修改此行为。

就像Django一样，Django REST框架使用 [视图](https://docs.djangoproject.com/en/dev/topics/http/views/) 从数据库中查询数据并显示给用户。 与从头开始编写REST API视图不同，你可以继承Django REST框架的 [ModelViewSet](https://www.django-rest-framework.org/api-guide/viewsets/#modelviewset) 类，它为常见的REST API操作提供了默认视图。

> **注意：** Django REST框架文档将这些视图称为 [动作](https://www.django-rest-framework.org/api-guide/viewsets/#viewset-actions) 。

下面是 `ModelViewSet` 提供的操作及其等效的HTTP方法的列表：
| HTTP 方式 | 动作 | 描述 |
| :--- | :--- | :--- |
| GET	 |.list()      | 获取国家列表。|
| GET	 |.retrieve()  | 获取一个单一的国家。|
| POST   |.create()    | 创建一个新的国家。|
| PUT	 |.update()   | 更新一个国家。 |
| PATCH	 |.partial_update() | 部分更新一个国家。|
| DELETE |.destroy()   | 删除一个国家。|

如你所见，这些操作映射到REST API中所期望的标准HTTP方法。 你可以在子类中 [重写这些操作](https://www.django-rest-framework.org/api-guide/viewsets/#example) ，或者根据API的需求添加 [额外的操作](https://www.django-rest-framework.org/api-guide/viewsets/#marking-extra-actions-for-routing) 。

下面是一个叫做 `CountryViewSet` 的 `ModelViewSet` 子类的代码。 这个类将生成管理 `Country` 数据所需的视图。 在 `Country` 应用程序的 `views.py` 中添加以下代码：

> Python
> ```python
> # countries/views.py
> from rest_framework import viewsets
> 
> from .models import Country
> from .serializers import CountrySerializer
> 
> class CountryViewSet(viewsets.ModelViewSet):
>     serializer_class = CountrySerializer
>     queryset = Country.objects.all()
> ```

在这个类中，`serializer_class` 被设置为 `CountrySerializer` 并且 `queryset` 被设置为 `Country.objects.all()` 。 这告诉Django REST框架使用哪个序列化器，以及如何查询这个特定视图集的数据库。

创建视图之后，需要将它们映射到适当的URLs或端点。为此，Django REST框架提供了一个 l`DefaultRouter` ，它会自动为 `ModelViewSet` 生成URLs。

在 `countries` 应用程序中创建一个 `url.py` 文件，并添加以下代码到文件中：
> Python
> ```python
> # countries/urls.py
> from django.urls import path, include
> from rest_framework.routers import DefaultRouter
> 
> from .views import CountryViewSet
> 
> router = DefaultRouter()
> router.register(r"countries", CountryViewSet)
> 
> urlpatterns = [
>     path("", include(router.urls))
> ]
> ```

这段代码创建了一个 `DefaultRouter` ，并在 `countries` URL下注册了 `CountryViewSet` 。这将把 `CountryViewSet` 的所有URLs放在 `/countries/` 下。  

> **注意：** Django REST框架会自动在 `DefaultRouter` 生成的任何端点的末尾添加一个正斜杠(/)。 你可以像这样禁用这个行为：
> > Python
> > ```python
> > router = DefaultRouter(trailing_slash=False) 
> > ```
> >
> > 这将在端点的末尾禁用正斜杠。

最后，你需要更新项目的基本 `urls.py` 文件，以包括项目中所有 `countries` 的URLs。 用以下代码更新 `countryapi` 文件夹内的 `url.py` 文件：

> Python
> ```python
> # countryapi/urls.py
> from django.contrib import admin
> from django.urls import path, include
> 
> urlpatterns = [
>     path("admin/", admin.site.urls),
>     path("", include("countries.urls")),
> ]
> ```

这将把所有的URLs放在 `/countries/` 下。 现在，你可以尝试一下你的Django-backed的REST API了。在根目录 `countryapi` 下执行如下命令启动Django开发服务器：
> Shell
> ```bash
> $ python manage.py runserver
> ```

开发服务器现在正在运行。继续向 `/countries/` 发送一个 `GET` 请求来获取Django项目中所有国家的列表：
> Shell
> ```bash
> $ curl -i http://127.0.0.1:8000/countries/ -w '\n'
> 
> HTTP/1.1 200 OK
> ...
> 
> [
>     {
>         "id": 1,
>         "name":"Thailand",
>         "capital":"Bangkok",
>         "area":513120
>     },
>     {
>         "id": 2,
>         "name":"Australia",
>         "capital":"Canberra",
>         "area":7617930
>     },
>     {
>         "id": 3,
>         "name":"Egypt",
>         "capital":"Cairo",
>         "area":1010408
>     }
> ]
> ```

Django REST框架发回一个JSON响应，其中包含了你之前添加的三个国家。 上面的响应是为了可读性而格式化的，因此你的响应看起来会有所不同。

 你在 `countries/urls.py` 中创建的 [DefaultRouter](https://www.django-rest-framework.org/api-guide/routers/#defaultrouter) 为所有标准API端点的请求提供了URLs：

- GET /countries/
- GET /countries/<country_id>/
- POST /countries/
- PUT /countries/<country_id>/
- PATCH /countries/<country_id>/
- DELETE /countries/<country_id>/

你可以在下面尝试更多的端点。 向 `/countries/` 发送POST请求，在你的Django项目中创建一个新的Country：
> Shell
> ```bash
> $ curl -i http://127.0.0.1:8000/countries/ \
> -X POST \
> -H 'Content-Type: application/json' \
> -d '{"name":"Germany", "capital": "Berlin", "area": 357022}' \
> -w '\n'
> 
> HTTP/1.1 201 Created
> ...
> 
> {
>    "id":4,
>    "name":"Germany",
>    "capital":"Berlin",
>    "area":357022
> }
> ```

这将使用你在请求中发送的JSON创建一个新的 `Country` 。Django REST框架返回 `201 Created` 状态码和新的 `Country` 。

> **注意：** 默认情况下，响应的末尾不包含一个新行。 这意味着JSON可能会运行到命令提示符中。 上面的curl命令包括-w '\n'来在JSON后面添加换行符来解决这个问题。  

你可以通过使用已存在的id发送请求到 `GET /countries/<country_id>/` 来查看已存在的 `Country` 。 执行以下命令获取第一个`Country` ：

> Shell
> ```bash
> $ curl -i http://127.0.0.1:8000/countries/1/ -w '\n'
> 
> HTTP/1.1 200 OK
> ...
> 
> {
>     "id":1,
>     "name":"Thailand",
>     "capital":"Bangkok",
>     "area":513120
> }
> ```

响应包含第一个 `Country` 的信息。这些示例仅涵盖 `GET` 和 `POST` 请求。你可以自己尝试 `PUT, PATCH,` 和 `DELETE` 请求来了解如何从REST API完全管理您的模型。

如你所见，Django REST框架是构建REST API的一个很好的选择，特别是当你有一个现有的Django项目并且你想要添加一个API的时候。

## FastAPI


[FastAPI](https://realpython.com/fastapi-python-web-apis/) 是一个为构建api而优化的Python web框架。 它使用 [Python类型提示](https://realpython.com/python-type-checking/) 并内置了对 [异步操作](https://realpython.com/async-io-python/) 的支持。 FastAPI是建立在 [Starlette](https://www.starlette.io/) 和 [Pydantic](https://pydantic-docs.helpmanual.io/) 之上的，并且非常高效。

下面是一个用FastAPI构建的REST API的例子：
> Python
> ```python
> # app.py
> from fastapi import FastAPI
> from pydantic import BaseModel, Field
> 
> app = FastAPI()
> 
> def _find_next_id():
>     return max(country.country_id for country in countries) + 1
> 
> class Country(BaseModel):
>     country_id: int = Field(default_factory=_find_next_id, alias="id")
>     name: str
>     capital: str
>     area: int
> 
> countries = [
>     Country(id=1, name="Thailand", capital="Bangkok", area=513120),
>     Country(id=2, name="Australia", capital="Canberra", area=7617930),
>     Country(id=3, name="Egypt", capital="Cairo", area=1010408),
> ]
> 
> @app.get("/countries")
> async def get_countries():
>     return countries
> 
> @app.post("/countries", status_code=201)
> async def add_country(country: Country):
>     countries.append(country)
>     return country
> ```

这个应用程序使用FastAPI的特性为你在其他示例中看到的相同 `country` 数据构建一个REST API。

你可以通过使用pip安装fastapi来尝试此应用程序：

> Shell
> ```bash
> $ python -m pip install fastapi
> ```

你还需要安装 `uvicorn[standard]` ，一个可以运行FastAPI应用程序的服务器：

> Shell
> ```bash
> $ python -m pip install uvicorn[standard]
> ```

如果你已经安装了 `fastapi` 和 `uvicorn` 两者，那么将上面的代码保存在一个名为 `app.py` 的文件中。 运行如下命令启动开发服务器：

> Shell
> ```bash
> $ uvicorn app:app --reload
> INFO: Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
> ```

服务器现在正在运行。打开浏览器并访问 `http://127.0.0.1:8000/countries` 。你会看到FastAPI响应如下:  
> JSON
> ```json
>  [
>    {
>         "id": 1,
>         "name":"Thailand",
>         "capital":"Bangkok",
>         "area":513120
>     },
>     {
>         "id": 2,
>         "name":"Australia",
>         "capital":"Canberra",
>         "area":7617930
>     },
>     {
>         "id": 3,
>         "name":"Egypt",
>         "capital":"Cairo",
>         "area":1010408
>     }
> ]
> ```

FastAPI响应一个包含 `countries` 列表的JSON数组。你也可以通过向 `/countries` 发送POST请求来添加一个新的国家：

> Shell
> ```bash
> $ curl -i http://127.0.0.1:8000/countries \
> -X POST \
> -H 'Content-Type: application/json' \
> -d '{"name":"Germany", "capital": "Berlin", "area": 357022}' \
> -w '\n'
> 
> HTTP/1.1 201 Created
> content-type: application/json
> ...
> 
> {"id":4,"name":"Germany","capital":"Berlin","area": 357022}
> ```

你增加了一个新的国家。你可以通过 `GET /countries` 来确认：

> Shell
> ```bash
> $ curl -i http://127.0.0.1:8000/countries -w '\n'
> 
> HTTP/1.1 200 OK
> content-type: application/json
> ...
> 
> [
>     {
>         "id":1,
>         "name":"Thailand",
>         "capital":"Bangkok",
>         "area":513120,
>     },
>     {
>         "id":2,
>         "name":"Australia",
>         "capital":"Canberra",
>         "area":7617930
>     },
>     {
>         "id":3,
>         "name":"Egypt",
>         "capital":"Cairo",
>         "area":1010408
>     },
>     {
>         "id":4,
>         "name": "Germany",
>         "capital": "Berlin",
>         "area": 357022
>     }
> ]
> ```

FastAPI返回了一个包括你刚刚添加新国家的JSON列表。

你将会注意到FastAPI应用程序看起来与Flask应用程序相似。像Flask一样，FastAPI也有一个集中的特性集。 它不会尝试处理web应用程序开发的所有方面。 它是用来构建带有现代Python特性的api的。

如果你看 `app.py` 的顶部，你会看到一个名为 `Country` 的类，它扩展了 `BaseModel` 。`Country` 类描述了REST API中的数据结构：

> Python
> ```python
> class Country(BaseModel):
>     country_id: int = Field(default_factory=_find_next_id, alias="id")
>     name: str
>     capital: str
>     area: int
> ```

这是 [Pydantic模型](https://pydantic-docs.helpmanual.io/usage/models/) 的一个例子。Pydantic模型在FastAPI中提供了一些有用的特性。它们使用Python类型注释来强制类中每个字段的数据类型。这允许FastAPI自动为API端点生成具有正确数据类型的JSON。它还允许FastAPI验证传入的JSON。

突出显示第一行是会很有帮助的，因为那里有很多事情要做：
> Python
> ```python
> country_id: int = Field(default_factory=_find_next_id, alias="id")
> ```

在这一行中，你会看到 `country_id` ，`country_id` 存储了国家ID的 [整数](https://realpython.com/python-numbers/#integers) 。它使用Pydantic的 [字段函数](https://pydantic-docs.helpmanual.io/usage/schema/#field-customisation) 来修改 `country_id` 的行为。在这个例子中，你传递了字段关键字参数 `default_factory` 和 `alias`。

第一个参数 `default_factory` 被设置为 `_find_next_id()` 。这个参数指定一个函数，每当创建一个新的国家时要运行它。 返回值将被分配给 `country_id` 。

第二个参数 `alias` 被设置为 `id` 。这告诉FastAPI输出键 `“id”` 而不是JSON中的 `“country_id”` ： 

> JSON
> ```json
> {
>     "id":1,
>     "name":"Thailand",
>     "capital":"Bangkok",
>     "area":513120,
> },
> ```

这个 `alias` 还意味着你可以在创建一个新的国家时使用 `id` 。你可以在 `countries` 列表中看到：

> Python
> ```python
> countries = [
>     Country(id=1, name="Thailand", capital="Bangkok", area=513120),
>     Country(id=2, name="Australia", capital="Canberra", area=7617930),
>     Country(id=3, name="Egypt", capital="Cairo", area=1010408),
> ]
> ```

这个列表包含API中初始国家的三个Country实例。Pydantic模型提供了一些很好的特性并允许FastAPI轻松地处理JSON数据。

现在看看这个应用程序中的两个API函数。 第一个是`get_countries()`，它返回一个国家列表，用于 `GET` 请求到 `/countries` ：

> Python
> ```python
> @app.get("/countries")
> async def get_countries():
>     return countries
> ```

FastAPI将根据Pydantic模型中的字段自动创建JSON，并根据Python类型提示设置正确的JSON数据类型。

Pydantic模型在向 `/countries` 发出POST请求时也提供了好处。 在下面的第二个API函数中，你可以看到参数 `country` 有一个 `Country` 注释：

> Python
> ```python
> @app.post("/countries", status_code=201)
> async def add_country(country: Country):
>     countries.append(country)
>     return country
> ```

这个类型注释告诉FastAPI根据Country验证传入的JSON。如果不匹配，那么FastAPI将返回一个错误。你可以尝试用JSON发出一个不匹配Pydantic模型的请求：

> Shell
> ```
> $ curl -i http://127.0.0.1:8000/countries \
> -X POST \
> -H 'Content-Type: application/json' \
> -d '{"name":"Germany", "capital": "Berlin"}' \
> -w '\n'
> 
> HTTP/1.1 422 Unprocessable Entity
> content-type: application/json
> ...
> 
> {
>     "detail": [
>         {
>             "loc":["body","area"],
>             "msg":"field required",
>             "type":"value_error.missing"
>         }
>     ]
> }
> ```

这个请求中的JSON缺少一个 `area` 的值，所以FastAPI返回了一个带有状态码 `422 Unprocessable Entity` 的响应，以及关于错误的详细信息。 Pydantic模型使这种验证成为可能。

这个例子只触及了FastAPI的皮毛。FastAPI具有高性能和现代特性，比如 `async` 函数和自动文档，FastAPI值得考虑最为你的下一个REST API。


# 结论


REST APIs无处不在。了解如何利用Python来使用和构建api可以允许你处理web服务提供的大量数据。

**在本教程中，您已经学习了如何：**
- 确定**REST体系结构**风格
- 使用**HTTP方法**和状态代码
- 使用**请求**从外部API获取和使用数据
- 为REST API定义**端点**，**数据**和**响应**
- 开始使用Python工具来构建**REST API**

使用你新的Python REST API技能，你不仅可以与web服务交互，还可以为你的应用程序构建REST API。 这些工具为各种有趣的数据驱动应用程序和服务打开了大门。