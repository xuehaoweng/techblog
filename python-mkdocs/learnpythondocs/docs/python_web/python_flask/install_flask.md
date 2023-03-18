# 安装

`pip install Flask`

## 依赖组件

Werkzeug 用于实现 WSGI，应用程序和服务器之间的标准python接口。

Jinja 是呈现应用程序服务的页面的模板语言。

MarkupSafe 与 Jinja 共用，在渲染页面时用于避免不可信的输入，防止注入攻击。

ItsDangerous 保证数据完整性的安全标志数据，用于保护Flask 的 session cookie.

Click 是用于编写命令行应用程序的框架。用于提供 flask 命令，并允许添加自定义 管理命令。

## 虚拟环境

`venv or conda env`