# 快速开始Flask

```python

from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"

```

那么，这段代码做了什么？

1. 首先我们进口了 Flask 班级。这个类的一个实例就是我们的WSGI应用程序。

2. 接下来我们创建这个类的一个实例。第一个参数是应用程序的模块或包的名称。 __name__ 是一种方便快捷的方法，适用于大多数情况。这是必需的，这样Flask就知道在哪里可以找到模板和静态文件等资源。

3. 然后我们使用 route() 装饰器来告诉 Flask 什么样的 URL可以触发函数 。

4. 函数返回我们希望在用户浏览器中显示的消息。默认的内容类型是HTML，因此字符串中的HTML将由浏览器呈现。

**注意** ：另存为 hello.py 或者类似的东西。确保不要调用您的应用程序 flask.py 因为这会和flask库本身发生冲突。

要运行应用程序，请使用 flask 命令或 python -m flask . 在执行此操作之前，您需要通过导出 FLASK_APP 环境变量：

CMD：
```bash

export FLASK_APP=hello.py
flask run
即可在默认5000端口访问
 * Running on http://127.0.0.1:5000/


```

类似下图:

![](http://rregf9gjs.hd-bkt.clouddn.com/Snipaste_2023-03-18_16-47-59.png)

如果想外部访问则需要指定ip

` flask run --host=0.0.0.0 `

![](http://rregf9gjs.hd-bkt.clouddn.com/Snipaste_2023-03-18_16-52-50.png)

这将启动一个非常简单的内置服务器，这对于测试来说已经足够好了，但可能不是您想要在生产中使用的服务器。有关部署选项 [常见Flask项目部署选择](https://www.osgeo.cn/flask/deploying/index.html)


## 如果服务器不能启动，该怎么办
万一 python -m flask 失败或 flask 不存在，有多种原因可能是这种情况。首先，您需要查看错误消息。

## 旧版Flask
早于0.11的Flask版本以前有不同的方法来启动应用程序。简而言之 flask 命令不存在，也不存在 python -m flask . 在这种情况下，你有两个选择：要么升级到更新的Flask版本，要么看看 开发服务器 查看运行服务器的替代方法。

## 导入名称无效
这个 FLASK_APP 环境变量是要导入的模块的名称, 运行 flask run 命令就会导入这个模块。 如果该模块的名称不正确，您将在启动时收到导入错误。如果调试模式打开的情况下，会在运行到应用开始的时候出现导入错误。出错信息会告诉你尝试导入哪个模块时出错，为什么会出错。

最常见的原因是打字错误，或者因为你没有真正创建一个 app 对象。

## 调试模式
这个 flask run 命令不仅仅可以启动开发服务器。通过启用调试模式，如果代码发生更改，服务器将自动重新加载，如果请求期间发生错误，服务器将在浏览器中显示交互式调试器。

![](https://www.osgeo.cn/flask/_images/debugger.png)

## HTML逃逸

当返回HTML（Flask中的默认响应类型）时，必须对输出中呈现的任何用户提供的值进行转义，以防止注入攻击。用Jinja呈现的HTML模板（稍后介绍）将自动执行此操作。

escape() ，可以手动使用。为了简洁起见，在大多数示例中都省略了它，但是您应该始终了解如何使用不可信的数据。

```python

from markupsafe import escape

@app.route("/<name>")
def hello(name):
    return f"Hello, {escape(name)}!"

```

如果用户成功提交了名称 `<script>alert("bad")</script>` ，转义使其呈现为文本，而不是在用户浏览器中运行脚本。
<name> 在路由中，从URL捕获一个值并将其传递给view函数。这些变量规则解释如下。

## 路由
现代Web应用都使用有意义的URL，这样有易于人们辨识记忆。用户都喜欢可以不访问索引页就可以直接访问想要的那个页面。

使用 route() 装饰器来把函数绑定到对应的 URL:

```python

@app.route('/')
def index():
    return 'Index Page'

@app.route('/hello')
def hello():
    return 'Hello, World'
```

但是能做的不仅仅是这些！你可以使URL的某些部分成为动态的，并将多个规则附加到一个函数。

## 变量规则
通过把 URL 的一部分标记为 <variable_name> 就可以在 URL 中添加变量。标记的部分会作为关键字参数传递给函数。通过使用 <converter:variable_name> ，可以选择性的加上一个转换器，为变量指定规则。请看下面的例子:

```python

from markupsafe import escape

@app.route('/user/<username>')
def show_user_profile(username):
    # show the user profile for that user
    return f'User {username}'

@app.route('/post/<int:post_id>')
def show_post(post_id):
    # show the post with the given id, the id is an integer
    return f'Post {post_id}'

@app.route('/path/<path:subpath>')
def show_subpath(subpath):
    # show the subpath after /path/
    return f'Subpath {subpath}'

```

## 唯一的URL/重定向行为
以下两条规则的不同之处在于是否使用尾部的斜杠。:
``` python

@app.route('/projects/')
def projects():
    return 'The project page'

@app.route('/about')
def about():
    return 'The about page'
```

## URL构建
url_for() 函数用于构建指定函数的 URL。它把函数名称作为第一个参数。它可以接受任意个关键字参数，每个关键字参数对应 URL 中的变量。未知变量 将添加到 URL 中作为查询参数。

为什么要使用URL反转函数生成URL，而非在模板中硬编码？

- 反转通常比硬编码URL更具描述性。

- 你可以只在一个地方改变 URL ，而不用到处乱找。

- URL构建透明地处理特殊字符的转义。

- 生成的路径总是绝对的，可以避免相对路径产生副作用。

- 如果你的应用是放在 URL 根路径之外的地方（如在 /myapplication 中，不在 / 中）， url_for() 会为你妥善处理

例如，这里我们使用 test_request_context() 方法来尝试使用 url_for() 。 test_request_context() 告诉 Flask 正在处理一个请求，而实际上也许我们正处在交互 Python shell 之中， 并没有真正的请求。参见 本地环境 。

```python

from flask import url_for

@app.route('/')
def index():
    return 'index'

@app.route('/login')
def login():
    return 'login'

@app.route('/user/<username>')
def profile(username):
    return f'{username}\'s profile'

with app.test_request_context():
    print(url_for('index'))
    print(url_for('login'))
    print(url_for('login', next='/'))
    print(url_for('profile', username='John Doe'))

```


``` python
/
/login
/login?next=/
/user/John%20Doe

```

## HTTP方法
Web应用程序在访问URL时使用不同的HTTP方法。在使用flask时，应该熟悉HTTP方法。默认情况下，一个路由只回应 GET 请求。可以使用 route() 装饰器的 methods 参数来处理不同的 HTTP 方法:

```python

from flask import request

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        return do_the_login()
    else:
        return show_the_login_form()

```
如果当前使用了 GET 方法， Flask 会自动添加 HEAD 方法支持，并且同时还会 按照 HTTP RFC 来处理 HEAD 请求。同样， OPTIONS 也会自动实现。

## 静态文件
动态Web应用程序还需要静态文件。通常是css和javascript文件。理想情况下，你的Web服务器已经配置好了为你提供静态文件服务，但在开发期间，Flask也可以这样做。只要在你的包或模块旁边创建一个名为 static 的文件夹就行了。 静态文件位于应用的 /static 中。

使用特定的 'static' 端点就可以生成相应的 URL

` url_for('static', filename='style.css') `

这个静态文件在文件系统中的位置应该是 static/style.css 。

## 渲染模板
从Python中生成HTML并不有趣，实际上相当麻烦，因为您必须自己进行HTML转义以保证应用程序的安全。因为这个瓶子配置了 Jinja2 自动为您提供模板引擎。

使用 render_template() 方法可以渲染模板，你只要提供模板名称和需要 作为参数传递给模板的变量就行了。下面是一个简单的模板渲染例子:


```python

from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
```

Flask 会在 templates 文件夹内寻找模板。因此，如果你的应用是一个模块， 那么模板文件夹应该在模块旁边；如果是一个包，那么就应该在包里面：

情形1: 一个模板：

```python

/application.py
/templates
    /hello.html

```

情形2: 一个包：

```python

/application
    /__init__.py
    /templates
        /hello.html
```

对于模板，您可以使用jinja2模板的全部功能。去见官员 Jinja2 Template Documentation 更多信息。

下面是一个示例模板：
```html

<!doctype html>
<title>Hello from Flask</title>
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello, World!</h1>
{% endif %}

```
在模板内部可以和访问 get_flashed_messages() 函数一样访问 request 、 session 和 g [1] 对象。

如果使用继承，模板尤其有用。如果你想知道这是怎么回事，看 模板继承 . 基本上，模板继承可以在每个页面上保留某些元素（如页眉、导航和页脚）。

自动转义已启用，因此如果 name 包含HTML，将自动转义。如果您可以信任一个变量，并且知道它将是安全的HTML（例如，因为它来自一个将wiki标记转换为HTML的模块），则可以使用 Markup 类或通过使用 |safe 在模板中筛选。有关更多示例，请参阅Jinja 2文档。

## 访问请求数据
对于 web 应用来说对客户端向服务器发送的数据作出响应很重要。在 Flask 中由全局 对象 request 来提供请求信息。如果你有一些 Python 基础，那么 可能会奇怪：既然这个对象是全局的，怎么还能保持线程安全？答案是本地环境：

## 环境局部变量
<!-- 内幕消息
如果你想了解工作原理和如何使用局部环境进行测试，那么请阅读本节， 否则可以跳过本节。 -->

某些对象在 Flask 中是全局对象，但不是通常意义下的全局对象。这些对象实际上是 特定环境下局部对象的代理。虽然很拗口，但还是很容易理解的。

设想现在处于处理线程的环境中。一个请求传入，服务器决定生成一个新线程（或者别的什么东西，这个底层的东西能够处理包括线程在内的并发系统）。当 Flask 开始其内部请求处理时会把当前线程作为活动环境，并把当前应用和 WSGI 环境绑定到这个环境（线程）上。它以一种聪明的方式使得一个应用可以在不中断的情况下调用另一个应用上。

这对你意味着什么？基本上你可以完全不必理会。这个只有在做类似单元测试时才有用。在测试时会遇到由于没有请求对象而导致依赖于请求的代码会突然崩溃的情况。解决方案是自己创建一个请求对象并绑定到环境。最简单的单元测试解决方案是使用 test_request_context() 环境管理器。通过使用 with 声明可以绑定一个测试请求，以便于交互。例如:

```python

from flask import request

with app.test_request_context('/hello', method='POST'):
    # now you can do something with the request until the
    # end of the with block, such as basic assertions:
    assert request.path == '/hello'
    assert request.method == 'POST'
```

另一种可能性是将整个WSGi环境传递给 request_context() 方法：

```python
with app.request_context(environ):
    assert request.method == 'POST'
```

请求对象
API章节对请求对象做了详尽阐述（参见request)，因此这里不会赘述。此处宽泛介绍一些最常用的操作。首先从Flask模块里导入它：

`from flask import request`
通过使用 method 属性可以操作当前请求方法，通过使用 form 属性处理表单数据（在 POST 或者 PUT 请求中传输的数据）。以下是使用上述两个属性的例子:

```python
@app.route('/login', methods=['POST', 'GET'])
def login():
    error = None
    if request.method == 'POST':
        if valid_login(request.form['username'],
                       request.form['password']):
            return log_the_user_in(request.form['username'])
        else:
            error = 'Invalid username/password'
    # the code below is executed if the request method
    # was GET or the credentials were invalid
    return render_template('login.html', error=error)
```
当 form 属性中不存在这个键时会发生什么？会引发一个 KeyError 的异常。 如果你不像捕捉一个标准错误一样捕捉 KeyError，那么会显示一个 HTTP 400 Bad Request 错误页面。因此，多数情况下你不必处理这个问题。

你可以使用args 属性来访问在URL中提交的参数（ ?key=value ）：

`searchword = request.args.get('key', '')`

我们建议使用 get 或通过捕捉 KeyError 来访问 URL 参数。因为在这种情况下，用户可能会更改URL导致一个400错误的请求页面，这样会影响用户体验。

要获得请求对象的方法和属性的完整列表，请参阅Request文档。

## 文件上传
你可以用flask轻松处理上传的文件。只要确保不要忘记在HTM里设置 enctype="multipart/form-data" 属性，否则浏览器根本不会发送文件。

上载的文件存储在内存中或文件系统上的临时位置。您可以通过查看 files 请求对象的属性。每个上载的文件都存储在该字典中。它的行为就像一条标准的 Python file 对象，但它也有一个 save() 方法，该方法允许您将该文件存储在服务器的文件系统中。下面是一个简单的例子，展示了它的工作原理：

```python
from flask import request

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        f = request.files['the_file']
        f.save('/var/www/uploads/uploaded_file.txt')
    ...

```
如果您想知道文件在上传到应用程序之前是如何在客户机上命名的，可以访问 filename 属性。但是请记住，这个价值观是可以伪造的，所以永远不要相信这个价值观。如果要使用客户端的文件名在服务器上存储文件，请通过 secure_filename() Werkzeug为您提供的功能：

```python
from werkzeug.utils import secure_filename

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        file = request.files['the_file']
        file.save(f"/var/www/uploads/{secure_filename(f.filename)}")
    ...
```
有关更好的示例，请参见 上传文件 .

## Cookies
可以使用 cookies 属性来访问 cookies 。可以使用响应对象的 set_cookie 方法来设置 cookies 。请求对象的 cookies 属性是一个包含了客户端传输的所有 cookies 的字典。在 Flask 中，如果使用会话，那么就不要直接使用 cookies ，请参考会话一节。

**读取cookie**:：

```python

from flask import request

@app.route('/')
def index():
    username = request.cookies.get('username')
    # use cookies.get(key) instead of cookies[key] to not get a
    # KeyError if the cookie is missing.
```
**存储cookie**:：

from flask import make_response

```python
@app.route('/')
def index():
    resp = make_response(render_template(...))
    resp.set_cookie('username', 'the username')
    return resp
```
请注意， cookies 是设置在响应对象上。通常只是从视图函数返回字符串，Flask 会把它们转换为响应对象。如果你想显式地转换，那么可以使用 make_response() 函数，然后再修改它。

有时，您可能希望将cookie设置在响应对象尚不存在的位置。这可以通过使用 延迟的请求回调 模式。

有关此信息，请参见 :about-responses。

## 重定向和错误
使用 redirect() 函数可以重定向。使用 abort() 可以更早退出请求，并返回错误代码:

```python
from flask import abort, redirect, url_for

@app.route('/')
def index():
    return redirect(url_for('login'))

@app.route('/login')
def login():
    abort(401)
    this_is_never_executed()
```

这是一个相当无意义的例子，它让一个用户从索引页重定向到一个无法访问的页面（401 表示禁止访问）。但是上例可以说明重定向和出错跳出是如何工作的。

默认情况下，每个错误代码都会显示一个黑白错误页。如果要自定义错误页，可以使用 errorhandler装饰器：

```python

from flask import render_template

@app.errorhandler(404)
def page_not_found(error):
    return render_template('page_not_found.html'), 404
```

注意 render_template() 后面的 404 ，这表示页面对应的出错代码是 404 ，即页面不存在。默认为 200 表示：一切正常。

## 关于响应
视图函数的返回值将自动转换为响应对象。如果返回值是一个字符串，它将被转换为一个以字符串作为响应体的响应对象，则 200 OK 状态代码和A text/html 模仿型。如果返回值是dict， jsonify() 被调用以产生响应。Flask应用于将返回值转换为响应对象的逻辑如下：

- 如果返回的是一个合法的响应对象，它会从视图直接返回。

- 如果返回的是一个字符串，响应对象会用字串符数据和默认参数创建。

- 如果是dict，则使用 jsonify .

- 如果返回一个元组，元组中的项可以提供额外的信息。这样的元组必须是 (response, status) ， (response, headers) 或 (response, status, headers) . 这个 status 值将覆盖状态代码和 headers 可以是其他头值的列表或字典。

- 如果以上都不是，那么 Flask 会假定返回值是一个有效的 WSGI 应用并把它转换为 一个响应对象。

如果要在视图中获取响应对象的结果，可以使用make_response函数。

比如你有这样一个视图：

```python
from flask import render_template

@app.errorhandler(404)
def not_found(error):
    return render_template('error.html'), 404

```
你只需要把返回值表达式传递给make_response，获取响应对象并修改它，然后返回它：

```python
from flask import make_response

@app.errorhandler(404)
def not_found(error):
    resp = make_response(render_template('error.html'), 404)
    resp.headers['X-Something'] = 'A value'
    return resp

```

## 使用JSON的API
编写API时，一种常见的响应格式是JSON。开始用Flask编写这样一个API很容易。如果您返回 dict 从视图来看，它将被转换为JSON响应。

```python
@app.route("/me")
def me_api():
    user = get_current_user()
    return {
        "username": user.username,
        "theme": user.theme,
        "image": url_for("user_image", filename=user.image),
    }
```
根据您的API设计，您可能希望为以下类型创建JSON响应 dict . 在这种情况下，使用 jsonify() 函数，该函数将序列化任何受支持的JSON数据类型。或者看看支持更复杂应用程序的Flask社区扩展。

```python
from flask import jsonify

@app.route("/users")
def users_api():
    users = get_all_users()
    return jsonify([user.to_json() for user in users])
```
## 会话¶
除了请求对象，还有一个名为 session 的对象，允许你在不同请求之间储存信息。这个对象相当于用密钥签名加密的 cookie ，即用户可以查看你的 cookie ，但是如果没有密钥就无法修改它。

要使用会话，你必须设置一个密钥。以下是会话的工作方式：

```python
from flask import session

# Set the secret key to some random bytes. Keep this really secret!
app.secret_key = b'_5#y2L"F4Q8z\n\xec]/'

@app.route('/')
def index():
    if 'username' in session:
        return f'Logged in as {session["username"]}'
    return 'You are not logged in'

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        session['username'] = request.form['username']
        return redirect(url_for('index'))
    return '''
        <form method="post">
            <p><input type=text name=username>
            <p><input type=submit value=Login>
        </form>
    '''

@app.route('/logout')
def logout():
    # remove the username from the session if it's there
    session.pop('username', None)
    return redirect(url_for('index'))

```

如何生成好的密钥
一个好的密钥应当有足够的随机性。 操作系统可以有多种方式基于密码随机生成器来生成随机数据。使用下面的命令可以快捷的为 Flask.secret_key （ 或者 SECRET_KEY ）生成值:

```bash
python -c 'import os; 
print(os.urandom(16))'
b'_5#y2L"F4Q8z\n\xec]/'
```
使用基于cookie的会话需要注意：Flask 会取出会话对象中的值，把值序列化后储存到 cookie 中。在打开 cookie 的情况下，如果需要查找某个值，但是这个值在请求中没有持续储存的话，那么不会得到一个明确的出错信息。请检查页面响应中的 cookie 的大小是否与网络浏览器所支持的大小一致。

除了默认的客户端的会话之外，还有许多 Flask 扩展支持服务端会话。

## 信息闪现
好的应用程序和用户界面都有良好的反馈。如果用户没有得到足够的反馈，他们可能最终会讨厌这个应用程序。flask通过闪现系统提供了一种非常简单的向用户提供反馈的方式。闪现系统的基本工作原理是在请求结束时记录一个消息，提供且只提供给下一个请求使用。通常通过一个布局模板来展现闪现的消息。

要闪烁信息，请使用 flash() 方法，获取可以使用的消息 get_flashed_messages() 也可以在模板中找到。看到了吗 信息闪烁 举一个完整的例子。

## 日志记录
Changelog
有时候你会遇到这样的情况，你处理的数据本应该是正确的，但实际上不是。例如因为用户篡改了数据或客户端代码出错而导致一个客户端代码向服务器发送了明显错误的 HTTP 请求。多数时候在类似情况下，返回 400 Bad Request 就没事了，但是有时候不能这么做，并且要让代码继续运行。

你可能还想记录下发生了什么问题。这时候日志记录就派上了用场。从Flask0.3开始，就已经为你配置好了一个日志工具。

以下是一些调用日志纪录的示例：

```bash
app.logger.debug('A value for debugging')
app.logger.warning('A warning occurred (%d apples)', 42)
app.logger.error('An error occurred')
```

附件 logger 是标准日志记录 Logger 所以直接去找官员 logging 有关详细信息，请参阅文档。

## 钩子WSGI中间件
要将WSGI中间件添加到Flask应用程序中，请包装应用程序的 wsgi_app 属性。例如，应用Werkzeug的 ProxyFix 运行在Nginx后面的中间件：

```python
from werkzeug.middleware.proxy_fix import ProxyFix
app.wsgi_app = ProxyFix(app.wsgi_app)
```
包装 app.wsgi_app 而不是 app 意思是说 app 仍然指向Flask应用程序，而不是中间件，因此您可以继续使用和配置 app 直接。

## 使用Flask扩展
扩展是帮助完成公共任务的包。例如 Flask-SQLAlchemy 为在 Flask 中轻松使用 SQLAlchemy 提供支持。

有关flask扩展的更多信息，请参阅 [扩展](https://www.osgeo.cn/flask/extensions.html) .

## 部署到Web服务器
准备好部署你的新Flask应用了吗？看到了吗 [部署选择](https://www.osgeo.cn/flask/deploying/index.html).