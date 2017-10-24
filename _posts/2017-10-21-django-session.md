---
layout: wp
title: django session处理
---

「校招面试中途」，闲着写一下 django 的 default session middleware (django.contrib.sessions.middleware.SessionMiddleware)的逻辑:   


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


