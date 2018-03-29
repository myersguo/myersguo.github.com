---
layout: wp
title: django 初探
comments: true
---

Django 是一个 *web application*, 其他的还有 flask,tornado 等, 可以与 gunicorn, uwsgi, gevent, werkzeug 等 *web server* 结合使用。   

Django 如果单独使用(使用 python manager 命令行启动)时，使用 wsgiref.simple_server 作为 web server.   


一个 WEB 的框架，必然要解决 URL 路由的问题。 Django 怎么做请求路由的呢？  

python manager runserver，即 django.core.management.execute_from_command_line()


```
def execute_from_command_line(argv=None):
    """
    A simple method that runs a ManagementUtility.
    """
    utility = ManagementUtility(argv)
    utility.execute()
```

其中 ManagementUtility 先解析到全局配置 settings，Django 的启动的全局配置：   django\conf\global_settings.py ，如果指定了 setting 模块（必须设置 ALLOWED_HOST 和 SECRET_KEY。
），会替换默认的全局配置。

django.core.management.__init__.py 文件中：   

```
def execute(self):
    ...
    self.fetch_command(subcommand).run_from_argv(self.argv)
```

这里的 commands 一个例子：   

```
{
    'clearsessions': 'django.contrib.sessions',
    'compilemessages': 'django.core',
    'dumpdata': 'django.core',
    'startproject': 'django.core',
    'findstatic': 'django.contrib.staticfiles',
    'sqldropindexes': 'django.core',
    'sqlcustom': 'django.core',
    'createcachetable': 'django.core',
    'flush': 'django.core',
    'squashmigrations': 'django.core',
    'check': 'django.core',
    'sqlmigrate': 'django.core',
    'purge_session_service_tickets': 'django_cas',
    'makemigrations': 'django.core',
    'runserver': 'django.contrib.staticfiles',
    'celery': 'djcelery',
    'migrate': 'django.core',
    'showmigrations': 'django.core',
    'dbshell': 'django.core',
    'runfcgi': 'django.core',
    'test': 'django.core',
    'sqlclear': 'django.core',
    'changepassword': 'django.contrib.auth',
    'celeryd': 'djcelery',
    'celerycam': 'djcelery',
    'shell': 'django.core',
    'sqlsequencereset': 'django.core',
    'testserver': 'django.core',
    'makemessages': 'django.core',
    'djcelerymon': 'djcelery',
    'sql': 'django.core',
    'validate': 'django.core',
    'sqlall': 'django.core',
    'collectstatic': 'django.contrib.staticfiles',
    'celerymon': 'djcelery',
    'clean_kombu_messages': 'kombu.transport.django',
    'diffsettings': 'django.core',
    'celerybeat': 'djcelery',
    'syncdb': 'django.core',
    'inspectdb': 'django.core',
    'startapp': 'django.core',
    'createsuperuser': 'django.contrib.auth',
    'sqlindexes': 'django.core',
    'celeryd_detach': 'djcelery',
    'loaddata': 'django.core',
    'celeryd_multi': 'djcelery',
    'sqlflush': 'django.core'
}
```

run_from_argv 将最终的执行 execute

这里的 fetch_command，是从命令行解析要执行的命令的实例。如果是 runserver 就返回 django.core.management.runserver.py 中的Command，它的 execute 把请求交个 django.core.servers.basehttp 中的 run。  

runserver.py 中的 execute 逻辑

```
try:
    handler = self.get_handler(*args, **options)
    run(self.addr, int(self.port), handler,
        ipv6=self.use_ipv6, threading=threading, server_cls=self.server_cls)
    except socket.error as e:
    ...
    except KeyboardInterrupt:
    if shutdown_message:
        self.stdout.write(shutdown_message)
    sys.exit(0)
```

我们来看下 

```
def get_internal_wsgi_application():
    from django.conf import settings
    app_path = getattr(settings, 'WSGI_APPLICATION')
    if app_path is None:
        return get_wsgi_application()

    try:
        return import_string(app_path)
    except ImportError as e:
        msg = (
            "WSGI application '%(app_path)s' could not be loaded; "
            "Error importing module: '%(exception)s'" % ({
                'app_path': app_path,
                'exception': e,
            })
        )
        six.reraise(ImproperlyConfigured, ImproperlyConfigured(msg),
                    sys.exc_info()[2])

```


### django 的wsgi入口 ###

