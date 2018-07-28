---
layout: wp
title : flask
comments: true
---

flask 的 uwsgi 是基于[werkzeug](http://werkzeug.pocoo.org/), django 是自己实现了一套 wsgi 协议。


### 路由解析 ###

flask 的路由由 werkzeug 的 routing 模块实现，我们来看 flask 的封装：

入口 `wsgi_app` :

	```
	ctx = self.request_context(environ) # return RequestContext(self, environ)
ctx.push()
	error = None
	try:
try:
response = self.full_dispatch_request()
	except Exception as e:
	error = e
	response = self.make_response(self.handle_exception(e))
return response(environ, start_response)
	finally:
	if self.should_ignore_error(error):
	error = None
	ctx.auto_pop(error)
	```

#### dispatch_request ####

	`return self.view_functions[rule.endpoint](**req.view_args)`

	这里的 view_functions 是在添加规则时（add_url_rule）设置的, 路由 rule 与 对应的 view 关系 list。


#### url_adapter, url_map  ####

	werkzeug.routing:

	create_url_adapter:创建一个 url dapater
	url_adapter.match: 获取匹配的 url rule
	self.view_functions: [rule.endpoint](**req.view_args)：  执行对应的 function


	app.add_url_rule()： 添加路由，相当于注解 app.route:

	```
	def index():
		pass
		app.add_url_rule('/', 'index', index)

		相当于

		@app.route('/')
		def index():
			pass
			```

			rule 包括三个部分：
			converter, arguments, variable
			<converter(arguments):name>

								  比如:

								  ```
								  import os
								  from werkzeug.wrappers import Request, Response
								  from werkzeug.wsgi import SharedDataMiddleware
								  from werkzeug.routing import Map, Rule
								  from werkzeug.exceptions import HTTPException, NotFound
								  from werkzeug.routing import BaseConverter

								  class RegexConverter(BaseConverter):
									  def __init__(self, url_map, *args):
										  super(RegexConverter, self).__init__(url_map)
										  self.regex = args[0]

										  class Shortly(object):
											  def __init__(self):
												  self.url_map = Map([
														  Rule('/', endpoint='new_url'),
														  Rule('/<regex(".*"):others>', endpoint='new_url')
												  ], converters={'regex': RegexConverter})

												  def dispatch_request(self, request):
													  return Response('Hello World!')

													  def dispatch_request(self, request):
														  adapter = self.url_map.bind_to_environ(request.environ)
														  try:
															  endpoint, values = adapter.match()
															  return getattr(self, 'on_' + endpoint)(request, **values)
															  except HTTPException, e:
															  return e
															  def on_new_url(self, request, others=None):
																  return Response('Hello World!')
																  def wsgi_app(self, environ, start_response):
	request = Request(environ)
	response = self.dispatch_request(request)
	return response(environ, start_response)

	def __call__(self, environ, start_response):
		return self. wsgi_app(environ, start_response)


		def create_app(redis_host='localhost', redis_port=6379, with_static=True):
			app = Shortly()
			if with_static:
				app.wsgi_app = SharedDataMiddleware(app.wsgi_app, {
						'/static':  os.path.join(os.path.dirname(__file__), 'static')
						})
return app

if __name__ == '__main__':
	from werkzeug.serving import run_simple
app = create_app()
	run_simple('127.0.0.1', 5000, app, use_debugger=True, use_reloader=True)
	```


### middleware ###

app.wsgi_app = MyMiddleware(app.wsgi_app)


	flask 的中间件是通过一些阶段方法来实现（before, after, tear down）

	```
	before_request:
app.before_request(f)
	after_request:
app.after_request(f)
	teardown_request:
app.teardown_request(f)
	```

### ctx(flask.ctx) 请求上下文  ###

	***RequestContext***
	The request context contains all request relevant information.  It is created at the beginning of the request and pushed to the `_request_ctx_stack` and removed at the end of it.  It will create the URL adapter and request object for the WSGI environment provided


	***AppContext***:
	The application context binds an application object implicitly to the current thread or greenlet.The application context is also implicitly created if a request context is created  but the application is not on top of the individual application  contex


### hello,world ###

```
# coding: utf-8
from flask import Flask
from flask import render_template
from flask import request
app = Flask(__name__)



@app.route('/')
def hello_world():
    name = request.args.get('name', 'world')
    return 'Hello, ' + name

@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
```

flask run --host=0.0.0.0   
or 
uwsgi -s /tmp/uwsgi.sock --module myapp --callable app --http 0.0.0.0:5000   

换成 django style 写 hello,world:   

```
# index.py
# coding: utf-8

from flask import Flask

def create_app():
    app = Flask(__name__, static_url_path='/static', static_folder="/webroot")
    import urls
    index = 0
    for pattern in urls.urlpatterns:
        rule, view_func = pattern
        index += 1
        app.add_url_rule(rule, endpoint=str(index), view_func=view_func)

    return app

app = create_app()

#urls.py
import views
urlpatterns = [
    ('/', views.hello),
]

# views.py
def hello():
    return 'Hello, World!'
```




### 参考资料 ###

		[https://blog.tonyseek.com/post/the-context-mechanism-of-flask/](https://blog.tonyseek.com/post/the-context-mechanism-of-flask/)
		[http://werkzeug-docs-cn.readthedocs.io/zh_CN/latest/tutorial.html](http://werkzeug-docs-cn.readthedocs.io/zh_CN/latest/tutorial.html)
		[https://jachinlin.github.io/2017/09/12/Werkzeug-URL-Routing/](https://jachinlin.github.io/2017/09/12/Werkzeug-URL-Routing/)

