---
title: ironic python agent source code 一瞥
layout: wp
comments: true
---

入口 cmd/run:  

```
from ironic_python_agent import agent
def run():
   agent.IronicPythonAgent(api_url, agent.Host,....)
        .run()
```

agent:   

```
extensions/base.py

class BaseAgentExtension(object):
    def __init__(self, agent=None):
        super(BaseAgentExtension, self).__init__()
        self.agent = agent
        self.command_map = dict(
            (v.command_name, v) for k, v in inspect.getmembvers(self)
            if hasattr(v, 'command_name')
        )
    def execute(self, command_name, **kwargs):
        cmd = self.command_map.get(command_name)
        if cmd is None:
            raise
        return cmd(**kwargs)

def ExecuteCommandMixin(object):
    def __init__(self):
        self.command_lock = threading.Lock()
        self.command_results = collections.OrderedDict()
        self.ext_mgr = None
    #命令的扩展都是 <extension>.<name> 
    def split_command(self, command_name):
        command_parts = command_name.split('.', 1)
        return (command_parts[0], command_parts[1])
    def get_extension(self, extension_name):
        ext = self.ext_mgr[extension_name].obj
        ext.ext_mgr = self.ext_mgr
        return ext
    def execute_command(self, command_name, **kwargs):
        with self.command_lock:
            extension_part, command_part = self.split_command(command_name)
        try:
            ext = self.get_extension(extension_part)
            result = ext.execute(command_part, **kwargs)
        except KeyError:
            ...
        self.command_results[result.id] = result

class IronicPytonAgent(base.ExecuteCommandMixin):
    def __init__(self, api_url, advertise_address,...):
        super(IronicPythonAgent, self).__init__()
        self.ext_mgr = extensions.ExtensionManager(
            namespace='ironnic_python_agent.extensions',
            invoke_on_load=True,
            invoke_kwds={'agent':self}
        )
        if self.api_url:
            self.api_client = ironic_api_client.APIClient(self.api_url)
            #心跳
            self.heartbeater = IronicPythonAgentHeartbeater(self)
    
    def run(self):
        """启动agent""
        self.started_at = _time()
        ...
        wsgi = simple_server.make_server(
            self.listen_address.hostname,
            self.listen_address.port,
            self.api,
            server_class=simple_server.WSGIServer)
        if not self.standalone and self.api_url:
            #与服务端的心跳
            self.heartbeater.start() 
        try:
            wsgi.serve_forever() 
        except:
            ...

```

application/controller:   


```
api/controllers/v1/command.py
class CommandController(rest.RestController):
    @wsme_pecan.wsexpose(CommandResult, types.text, body=Comand)
   def post(self, wait=None, command=None):
        ""Post a command for the agent to run
        if command is None:
            command = Command()        
        agent = pecan.request.agent
        result = agent.execute_command(command.name, **command.params)
        if wait and wait.lower() == 'true':
            result.join()
        return result
```


命令使用插件(extension) 的方式执行，agent 启动一个 api server 用于接收指令，同时开启心跳。每当rest请求过来时执行命令。   