```
from django.core.wsgi import get_wsgi_application
application = get_wsgi_application()

or 
from django.core.handlers.wsgi import WSGIHandler
```
WSGIHandler 初始化 basehandler， 然后 new WSGIRequest(request = self.request_class(environ)), 获取返回值(response = self.get_response(request))

老版本:   

```
    def get_response(self, request):
        "Returns an HttpResponse object for the given HttpRequest"
        from django.core import exceptions, urlresolvers
        from django.conf import settings

        try:
            urlconf = settings.ROOT_URLCONF
            urlresolvers.set_urlconf(urlconf)
            resolver = urlresolvers.RegexURLResolver(r'^/', urlconf)
            try:
                response = None
                for middleware_method in self._request_middleware:
                    response = middleware_method(request)
                    if response:
                        break

                if response is None:
                    if hasattr(request, "urlconf"):
                        urlconf = request.urlconf
                        urlresolvers.set_urlconf(urlconf)
                        resolver = urlresolvers.RegexURLResolver(r'^/', urlconf)

                    callback, callback_args, callback_kwargs = resolver.resolve(
                            request.path_info)

                    # Apply view middleware
                    for middleware_method in self._view_middleware:
                        response = middleware_method(request, callback, callback_args, callback_kwargs)
                        if response:
                            break

                if response is None:
                    try:
                        response = callback(request, *callback_args, **callback_kwargs)
                    except Exception, e:
                        for middleware_method in self._exception_middleware:
                            response = middleware_method(request, e)
                            if response:
                                break
                        if response is None:
                            raise

                # Complain if the view returned None (a common error).
                if response is None:
                    try:
                        view_name = callback.func_name # If it's a function
                    except AttributeError:
                        view_name = callback.__class__.__name__ + '.__call__' # If it's a class
                    raise ValueError("The view %s.%s didn't return an HttpResponse object." % (callback.__module__, view_name))

                if hasattr(response, 'render') and callable(response.render):
                    for middleware_method in self._template_response_middleware:
                        response = middleware_method(request, response)
                    response = response.render()

            except http.Http404, e:
                logger.warning('Not Found: %s' % request.path,
                            extra={
                                'status_code': 404,
                                'request': request
                            })
                if settings.DEBUG:
                    from django.views import debug
                    response = debug.technical_404_response(request, e)
                else:
                    try:
                        callback, param_dict = resolver.resolve404()
                        response = callback(request, **param_dict)
                    except:
                        try:
                            response = self.handle_uncaught_exception(request, resolver, sys.exc_info())
                        finally:
                            receivers = signals.got_request_exception.send(sender=self.__class__, request=request)
            except exceptions.PermissionDenied:
                logger.warning('Forbidden (Permission denied): %s' % request.path,
                            extra={
                                'status_code': 403,
                                'request': request
                            })
                response = http.HttpResponseForbidden('<h1>Permission denied</h1>')
            except SystemExit:
                # Allow sys.exit() to actually exit. See tickets #1023 and #4701
                raise
            except: # Handle everything else, including SuspiciousOperation, etc.
                # Get the exception info now, in case another exception is thrown later.
                receivers = signals.got_request_exception.send(sender=self.__class__, request=request)
                response = self.handle_uncaught_exception(request, resolver, sys.exc_info())
        finally:
            urlresolvers.set_urlconf(None)

        try:
            # Apply response middleware, regardless of the response
            for middleware_method in self._response_middleware:
                response = middleware_method(request, response)
            response = self.apply_response_fixes(request, response)
        except: # Any exception should be gathered and handled
            receivers = signals.got_request_exception.send(sender=self.__class__, request=request)
            response = self.handle_uncaught_exception(request, resolver, sys.exc_info())

        return response
```

!注意这里的处理过程。`python`中的全局变量再加载之后，不会每次都加载，而只有用到（django中的view）才会重新获取；那么，如果你是通过某个请求更改了全局变量，它只在那一台机器生效，如果需要多台机器都生效，需要另找办法。    




OK, Django 是怎么实现 WSGI的接口的呢？具体在 django\core\handlers\base.py 中。  

WSGIRequest

从接收请求到返回结果，它是怎么做的呢？  

