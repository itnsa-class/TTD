# Python 和 REST APIs：与Web服务交互

> 翻译： [Carllllll](https://github.com/F-Ar1es)  
> 原文：Python and REST APIs: Interacting With Web Services
> 链接：<https://realpython.com/api-integration-in-python/>

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

## REST 和 Python：使用 API

&emsp;&emsp;要写代码跟 `REST API` 进行交互，很多 `Python` 开发者使用 `requests` 去发送 `HTTP` 请求。这个库提取出创建 `HTTP` 请求的复杂性。它是为数不多值得当作标准库一部分来对待的项目之一。

&emsp;&emsp;要开始使用 `requests`，你首先需要安装它，你可以使用 `pip` 来安装它

```bash
python -m pip install requests
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

&emsp;&emsp;这段代码调用 `requests.get()` 发送一个 `GET` 请求到 `/todos/1`，`/todos/1` 以 `ID1` 的 `todo` 项目进行相应。然后你可以在 `response` 对象中调用 `.json()` 来查看API返回的数据。

&emsp;&emsp;响应的数据会被格式化为 [JSON](https://www.json.org/json-en.html)（格式），储存为像[Python dictionary](https://realpython.com/python-dicts/)一样的键值对。这是一种非常受欢迎的数据并且是大多数 `REST API` 的交互格式。

&emsp;&emsp;除了在 `API` 中查看 `JSON` 数据，你也可以在 `resopnse` 中查看其他的东西：

```python
>>> response.status_code
200

>>> response.headers["Content-Type"]
'application/json; charset=utf-8'
```

&emsp;&emsp;像这样，你通过 `response.status_code` 去查看 `HTTP` 状态码，你也可以通过 `response.headers` 查看返回值的 `HTTP头部信息`。这个字典包含了相应的元数据，例如这个响应的 `Content-Type`。  

## POST

&emsp;&emsp;现在，（让我们看看）你才能通过使用 `requests` 库种 `POST` 数据的方式去创建一个新资源。你将再次使用 `JSONPlaceholder` ，不过这次你将在请求中使用 `JSON` 数据。你将送的内容如下所示。

```JSON
{
"userId": 1,
"title": "Buy milk",
"completed": false
}
```

&emsp;&emsp;这个 `JSON` 包含一个新的 `todo` 项信息。回到 `Python REPL` ，运行一下代码去创建一个新的 `todo` 项：

```python
>>> import requests
>>> api_url = "https://jsonplaceholder.typicode.com/todos"
>>> todo = {"userId": 1, "title": "Buy milk", "completed": False}
>>> response = requests.post(api_url, json=todo)
>>> response.json()
{'userId': 1, 'title': 'Buy milk', 'completed': False, 'id': 201}

>>> response.status_code
20
```

&emsp;&emsp;像这样，你在系统中了调用 `requests.post()` 去创建一个新的 `todo` 项。

&emsp;&emsp;首先，你创建了一个包含了 `todo` 项数据的字典。然后你将这个字典传递给 `requests.post()`的 `JSON` 关键字参数。当你这么做时，`requests.post()` 自动将 `HTTP`请求头部 `Content-Type` 设置为 `application/json` 。它也将 `todo` 项序列化成 `JSON` 字符串，同时 `requests.post()` 将这个 `JSON`字符串附加到请求的主体中。

&emsp;&emsp;如果你不使用 `JSON` 关键字参数提供到 `JSON` 数据，则你需要去设置相应的 `Content-Type` 并手动序列化 `JSON`。下面是与前一个代码块等效的版本：

```python
>>> import requests
>>> import json
>>> api_url = "https://jsonplaceholder.typicode.com/todos"
>>> todo = {"userId": 1, "title": "Buy milk", "completed": False}
>>> headers = {"Content-Type":"application/json"}
>>> response = requests.post(api_url, data=json.dumps(todo), headers=headers)
>>> response.json()
{'userId': 1, 'title': 'Buy milk', 'completed': False, 'id': 201}

>>> response.status_code
201
```

&emsp;&emsp;在这个代码块中，你添加了一个 `headers` 字典其中包含了设置为`Application/Json`的 `Content-Type`头部。这告诉 `REST API` 你使用请求发送 `JSON`数据。然后调用 `requests.post()`，不过不是将 `todo` 项传递给 `JSON`参数，你首先调用 `json.dumps(todo)` 去序列化它。在序列化之后，将其传递给 `data` 关键字参数。`data` 参数告诉 `requests` 这个请求中包含哪些数据。同时，你也将 `headers` 字典传递到 `requests.post()` 去手动设置 `HTTP` 头部信息。

&emsp;&emsp;当你像这样调用 `requests.post()`，这会像前一个代码块一样得到相同的结果，不过这为你提供了更好的请求控制。

> **注意**：[json.dumps](https://docs.python.org/3/library/json.html#json.dumps) 来自标准库的 [json](https://docs.python.org/3/library/json.html) 包。这个包提供在 [Python中使用JSON](https://realpython.com/python-json/) 的有用的方法

&emsp;&emsp;在 `API` 应答后，你调用 `response.json()` 来查看 `JSON`。`JSON` 包含一个为新的 `todo` 生成的 `id`。201状态码告诉你一个新的资源被创建出来了。

## PUT

&emsp;&emsp;除了 `GET` 和 `POST` ，`requests` 还提供你将对 `REST API` 进行操作的所有 `HTTP` 方法。在下面的代码中，发送一个 `PUT` 请求向存在的 `todo` 更新数据。所有通过 `PUT`请求发送的数据将完全替代 `todo`中存在的值

&emsp;&emsp;你将使用与 `GET` 和 `POST`  相同的 `JSONNPlaceholder` 终端，不过这次你将在 `URL` 的尾部添加 `10` 。这将告诉 `REST API` 你想要更新的是哪一个 `todo`

```python
>>> import requests
>>> api_url = "https://jsonplaceholder.typicode.com/todos/10"
>>> response = requests.get(api_url)
>>> response.json()
{'userId': 1, 'id': 10, 'title': 'illo est ... aut', 'completed': True}

>>> todo = {"userId": 1, "title": "Wash car", "completed": True}
>>> response = requests.put(api_url, json=todo)
>>> response.json()
{'userId': 1, 'title': 'Wash car', 'completed': True, 'id': 10}

>>> response.status_code
200
```

&emsp;&emsp;像这样，你首先调用 `requests.get()` 去查看现有 `todo` 中的内容。接下来，你调用 `requests.put()` 通过新的 `JSON` 数据去替代存在 `to-do` 中的值。当你调用 `response.json()` 时可以看到新的值。一个成功的 `PUT` 请求会返回 `200` 而不是 `201` 因为你没有创建一个新资源而是更新了存在的资源。

## PATCH

&emsp;&emsp;接下来，你将使用 `requests.patch()` 去修改现有 `todo` 上指定资源的值。`PATCH` 不同于 `PUT` ，（具体体现在）它不会完全替代存在的资源。它只会修改在随请求发送的 `JSON` 中的值集

&emsp;&emsp;你将使用上个例子中相同的 `todo` 去尝试 `requests.patch()`。以下是当前的值：

```python
{'userId': 1, 'title': 'Wash car', 'completed': True, 'id': 10}
```

&emsp;&emsp;现在你可以通过一个新的值更新 `title`：

```python
>>> import requests
>>> api_url = "https://jsonplaceholder.typicode.com/todos/10"
>>> todo = {"title": "Mow lawn"}
>>> response = requests.patch(api_url, json=todo)
>>> response.json()
{'userId': 1, 'id': 10, 'title': 'Mow lawn', 'completed': True}

>>> response.status_code
200
```

&emsp;&emsp;当你调用 `response.json()` 时，你可以看到 `title` 已经被升级为 `Mow lawn`

## DELETE

&emsp;&emsp;最后但也相同重要的，如果你想要完全删除一个资源，你需要使用 `DELETE`。这些是删除一个 `todo` 的代码：

```python
>>> import requests
>>> api_url = "https://jsonplaceholder.typicode.com/todos/10"
>>> response = requests.delete(api_url)
>>> response.json()
{}

>>> response.status_code
200
```

&emsp;&emsp;你调用 `requests.delete()` 的 `API URL` 包含你想要删的 `todo` 的 `ID` 。这会发送一个 `DELETE` 请求到 `REST API` ，它将会删除相应的资源。在删除资源后， `API` 返回一个空的 `JSON` 对象，表明资源已经被删除。

&emsp;&emsp;`requests` 库是使用 `REST API` 的一个非常好的工具，并且也是你 `Python` ”工具腰带“上不可缺少的一部分。在下一个环节，你将改变档位并且思考构建一个 `REST API` 需要什么。
