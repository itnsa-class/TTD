# Python 和 REST APIs：与Web服务交互


> 翻译： [Carllllll](https://github.com/F-Ar1es)  
> 原文：Python and REST APIs: Interacting With Web Services   
> 链接：https://realpython.com/api-integration-in-python/   


&emsp;&emsp;网络上有大量的数据可用。许多 `Web` 服务(如 YouTube 和 GitHub)通过应用程序编程接口( `application programming interface` , 简称 `API` ), 使其数据可供第三方应用程序访问。构建 `API` 的最流行的方法之一是 `REST` 体系结构风格。 `Python` 提供了一些很棒的工具，不仅可以从 `REST API` 获取数据，还可以构建自己的 `Python REST API` 。

**通过这个教程，你将学习：**

* `REST` 架构是什么
* `REST API` 如何提供对 `Web` 数据的访问
* 如何使用 `requests` 库使用 `REST API` 中的数据
* 构建 `REST API` 需要执行哪些步骤
* 哪些流行的 `Python` 工具用于构建 `REST API`

通过使用 `Python` 和 `REST APIs` , 你可以检索，解析，更新，和操作你感兴趣的任何 `web` 服务提供的数据。

> 免费资源：[点击这里下载“REST API示例”指南](https://realpython.com/api-integration-in-python/)，并获得 `Python + REST API` 原则的实际介绍和可操作的示例。


<!-- Others' translation -->

# REST 和 Python：使用 API

&emsp;&emsp;要写代码跟 `REST API` 进行交互，很多 `Python` 开发者使用 `requests`去发送 `HTTP` 请求。这个库提取出创建 `HTTP` 请求的复杂性。它是为数不多值得当作标准库一部分来对待的项目之一。

&emsp;&emsp;要开始使用 `requests`，你首先需要安装它，你可以使用 `pip` 来安装它

```bash
$ python -m pip install requests
```

&emsp;&emsp;现在，你已经安装好 `requests`，可以开始发送 `HTTP` 请求了。



## GET

&emsp;&emsp;当你在使用 `REST API` 的工作时，`Get` 将会是你最常用的 `HTTP` 方法之一。这个方法让你能够在给定的 `API` 中检索资源。 `GET` 是一个只读操作，所以你不应该使用它来修改一个已经存在的资源。

&emsp;&emsp;在这个部分中要测试 `GET` 和其他的方法，那你将使用一个叫 [JSONPlaceholder](https://jsonplaceholder.typicode.com/) 的服务。这个免费的服务提供假的 `API` 终端，这些终端将发回 `requests` 可以处理的应答。

&emsp;&emsp;要把这个测试出来，打开 [Python REPL](https://realpython.com/interacting-with-python/) 然后运行以下代码区发送一个 `GET` 请求到 `JSONPlaceholder` 的终端：

```python
>>> import requests
>>> api_url = "https://jsonplaceholder.typicode.com/todos/1"
>>> response = requests.get(api_url)
>>> response.json()
{'userId': 1, 'id': 1, 'title': 'delectus aut autem', 'completed': False}
```

&emsp;&emsp;这段代码调用 `requests.get()` 发送一个 `GET` 请求到 `/todos/1`，`/todos/1`以 `ID1` 的 `todo` 项目进行相应。然后你可以在 `response` 对象中调用 `.json()` 来查看API返回的数据。

&emsp;&emsp;响应的数据会被格式化为 [JSON](https://www.json.org/json-en.html)（格式），储存为像[Python dictionary](https://realpython.com/python-dicts/)一样的键值对。这是一种非常受欢迎的数据并且是大多数 `REST API` 的交互格式。

&emsp;&emsp;除了在 `API` 中查看 `JSON` 数据，你也可以在 `resopnse` 中查看其他的东西：

```python
>>> response.status_code
200

>>> response.headers["Content-Type"]
'application/json; charset=utf-8'
```

&emsp;&emsp;像这样，你通过 `response.status_code` 去查看 `HTTP` 状态码，你也可以通过 `response.headers` 查看返回值的 `HTTP头部信息`。这个字典包含了相应的元数据，例如这个响应的 `Content-Type` 。

