---
layout: wp
title: event hook
---

event hook 是[观察者模式](https://github.com/faif/python-patterns/blob/master/patterns/behavioral/observer.py)的典型应用。`provide a callback for notification of events/changes to data`   


### django ###

用信号标识接收的方式，connect 表示接入， send 进行发送新号，所有接入方都将进行处理。   

```
class Signal(object):
    def __init__(self):
        self.receivers = []
    def connect(self, receiver):
       self.receivers.append(receiver)
    def send(self, sender, **named):
        responses = []
        for receiver in self.receivers:
            response = receiver(**named)
            responses.append((receiver, response))
        return responses

test_signal = Signal()
def test():
    print " go test"

test_signal.connect(test)
test_signal.send(sender="sender")
```

### flask ###

flask 里的 signal 使用 `blinker` 的 `Signal` 

```
class Signal(object):
    def __init__(self):
        self.receivers = {}
    def connect(self, receiver):
        self.receivers.setdefault(receiver_id, receiver_ref)
    def send(self):
        return [(receiver, receiver(sender, **kwargs))
                    for receiver in self.receivers_for(sender)]
```
和 django 非常相似。   

### other ###

locust

```
class EventHook(object):
    def __init__(self):
        self._handlers = []
    def __iadd__(self, handler):
        self._handlers.append(handler)
        return self

    def fire(self, reverse=False, **kwargs):
        if reverse:
            self._handlers.reverse()
        for handler in self._handlers:
            handler(**kwargs)

request_success = EventHook()
def on_success():
    print "on_success"

request_success += on_success
request_success.fire()
```

和 `django`, `flask` 类似。  