```
def get_response(self, request):
    """Return an HttpResponse object for the given HttpRequest."""
    # Setup default url resolver for this thread
    set_urlconf(settings.ROOT_URLCONF)

    response = self._middleware_chain(request)

    # This block is only needed for legacy MIDDLEWARE_CLASSES; if
    # MIDDLEWARE is used, self._response_middleware will be empty.
    try:
        # Apply response middleware, regardless of the response
        for middleware_method in self._response_middleware:
            response = middleware_method(request, response)
            # Complain if the response middleware returned None (a common error).
            if response is None:
                raise ValueError(
                    "%s.process_response didn't return an "
                    "HttpResponse object. It returned None instead."
                    % (middleware_method.__self__.__class__.__name__))
    except Exception:  # Any exception should be gathered and handled
        signals.got_request_exception.send(sender=self.__class__, request=request)
        response = self.handle_uncaught_exception(request, get_resolver(get_urlconf()), sys.exc_info())

    response._closable_objects.append(request)

    # If the exception handler returns a TemplateResponse that has not
    # been rendered, force it to be rendered.
    if not getattr(response, 'is_rendered', True) and callable(getattr(response, 'render', None)):
        response = response.render()

    if response.status_code == 404:
        logger.warning(
            'Not Found: %s', request.path,
            extra={'status_code': 404, 'request': request},
        )

    return response


 def _legacy_get_response(self, request):
        """
        Apply process_request() middleware and call the main _get_response(),
        if needed. Used only for legacy MIDDLEWARE_CLASSES.
        """
        response = None
        # Apply request middleware
        for middleware_method in self._request_middleware:
s            response = middleware_method(request)
            if response:
                break

        if response is None:
            response = self._get_response(request)
        return response

```

Django 默认加载的 middleware 是：  

```
MIDDLEWARE_CLASSES = [
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
]
```

对 process_request ， process_view， process_template_response， process_response , process_exception 进行处理。   

请求怎么解析呢？   

RegexURLResolver（ django\urls\resolvers.p )   


### 静态文件的处理 ###


当打开了 DEBUG 开关，   

静态文件的处理就交给了 StaticFilesHandler， 它会使用 STATICFILES_FINDERS 中的 finder 类来查找静态文件的路径，然后解析静态文件。 但，它要求  STATIC_ROOT 和  STATICFILES_DIRS 的路径不能相同，因为 源和目的要分开的啊。     

所以，当你自己要处理静态文件的时候，就要关闭 DEBUG 开关。     

### DB 操作 ###


#### django.db.models.Manager ####

>A Manager is the interface through which database query operations are provided to Django models. At least one Manager exists for every model in a Django application.
`django\db\models\query`

在用 `model`的时候，经常使用 model.objects 这个 objects 属性，这个属性哪里来的呢？我们来梳理下。   
在 django\db\models\base.py 中我们可以看到里初始化中以下代码：  

```
class ModelBase(type):
    """
    Metaclass for all models.
    """
    def __new__(cls, name, bases, attrs):
        super_new = super(ModelBase, cls).__new__

        ...
        new_class._prepare()
        ...
    def _prepare(cls):
       ensure_default_manager(cls) 
```

这里的 ensure_default_manager(django\db\models\manager.py) 中为每个 model 设置了一个 manager:   

```
def ensure_default_manager(cls):
    """
    Ensures that a Model subclass contains a default manager  and sets the
    _default_manager attribute on the class. Also sets up the _base_manager
    points to a plain Manager instance (which could be the same as
    _default_manager if it's not a subclass of Manager).
    """
    if cls._meta.abstract:
        setattr(cls, 'objects', AbstractManagerDescriptor(cls))
        return
    elif cls._meta.swapped:
        setattr(cls, 'objects', SwappedManagerDescriptor(cls))
        return
	if not getattr(cls, '_default_manager', None):
        if any(f.name == 'objects' for f in cls._meta.fields):
            raise ValueError(
                "Model %s must specify a custom Manager, because it has a "
                "field named 'objects'" % cls.__name__
            )
        # Create the default manager, if needed.
        cls.add_to_class('objects', Manager())
        cls._base_manager = cls.objects
	...
class Manager(BaseManager.from_queryset(QuerySet)):
    pass
```

ok, 这样我们可以理解了， model.objects 其实是调用了 Manager 来管理 queryset.  

#### queryset ####   

>A QuerySet represents a collection of objects from your database. It can have zero, one or many filters. Filters narrow down the query results based on the given parameters. In SQL terms, a QuerySet equates to a SELECT statement, and a filter is a limiting clause such as WHERE or LIMIT.



常用的 SQL 及对应的代码：  

