# Python 和 REST APIs：与Web服务交互


> 翻译：  
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