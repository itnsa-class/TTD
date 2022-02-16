# 将你的Django项目的开发和生产（环境）分开

> 作者：[Abhyudai | HackAdda](https://www.hackadda.com/author/abhi/)
>
> 浏览数：5.24K 
>
> 发布时间：2020年 2月 22日
>
> 最后修改时间：2021年 7月 14日
>
> 翻译：Carl Fan
>
> [原文章](https://www.hackadda.com/post/2020/2/21/separate-your-development-and-production-settings-for-a-django-project/)

<img src="https://www.hackadda.com/media/blog/separate-your-production-and-development-settings.png" alt="Separate your development and production settings for a django project" style="zoom:50%;" />

​	当我第一次开始在Django上工作，每次我需要将代码放到开发环境时，我都会编辑 `settings`文件。更改`DEBUG SECURITY_KEY`的值，从配置文件和许多其他设置中读取`DEBUG SECURITY_KEY`的值。这曾经是一（种）非常痛苦的经历，每次我不得不去这么做时，我都会（在心里）咒骂自己。

​	然后，在美好的一天，我受够了。我决定考虑找（一个）在将代码推送到生产环境时不需要进行任何修改的方法。我必须将`开发`和 `生产`环境分开



​	我决定我们可以将`settings`做成一个python模块。当前情况之下，目录结构将会看起来像：

  ```
├── manage.py
└── django_project
    ├── settings.py
    ├── urls.py
    ├── views.py
    └── wsgi.py
  ```

​	让我们开始设置所有东西。

## 创建一个`settings`目录

​	现在目录结构看起来是这样的

  ```
├── manage.py
└── django_project
    ├── settings
    ├── urls.py
    ├── views.py
    └── wsgi.py
  ```

## 创建`base.py`文件

​	在`settings`中创建一个`base.py`文件。这个文件应该包含将被`开发`和`生产`环境使用的设置。

​	你可以在这个文件中使用设置除了`DEBUG SECURIITY_KEY`;`ALLOWED_HOSTS`之外的所有内容，我们将设置他们，不必担心。

## 创建`dev.py`文件

​	在`settings`文件夹中，创建另一个名为`dev.py`的python文件。文件中的内容看起来应该如下所示

```python
from .base import *

DEBUG = True

ALLOWED_HOSTS = []
```

​	如果你使用类似[django-debug-toolbar](https://github.com/jazzband/django-debug-toolbar)的内容，则应在此处初始化其设置。

## 创建`prod.py`文件

​	你应该在`settings`目录中创建这个文件。文件的内容如下：

```python
from .base import *

DEBUG = False

ALLOWED_HOSTS = ['mysite.com', 'ip.of.my.site', 'www.mysite.com', ]
```

​	如果你使用类似[Sentry](https://sentry.io/welcome/)的内容在部署服务器上记录错误，则在这个文件中初始化它

## 创建一个`JSON`文件

​	这个`JSON`文件将包含我们的`security key`和一个密钥。这个密钥将帮助我们庚戌环境选择Django项目使用的文件。`PROD`

​	你将需要同时在开发和生产环境中创建这个文件。如果你正在使用一个版本控制软件像 `Git`,`Mercurial`等你需要将此文件置于项目目录之外。将此密钥暴露给全世界可能会产生安全问题。

​	自从我在Linux机器上工作后，我在目录`/etc/.`下创建文件

```bash
nano /etc/config.json
```

​	现在，在一台开发服务器上，你只需要去设置密钥。所以，文件的内容是：`SECURITY_KEY`

```json
{
    "SECRET_KEY": "my security key",
}
```

对于生产服务器，这将包含一个额外的密钥，`PROD`。

```json
{
    "SECRET_KEY": "my security key",
    "PROD": "True"
}
```

**注意**：你可以使用任何其他方法来存储和其他其他值。你可以使用环境变量或其他你喜欢的方法。这只是方式中的一个。只需要相应地`__init__.py` 文件中获取这些值即可。r如果我们遵循JSON格式，让我们看看如何做到这一点`SECURITY_KEY`

目前情况下，我们的目录结构应如下所示：

```
.
    │   manage.py
    ├───data
    └───django-project
        ├───settings
        │   ├── base.py
        │   ├── dev.py
        │   ├── __init__.py
        │   ├── prod.py
        │   urls.py
        │   wsgi.py
```



`__init__.py`文件将类似于：

```python
import os
import json

CONFIG_FILE = '/etc/config.json'

try:
    with open(CONFIG_FILE) as config_file:
        config = json.load(config_file)
        config['PROD']
    from .prod import *


except KeyError:
    from .dev import *

SECRET_KEY = config['SECRET_KEY']
```

注意：如果你使用了任何其他方式来存储，则只需要相应地修改此文件即可`SECURITY_KEY`



就是这样，你干干已经分离了你的生产和开发环境的设置。现在，你不需要在每次将代码推送到开发环境时都编辑这些文件。只需要推动代码和经验。

作者:Nirvana

---

这只是帮助你提高工作效率，这样你就可以节省你的时间来处理其他问题。我们有更多关于Django技术的文档，你可能会觉得有用，一定要去看看

直到我们在另一篇博客文章中再次见面，保持学习。

> Translater