## 快速开始fastapi

### 依赖

Python 3.6 及更高版本

FastAPI 站在以下巨人的肩膀之上：

- Starlette 负责 web 部分。
- Pydantic 负责数据部分。

### 示例


- 创建一个`main.py` 文件

```python

from typing import Union

from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: Union[str, None] = None):
    return {"item_id": item_id, "q": q}

```

通过以下命令允许服务器:

```bash

$ uvicorn main:app --reload

INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [28720]
INFO:     Started server process [28722]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```


你已经创建了一个具有以下功能的 API：

- 通过 路径 / 和 /items/{item_id} 接受 HTTP 请求。
- 以上 路径 都接受 GET 操作（也被称为 HTTP 方法）。
- /items/{item_id} 路径 有一个 路径参数 item_id 并且应该为 int 类型。
- /items/{item_id} 路径 有一个可选的 str 类型的 查询参数 q。


### 交互式 API 文档
现在访问 http://127.0.0.1:8000/docs。

你会看到自动生成的交互式 API 文档（由 Swagger UI生成）：

![](https://fastapi.tiangolo.com/img/index/index-01-swagger-ui-simple.png)

### 可选的 API 文档

访问 http://127.0.0.1:8000/redoc。

你会看到另一个自动生成的文档（由 ReDoc 生成）：

![](https://fastapi.tiangolo.com/img/index/index-02-redoc-simple.png)


### 示例升级

现在修改 main.py 文件来从 PUT 请求中接收请求体。

我们借助 Pydantic 来使用标准的 Python 类型声明请求体。

```python

from typing import Union

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    price: float
    is_offer: Union[bool, None] = None


@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: Union[str, None] = None):
    return {"item_id": item_id, "q": q}


@app.put("/items/{item_id}")
def update_item(item_id: int, item: Item):
    return {"item_name": item.name, "item_id": item_id}
```


服务器将会自动重载（因为在上面的步骤中你向 uvicorn 命令添加了 --reload 选项）。

### 交互式 API 文档升级

访问 http://127.0.0.1:8000/docs。
![](https://fastapi.tiangolo.com/img/index/index-03-swagger-02.png)


### 可选文档升级
访问 http://127.0.0.1:8000/redoc。
![](https://fastapi.tiangolo.com/img/index/index-06-redoc-02.png)

### 总结

总的来说，你就像声明函数的参数类型一样只声明了一次请求参数、请求体等的类型。

你使用了标准的现代 Python 类型来完成声明。

你不需要去学习新的语法、了解特定库的方法或类，等等。

只需要使用标准的 Python 3.6 及更高版本。

举个例子，比如声明 int 类型：

`item_id: int`


或者一个更复杂的 Item 模型：


`item: Item`

在进行一次声明之后，你将获得：

- 编辑器支持，包括：
    - 自动补全
    - 类型检查
- 数据校验：
    - 在校验失败时自动生成清晰的错误信息
    - 对多层嵌套的 JSON 对象依然执行校验
- 转换 来自网络请求的输入数据为 Python 数据类型。包括以下数据：
    - JSON
    - 路径参数
    - 查询参数
    - Cookies
    - 请求头
    - 表单
    - 文件
- 转换 输出的数据：转换 Python 数据类型为供网络传输的 JSON 数据：
    - 转换 Python 基础类型 （str、 int、 float、 bool、 list 等）
    - datetime 对象
    - UUID 对象
    - 数据库模型
    - ......以及更多其他类型
- 自动生成的交互式 API 文档，包括两种可选的用户界面：
    - Swagger UI
    - ReDoc