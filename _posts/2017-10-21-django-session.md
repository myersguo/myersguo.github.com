---
layout: wp
title: django session & user 处理
---

「校招面试中途」，闲着写一下 django 的 default session middleware (django.contrib.sessions.middleware.SessionMiddleware)的逻辑:   

### sesssion ###

配置:  

```
SESSION_COOKIE_NAME = 'sessionid'                       # session id ,cookie 名称
SESSION_COOKIE_AGE = 60 * 60 * 24 * 7 * 2               # session cookie 过期时间
SESSION_COOKIE_DOMAIN = None                            # cookie domain
SESSION_COOKIE_SECURE = False                           # 是否 https only
SESSION_COOKIE_PATH = '/'                               # cookie path
SESSION_COOKIE_HTTPONLY = False                         # Whether to use the non-RFC standard httpOnly flag (IE, FF3+, others)
SESSION_SAVE_EVERY_REQUEST = False                      # Whether to save the session data on every request.
SESSION_EXPIRE_AT_BROWSER_CLOSE = False                 # Whether a user's session cookie expires when the Web browser is closed.
SESSION_ENGINE = 'django.contrib.sessions.backends.db'  # The module to store session data
SESSION_FILE_PATH = None                                # Directory to store session files if using the file session module. If None, the backend will use a sensible default.
```

默认的 session engine 是 `django.contrib.sessions.backends.db`, 其他的有 cache,file 等。所有 session engine 集成自 `django.contrib.sessions.backends.base.SessionBase, session 类需要继承并实现 `save`, `delete`, `load`, create` 方法。   


* load: 从cache/db 根据 session key 加载 session内容,如果不存在就 create   
* create: 生成一个唯一的 session key, 并保存到 cache/db
* save: save session data to cache/db   
* delete: delete from cache/data   

来看下 session plugin 的处理流程:  
 

```
import time

from django.conf import settings
from django.utils.cache import patch_vary_headers
from django.utils.http import cookie_date
from django.utils.importlib import import_module

class SessionMiddleware(object):
    def process_request(self, request):
        engine = import_module(settings.SESSION_ENGINE)
        session_key = request.COOKIES.get(settings.SESSION_COOKIE_NAME, None)
        #session key,cookie 的值
        #初始化request.session 的值
        request.session = engine.SessionStore(session_key)

    def process_response(self, request, response):
        """
        If request.session was modified, or if the configuration is to save the
        session every time, save the changes and set a session cookie.
        """
        try:
            accessed = request.session.accessed
            modified = request.session.modified
        except AttributeError:
            pass
        else:
            if accessed:
                patch_vary_headers(response, ('Cookie',))
            if modified or settings.SESSION_SAVE_EVERY_REQUEST:
                if request.session.get_expire_at_browser_close():
                    max_age = None
                    expires = None
                else:
                    max_age = request.session.get_expiry_age()
                    expires_time = time.time() + max_age
                    expires = cookie_date(expires_time)
                # Save the session data and refresh the client cookie.
                request.session.save()
                response.set_cookie(settings.SESSION_COOKIE_NAME,
                        request.session.session_key, max_age=max_age,
                        expires=expires, domain=settings.SESSION_COOKIE_DOMAIN,
                        path=settings.SESSION_COOKIE_PATH,
                        secure=settings.SESSION_COOKIE_SECURE or None,
                        httponly=settings.SESSION_COOKIE_HTTPONLY or None)
        return response
```

上面的 middleware 处理 session， process_request 获取 session 值, process_response 根据 session 是否更改来更新 session。   


### user ###

在`session` 模块，你可以通过 request.session 来获取当前的session信息，那么如果通过 session 找到用户呢？---- `django.contrib.auth.AuthenticationMiddleware`:   


```
class LazyUser(object):
    def __get__(self, request, obj_type=None):
        if not hasattr(request, '_cached_user'):
            from django.contrib.auth import get_user
            request._cached_user = get_user(request)
        return request._cached_user

class AuthenticationMiddleware(object):
    def process_request(self, request):
        assert hasattr(request, 'session'), "The Django authentication middleware requires session middleware to be installed. Edit your MIDDLEWARE_CLASSES setting to insert 'django.contrib.sessions.middleware.SessionMiddleware'."
        request.__class__.user = LazyUser()
        return None
```

request.user 就是由 django的 auth middleware 产生，我们看下如何获取 `lazyuser`:   


```
def get_user(request):
    from django.contrib.auth.models import AnonymousUser
    try:
        user_id = request.session[SESSION_KEY]
        backend_path = request.session[BACKEND_SESSION_KEY]
        backend = load_backend(backend_path)
        user = backend.get_user(user_id) or AnonymousUser()
    except KeyError:
        user = AnonymousUser()
    return user
```

未登录是，用户为 `AnonymouseUser`, 登录:   


```
def login(request, user):
    if user is None:
        user = request.user
    # TODO: It would be nice to support different login methods, like signed cookies.
    if SESSION_KEY in request.session:
        if request.session[SESSION_KEY] != user.id:
            # To avoid reusing another user's session, create a new, empty
            # session if the existing session corresponds to a different
            # authenticated user.
            request.session.flush()
    else:
        request.session.cycle_key()
    request.session[SESSION_KEY] = user.id
    request.session[BACKEND_SESSION_KEY] = user.backend
    if hasattr(request, 'user'):
        request.user = user
    user_logged_in.send(sender=user.__class__, request=request, user=user)
```

登录成功之后存储 user.backend 到 session 中.   

auth 模块提供了 `authenticate` 方法:   

```
def authenticate(**credentials):
    for backend in get_backends():
        try:
            user = backend.authenticate(**credentials)
        except TypeError:
            continue
        if user is None:
            continue
        user.backend = "%s.%s" % (backend.__module__, backend.__class__.__name__)
        return user

def get_backends():
    from django.conf import settings
    backends = []
    for backend_path in settings.AUTHENTICATION_BACKENDS:
        backends.append(load_backend(backend_path))
    if not backends:
        raise ImproperlyConfigured('No authentication backends have been defined. Does AUTHENTICATION_BACKENDS contain anything?')
    return backends
```

验证方，调用 `authenticate` 来进行认证。   
我们来看下 `django.contrib.auth.backends.ModelBackend` 的认证过程：   

```
    def authenticate(self, username=None, password=None):
        try:
            user = User.objects.get(username=username)
            if user.check_password(password):
                return user
        except User.DoesNotExist:
            return None
```

ok, 到此，我们可以很明朗的看出来：  

session 信息存储在 `django_session` 中, 用户信息存储在  `auth_user`中，组信息存储在  `auth_group`中，权限信息 `auth_group_permission`。    


权限这一块，下次再说。。。    

