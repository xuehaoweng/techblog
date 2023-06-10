# Python项目如何部署

## python +uWSGI +nginx常规部署
```shell 

python +uWSGI +nginx

uWsgi为什么要结合nginx去部署python应用?

WSGI: Web Server Gateway Interface, 是一个接口，定义web server 如何转发请求到python应用。这样，只要实现web server都可以调用遵守此接口的任何python应用

uWsgi: 实现了WSGI的一个中间件

uWsgi 是实现了WSGI接口的，一个完整实现http server，可以直接部署python应用，但是为何还要用nginx呢？
因为nginx擅长高并发，静态文件，gzip压缩等，这些功能uWsgi不具备，如果网站访问量不大，完全可以只用uWsgi。


```
## nginx + uWsgi 组合使用原理
nginx 来完成proxy（反向代理），静态文件等服务。

动态请求转发给uWsgi调用python应用。

nginx与uWsgi通过uwsgi(全部小写)协议来完成，uwsgi是一个二进制协议，允许uWsgi 与 nginx等应用服务器交互，python代码里包括了wsgi app和简易的http server（不建议用于线上环境）

## supervisor 守护程序部署

```shell 

supervisor是python程序部署中常用的守护程序，
可以用于网络爬虫/python开发的web程序/定时程序······的守护，
使其在未知bug/意外出错停止的情况下重启，
supervisor+nginx常作为web的部署方式。

```