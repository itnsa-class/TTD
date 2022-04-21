# Python 和 REST APIs：与Web服务交互

> 翻译：motian
> 原文：Python and REST APIs: Interacting With Web Services
> 链接：https://realpython.com/api-integration-in-python/

网络上有大量的数据可用。许多 `Web` 服务(如 YouTube 和 GitHub)通过应用程序编程接口( `application programming interface` , 简称 `API` ), 使其数据可供第三方应用程序访问。构建 `API` 的最流行的方法之一是 `REST` 体系结构风格。 `Python` 提供了一些很棒的工具，不仅可以从 `REST API` 获取数据，还可以构建自己的 `Python REST API` 。

**通过这个教程，你将学习：**

* `REST` 架构是什么
* `REST API` 如何提供对 `Web` 数据的访问
* 如何使用 `requests` 库使用 `REST API` 中的数据
* 构建 `REST API` 需要执行哪些步骤
* 哪些流行的 `Python` 工具用于构建 `REST API`

通过使用 `Python` 和 `REST APIs` , 你可以检索，解析，更新，和操作你感兴趣的任何 `web` 服务提供的数据。

> 免费资源：[点击这里下载“REST API示例”指南](https://realpython.com/api-integration-in-python/)，并获得 `Python + REST API` 原则的实际介绍和可操作的示例。

REST 体系是一个什么东西。


## REST 和 Python：构建 API

REST API 设计是一个包含许多层次且庞大的主题。和很多其他技术一样，对于构建 API 的最佳方法有很多的意见。在本节中，您将了解构建 API 时要遵循的一些步骤和建议。

### 识别资源

构建 `REST API` 时，您将采取的第一步是确定 `API` 需要管理的资源。通常将这些资源称作plural nouns，如 `customers`、`events`或 `transactions`。当您在 Web 服务中识别不同的资源时，您将构建一个nouns名列表来描述用户可以在 API 中管理的不同数据。

执行此操作时，请务必考虑任何嵌套的资源。例如，`customers`可能有 `sales`，或 `events`可能包含 `guests`。当您定义 API 端点时，建立这些资源层次结构将有所帮助。

### 定义您的端点

一旦您识别了 Web 服务中的资源，您将希望使用这些资源来定义 API 端点。`transactions`以下是您可能在支付处理服务的 API 中找到的资源的一些示例端点：

| HTTP 方式  | API 端点                           | 描述                      |
| ---------- | ---------------------------------- | ------------------------- |
| `GET`    | `/transactions`                  | 获取 transactions 列表.   |
| `GET`    | `/transactions/<transaction_id>` | 获取单个 transaction.     |
| `POST`   | `/transactions`                  | 创建一个新的 transaction. |
| `PUT`    | `/transactions/<transaction_id>` | 更新一个 transaction.     |
| `PATCH`  | `/transactions/<transaction_id>` | 部分更新 transaction.     |
| `DELETE` | `/transactions/<transaction_id>` | 删除一个 transaction.     |

这六个端点涵盖了您 `transactions`在 Web 服务中创建、读取、更新和删除所需的所有操作。根据用户可以使用 API 执行的操作，Web 服务中的每个资源都将具有类似的端点列表。


 **注意：** 端点不应包含verbs。相反，您应该选择适当的 HTTP 方法来传达端点的操作。例如，下面的端点包含一个不需要的verbs：

```
GET /getTransactions
```

此处，`get`在不需要时包含在端点中。HTTP 方法 `GET`已经通过指示动作为端点提供了语义含义。您可以 `get`从端点中删除：

```
GET /transactions
```

这个端点只包含一个plural noun，并且 HTTP 方法 `GET`传达了这个动作。


现在看一个嵌套资源的端点示例。在这里，您将看到 `guests`嵌套在 `events`资源下的端点：

| HTTP 方法  | API 端点                                 | 描述                |
| ---------- | ---------------------------------------- | ------------------- |
| `GET`    | `/events/<event_id>/guests`            | 获取 guests  列表.  |
| `GET`    | `/events/<event_id>/guests/<guest_id>` | 获取单个 guest.     |
| `POST`   | `/events/<event_id>/guests`            | 创建一个新的 guest. |
| `PUT`    | `/events/<event_id>/guests/<guest_id>` | 更新一个 guest.     |
| `PATCH`  | `/events/<event_id>/guests/<guest_id>` | 部分更新 guest.     |
| `DELETE` | `/events/<event_id>/guests/<guest_id>` | 删除一个 guest.     |

使用这些端点，您可以管理 `guests`系统中的特定事件。

这不是为嵌套资源定义端点的唯一方法。有些人喜欢使用[查询字符串](https://en.wikipedia.org/wiki/Query_string)来访问嵌套资源。查询字符串允许您通过 HTTP 请求发送其他参数。在以下端点中，您附加一个查询字符串以获取 `guests`特定的 `event_id`：

HTTP

```
GET /guests?event_id=23
```

此端点将过滤掉任何 `guests`不引用给定 `event_id`. 与 API 设计中的许多事情一样，您需要决定哪种方法最适合您的 Web 服务。


 **注意：** 您的 REST API 不太可能在 Web 服务的整个生命周期中保持不变。资源会发生变化，您需要更新端点以查看这些变化。这就是**API 版本控制**的用武之地。API 版本控制允许您修改 API，而不必担心破坏现有集成。

有多种版本控制策略。选择正确的选项取决于 API 的要求。以下是一些最流行的 API 版本控制选项：

* [URI 版本控制](https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design#uri-versioning)
* [HTTP 标头版本控制](https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design#header-versioning)
* [查询字符串版本控制](https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design#query-string-versioning)
* [媒体类型版本控制](https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design#media-type-versioning)

无论您选择何种策略，对 API 进行版本控制都是确保它能够适应不断变化的需求同时支持现有用户的重要步骤。

现在您已经介绍了端点，在下一节中，您将了解一些用于在 REST API 中格式化数据的选项。


### 选择您的数据交换格式

格式化 Web 服务数据的两个流行选项是[XML](https://en.wikipedia.org/wiki/XML)和 JSON。[传统上，XML 在SOAP](https://en.wikipedia.org/wiki/SOAP) API中非常流行，但 JSON 在 REST API 中更流行。`book`要比较这两者，请查看格式为 XML 和 JSON的示例资源。

这是格式化为 XML 的书：


```XML
<?xml version="1.0" encoding="UTF-8" ?>
<book>
    <title>Python Basics</title>
    <page_count>635</page_count>
    <pub_date>2021-03-16</pub_date>
    <authors>
        <author>
            <name>David Amos</name>
        </author>
        <author>
            <name>Joanna Jablonski</name>
        </author>
        <author>
            <name>Dan Bader</name>
        </author>
        <author>
            <name>Fletcher Heisler</name>
        </author>
    </authors>
    <isbn13>978-1775093329</isbn13>
    <genre>Education</genre>
</book>
```


XML 使用一系列**元素**对数据进行编码。每个元素都有一个开始和结束标签，数据介于两者之间。元素可以嵌套在其他元素中。您可以在上面看到，其中有几个 `<author>`标签嵌套在 `<authors>`.

现在，看一下 `book`JSON 中的相同内容：

```json
{
    "title": "Python Basics",
    "page_count": 635,
    "pub_date": "2021-03-16",
    "authors": [
        {"name": "David Amos"},
        {"name": "Joanna Jablonski"},
        {"name": "Dan Bader"},
        {"name": "Fletcher Heisler"}
    ],
    "isbn13": "978-1775093329",
    "genre": "Education"
}
```

JSON 将数据存储在类似于 Python 字典的键值对中。与 XML 一样，JSON 支持将数据嵌套到任何级别，因此您可以对复杂数据进行建模。

JSON 和 XML 在本质上都不比另一个更好，但是 REST API 开发人员偏爱 JSON。[当您将 REST API 与React](https://reactjs.org/)或[Vue](https://vuejs.org/)等前端框架配对时尤其如此。