```
SELECT * FROM A
A.objects.all()

SELECT * FROM A WHERE A.id = 1;
A.objects.filter(id=1)

SELECT * FROM A WHERE a.name LIKE '%myersguo%' OR description like '%myersguo'
A.objects.filter(Q(name__icontains('myersguo')) | Q(description__icontains('myersguo')))

SELECT * FROM A order by id desc 
A.objects.all().order_by('-id')

SELECT COUNT(*) FROM A
A.objects.count()

如果想要获取到 values,可以使用 .values()

INSERT INT A (name) VALUES('myersguo')
A.save()

SELECT SUM(c), sum(b) FROM A GROUP BY a
A.objects.values('a').annotate(t=Sum('c'),b=Sum('b'))


SELECT SUM(c),SUM(b),DATE_FORMAT(FROM_UNIXTIME(start_time), '%Y-%m-%d') as t
FROM A
GROUP BY DATE_FORMAT(FROM_UNIXTIME(start_time), '%Y-%m-%d')
ORDER BY t desc

select_data = {"start_time": """DATE_FORMAT(FROM_UNIXTIME(start_time), '%%Y-%%m-%%d')"""}
A.objects.extra(select=select_data).values('start_time').annotate(t=Sum('c'), b=Sum('b')).order_by('start_time')

SELECT * FROM A LEFT JOIN B on A.id = B.aid
A.objects.select_related().all()
A.objects.exclude(B__isnull=False)

```


### 命令行 ###

我们经常要写一些  script 单独来跑（比如放到 cron 里面），而脚本直接使用 django 环境是不可以的。怎么使用 django 环境的：   

1) 使用 manager.py , django 提供了管理工具，使用管理工具可以执行脚本：   

```
python manager.py shell
```

或者使用插件(django-extension)：   

```
python manager.py runscript
```

2) 设置 django 环境:   

```
import sys,os
sys.path.append(os.path.dirname(os.path.abspath(__file__)) + "/../")
os.environ['DJANGO_SETTINGS_MODULE'] = '项目名.settings'
```

这个 `DJANGO_SETTINGS_MODULE` 我们在 `manager.py`中就看到过：  

```
#!/usr/bin/env python
import os
import sys

if __name__ == "__main__":
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "project.settings")

    from django.core.management import execute_from_command_line

    execute_from_command_line(sys.argv)
```

django 环境的初始化是依赖于 `DJANGO_SETTINGS_MODULE`的设置的。  


### django logging ###

django 启动的时候会配置logging,   

```
    from django.utils.log import configure_logging
    configure_logging(settings.LOGGING_CONFIG, settings.LOGGING)
    #相当于
    logging.config.dictConfig(settings.LOGGING)
```


### http response ###

http 请求到返回基类在 django/http/response.py 中 HttpResponseBase, 一些子类，如 SimpleTemplagteResponse ( 位于 django/template/response.py ,包含 render方法)。比较常用的:   

平时经常见到的 render,render_to_response 都是模版渲染的返回。也可以直接返回一个HttpResponse。   

渲染 json

```
class JsonResponse(HttpResponse):
    """
    An HTTP response class that consumes data to be serialized to JSON.

    :param data: Data to be dumped into json. By default only ``dict`` objects
      are allowed to be passed due to a security flaw before EcmaScript 5. See
      the ``safe`` parameter for more information.
    :param encoder: Should be an json encoder class. Defaults to
      ``django.core.serializers.json.DjangoJSONEncoder``.
    :param safe: Controls if only ``dict`` objects may be serialized. Defaults
      to ``True``.
    """

    def __init__(self, data, encoder=DjangoJSONEncoder, safe=True, **kwargs):
        if safe and not isinstance(data, dict):
            raise TypeError('In order to allow non-dict objects to be '
                'serialized set the safe parameter to False')
        kwargs.setdefault('content_type', 'application/json')
        data = json.dumps(data, cls=encoder)
        super(JsonResponse, self).__init__(content=data, **kwargs)
```

### django 用户 ###

django 的用户登录控制有：   

```
middleware:  
'django.contrib.auth.middleware.AuthenticationMiddleware',
'django.contrib.auth.middleware.SessionAuthenticationMiddleware',
'django_cas.middleware.CASDisabledCSRFMiddleware',
'django_cas.middleware.CASMiddleware',

AUTHENTICATION_BACKENDS = (
    'django.contrib.auth.backends.ModelBackend',
    'django_cas.backends.CASBackend',
)

```

我们来看一下这里的登录处理逻辑：   




(待续)  

