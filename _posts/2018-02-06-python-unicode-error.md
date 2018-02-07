---
layout: wp
title: python http 请求编码问题追查
---

背景：   
有一个上传文件的业务，偶尔会报错：   
`'utf8' codec can't decode byte 0xce in position 17: invalid continuation byte`   

问题追查，我们看下堆栈上下文:   

```
import traceback
traceback.print_exc()
```

```
  File "/usr/lib/python2.7/httplib.py", line 1039, in request
    self._send_request(method, url, body, headers)
  File "/usr/lib/python2.7/httplib.py", line 1073, in _send_request
    self.endheaders(body)
  File "/usr/lib/python2.7/httplib.py", line 1035, in endheaders
    self._send_output(message_body)
  File "/usr/lib/python2.7/httplib.py", line 877, in _send_output
    msg += message_body
UnicodeDecodeError: 'utf8' codec can't decode byte 0xce in position 17: invalid continuation byte
```

ok，我们看到这里是因为字符串相加造成的。看到这里就是经典的 python unicode 和 string 的问题了。我们先补充下基础知识，先看一个例子：    

```
["{:02x}".format(ord(c)) for c in '我']
['e6', '88', '91']
["{:02x}".format(ord(c)) for c in u'我']
['6211']
[ord(c) for c in u'我']
[25105]
```
因为计算机无法表示'我'，只能用编码方式表示。所以系统需要一个默认的编码方式,可通过：`sys.setdefaultencoding('UTF8')`来设置。ok, 那:   

```
'我' + u'我'
```

就相当于:   

```
'我'.decode('ascii') + u'我'
```
当然会报错。

回到我们最初的问题,我们来看下`httplib`的代码:    

```  
    def request(self, method, url, body=None, headers={}):
        """Send a complete request to the server."""
        self._send_request(method, url, body, headers)
        def _send_request(self, method, url, body, headers):
        # Honor explicitly requested Host: and Accept-Encoding: headers.
        header_names = dict.fromkeys([k.lower() for k in headers])
        skips = {}
        if 'host' in header_names:
            skips['skip_host'] = 1
        if 'accept-encoding' in header_names:
            skips['skip_accept_encoding'] = 1

        self.putrequest(method, url, **skips)

        if body is not None and 'content-length' not in header_names:
            self._set_content_length(body)
        for hdr, value in headers.iteritems():
            self.putheader(hdr, value)
        self.endheaders(body)
	    
	def _send_output(self, message_body=None):
        """Send the currently buffered request and clear the buffer.

        Appends an extra \\r\\n to the buffer.
        A message_body may be specified, to be appended to the request.
        """
        self._buffer.extend(("", ""))
        msg = "\r\n".join(self._buffer)
        del self._buffer[:]
        # If msg and message_body are sent in a single send() call,
        # it will avoid performance problems caused by the interaction
        # between delayed ack and the Nagle algorithm.
        if isinstance(message_body, str):
            msg += message_body
            message_body = None
        self.send(msg)
        if message_body is not None:
            #message_body was not a string (i.e. it is a file) and
            #we must run the risk of Nagle
            self.send(message_body)
```

可以看到： `msg += message_body`，其中`msg`是http的请求头的相关信息,而message_body 是请求体.   

而这里必须是 ascii 编码。   

我们写个 demo 复现一下问题：    

```
# coding: utf8
import sys
reload(sys)
sys.setdefaultencoding('UTF8')
from httplib import HTTPConnection

conn = HTTPConnection('www.baidu.com')
conn.request('GET', '/s=xxxx', '我\xce\xe6\x88\x91')
print conn.getresponse().read()

conn = HTTPConnection('www.baidu.com')
conn.request('GET', u'/s=xxxx', '我\xce\xe6\x88\x91')
print conn.getresponse().read()
```


ok,上面的输出没有问题，下面的输出，就会直接报错。因为我们这里的url 会拼接到请求头中，没有使用 ascii。   

(done)

