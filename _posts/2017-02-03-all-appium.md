---
layout: wp
title: overview of Appium
---

### 说在前面 ###

本文针对appium(version:1.6.4-beta)「比较粗糙」的介绍了下它的实现流程。难免有不妥支出，有任何问题，可直接沟通交流。   


## Appium的架构 ##

![appium](/public/appium-packages.png)   
![appium](/public/images/appium_ios.png)  
![appium](/public/images/apium_android.png)  


### 起步 ###

下载appium的源码，并安装依赖：   

```
git clone https://github.com/appium/appium.git
npm install
```

启动appium:`node .`  

这个启动命令实际是执行的：`node build\main.js`(package.json中指定了main入口)：  

```
...
 "main": "./build/lib/main.js",
  "bin": {
    "appium": "./build/lib/main.js"
  },
...
```



/build/main.js是由/lib/main.js经babel翻译后的结果，所以，我们来看下/lib/main.js来理解appium的流程。    


### appium server ###

![appium uml](/public/images/appium_uml.png)  


appium server端实现了HTTP REST API接口，将client端发来的API请求，解析，发送给执行端。我们看一下appium server的源码实现。

```
import { server as baseServer } from 'appium-base-driver';
import getAppiumRouter from './appium';
...

async function main (args = null) {
  //解析参数
  let parser = getParser();
  let throwInsteadOfExit = false;
  if (args) {
    args = Object.assign({}, getDefaultArgs(), args);
    if (args.throwInsteadOfExit) {
      throwInsteadOfExit = true;
      delete args.throwInsteadOfExit;
    }
  } else {
    args = parser.parseArgs();
  }
  await logsinkInit(args);
  await preflightChecks(parser, args, throwInsteadOfExit);
  //输出欢迎信息
  await logStartupInfo(parser, args);
  //注册接口路由,参见(appium-base-driver\lib\jsonwp\Mjsonwp.js)
  let router = getAppiumRouter(args);
  //express server类(appium-base-driver\lib\express\server.js)
  //将注册的路由，传递给express注册.
  let server = await baseServer(router, args.port, args.address);
  try {
    //是否为appium grid的node节点
    if (args.nodeconfig !== null) {
      await registerNode(args.nodeconfig, args.address, args.port);
    }
  } catch (err) {
    await server.close();
    throw err;
  }

  process.once('SIGINT', async function () {
    logger.info(`Received SIGINT - shutting down`);
    await server.close();
  });

  process.once('SIGTERM', async function () {
    logger.info(`Received SIGTERM - shutting down`);
    await server.close();
  });

  logServerPort(args.address, args.port);

  return server;
}
...
//路由
//appium.js,下面会讲解路由解析
function getAppiumRouter (args) {
  let appium = new AppiumDriver(args);
  return routeConfiguringFunction(appium);
}

```

#### URL路由解析 #### 

上面的总揽可以看到基本流程如下：   

```
parse arguments -> register route -> process route
```

我们看一下命令的具体执行逻辑(appium-base-driver\lib\mjsonwp\Mjsonwp.js):   


```javascript

function routeConfiguringFunction (driver) {
  if (!driver.sessionExists) {
    throw new Error('Drivers used with MJSONWP must implement `sessionExists`');
  }
  if (!(driver.executeCommand || driver.execute)) {
    throw new Error('Drivers used with MJSONWP must implement `executeCommand` or `execute`');
  }
  // return a function which will add all the routes to the driver
  return function (app) {
    //[METHOD_MAP](#route_config),是所有的路由配置,key为path,value为method的数组
   //对METHOD_MAP的配置进行绑定
    for (let [path, methods] of _.toPairs(METHOD_MAP)) {
      for (let [method, spec] of _.toPairs(methods)) {
        // set up the express route handler
        buildHandler(app, method, path, spec, driver, isSessionCommand(spec.command));
      }
    }
  };
}
//路由绑定
//示例：
/*
  '/wd/hub/session': {
    POST: {command: 'createSession', payloadParams: {required: ['desiredCapabilities'], optional: ['requiredCapabilities', 'capabilities']}}
  },
即:
method: POST
path: /wd/hub/session
spec: array
driver: appium
*/
function buildHandler (app, method, path, spec, driver, isSessCmd) {
  let asyncHandler = async (req, res) => {
    let jsonObj = req.body;
    let httpResBody = {};
    let httpStatus = 200;
    let newSessionId;
    try {
      //判断是否是创建session命令(包含createSession,getStatus,getSessions) 
      //是否有session
      if (isSessCmd && !driver.sessionExists(req.params.sessionId)) {
        throw new errors.NoSuchDriverError();
      }
      //设置了代理则透传
      if (isSessCmd && driverShouldDoJwpProxy(driver, req, spec.command)) {
        await doJwpProxy(driver, req, res);
        return;
      }
      //命令是否支持
      if (!spec.command) {
        throw new errors.NotImplementedError();
      }
      //POST参数检查 
      if (spec.payloadParams && spec.payloadParams.wrap) {
        jsonObj = wrapParams(spec.payloadParams, jsonObj);
      }
      if (spec.payloadParams && spec.payloadParams.unwrap) {
        jsonObj = unwrapParams(spec.payloadParams, jsonObj);
      }
      checkParams(spec.payloadParams, jsonObj);
      //构造参数
      let args = makeArgs(req.params, jsonObj, spec.payloadParams || []);
      let driverRes;
      if (validators[spec.command]) {
        validators[spec.command](...args);
      }
      //!!!!执行命令
      //捕获返回值
      if (driver.executeCommand) {
        driverRes = await driver.executeCommand(spec.command, ...args);
      } else {
        driverRes = await driver.execute(spec.command, ...args);
      }

      // unpack createSession response
      if (spec.command === 'createSession') {
        newSessionId = driverRes[0];
        driverRes = driverRes[1];
      }
      ...
    } catch (err) {
      [httpStatus, httpResBody] = getResponseForJsonwpError(actualErr);
    }
    if (_.isString(httpResBody)) {
      res.status(httpStatus).send(httpResBody);
    } else {
      if (newSessionId) {
        httpResBody.sessionId = newSessionId;
      } else {
        httpResBody.sessionId = req.params.sessionId || null;
      }

      res.status(httpStatus).json(httpResBody);
    }
  };
  // add the method to the app
  app[method.toLowerCase()](path, (req, res) => {
    B.resolve(asyncHandler(req, res)).done();
  });
}
```


### 创建session 与 executeCommand ###

(lib\appium.js) 

上面说了`appium server`已经启动了，第一件事情，当然是创建***session***,然后把command交给这个session的不同driver去执行了。  
appium先根据caps进行session创建（`getDriverForCaps`）,然后保存InnerDriver到当前session,以后每次执行命令(executeDCommand)会判断是否为appiumdriver的命令，不是则转给相应的driver去执行命令(android,ios等)。  


```
  async createSession (caps, reqCaps) {
    caps = _.defaults(_.clone(caps), this.args.defaultCapabilities);
    let InnerDriver = this.getDriverForCaps(caps);
    this.printNewSessionAnnouncement(InnerDriver, caps);

    if (this.args.sessionOverride && !!this.sessions && _.keys(this.sessions).length > 0) {
      for (let id of _.keys(this.sessions)) {
        log.info(`    Deleting session '${id}'`);
        try {
          await this.deleteSession(id);
        } catch (ign) {
        }
      }
    }

    let curSessions;
    try {
      curSessions = this.curSessionDataForDriver(InnerDriver);
    } catch (e) {
      throw new errors.SessionNotCreatedError(e.message);
    }

    let d = new InnerDriver(this.args);
    let [innerSessionId, dCaps] = await d.createSession(caps, reqCaps, curSessions);
    this.sessions[innerSessionId] = d;
    this.attachUnexpectedShutdownHandler(d, innerSessionId);
    d.startNewCommandTimeout();

    return [innerSessionId, dCaps];
  }
    async executeCommand (cmd, ...args) {
    if (isAppiumDriverCommand(cmd)) {
      return super.executeCommand(cmd, ...args);
    }

    let sessionId = args[args.length - 1];
    return this.sessions[sessionId].executeCommand(cmd, ...args);
  }

```

在basedriver中`executeDCommand`其实是调用类的`cmd`定义的方法。

### android 执行命令 ###

我们以`uiautomator2`(\appium-uiautomator2-driver\build\lib)为例看一下它的`cmd`执行情况。    

以`getAttribute`(appium-uiautomator2-driver\lib\commands\element.js)为例说明：  


```
commands.getAttribute = async function (attribute, elementId) {
  return await this.uiautomator2.jwproxy.command(`/element/${elementId}/attribute/${attribute}`, 'GET', {});
};
```

***appium 通过adb forward将主机的HTTP请求转发到设备中***   

```
await this.adb.forwardPort(this.opts.systemPort, DEVICE_PORT);
//主机端口号:8200,8299
//设备端口号：6790
```

(未完待续)



### 附录 ### 

<h4 id="parser">lib/Parser.js</h4>     

参数解析，概览：   

```
import fs from 'fs';
import path from 'path';
import _ from 'lodash';
import { ArgumentParser } from 'argparse';
import pkgObj from '../../package.json';  // eslint-disable-line import/no-unresolved


const args = [
  [['--shell'], {
    required: false,
    defaultValue: null,
    help: 'Enter REPL mode',
    nargs: 0,
    dest: 'shell',
  }],

  [['--reboot'], {
    defaultValue: false,
    dest: 'reboot',
    action: 'storeTrue',
    required: false,
    help: '(Android-only) reboot emulator after each session and kill it at the end',
    nargs: 0,
  }],
...

updateParseArgsForDefaultCapabilities
...
```

<h4 id="selenium_grid">appium node grid</h4>   

```
appium --nodeconfig /path/to/nodeconfig.json
``` 

指定nodeconfig参数以支持selenium grid,nodeconfig.json例子：   

```
{
  "capabilities":
      [
        {
          "browserName": "<e.g._iPhone5_or_iPad4>",
          "version":"<version_of_iOS_e.g._7.1>",
          "maxInstances": 1,
          "platform":"<platform_e.g._MAC_or_ANDROID>"
        }
      ],
  "configuration":
  {
    "cleanUpCycle":2000,
    "timeout":30000,
    "proxy": "org.openqa.grid.selenium.proxy.DefaultRemoteProxy",
    "url":"http://<host_name_appium_server_or_ip-address_appium_server>:<appium_port>/wd/hub",
    "host": <host_name_appium_server_or_ip-address_appium_server>,
    "port": <appium_port>,
    "maxSession": 1,
    "register": true,
    "registerCycle": 5000,
    "hubPort": <grid_port>,
    "hubHost": "<Grid_host_name_or_grid_ip-address>"
  }
}
```

指定了hubPort和Host之后就，匹配capabilities之后就可以会在node节点执行了。   



<h4 id="route_config">路由配置(appium-base-driver\lib\mjsonwp\routes.js)</h4>   

```
  const METHOD_MAP = {
  '/wd/hub/status': {
    GET: {command: 'getStatus'}
  },
  '/wd/hub/session': {
    POST: {command: 'createSession', payloadParams: {required: ['desiredCapabilities'], optional: ['requiredCapabilities', 'capabilities']}}
  },
  '/wd/hub/sessions': {
    GET: {command: 'getSessions'}
  },
  '/wd/hub/session/:sessionId': {
    GET: {command: 'getSession'},
    DELETE: {command: 'deleteSession'}
  },
  '/wd/hub/session/:sessionId/timeouts': {
    POST: {command: 'timeouts', payloadParams: {required: ['type', 'ms']}}
  },
  '/wd/hub/session/:sessionId/timeouts/async_script': {
    POST: {command: 'asyncScriptTimeout', payloadParams: {required: ['ms']}}
  },
  '/wd/hub/session/:sessionId/timeouts/implicit_wait': {
    POST: {command: 'implicitWait', payloadParams: {required: ['ms']}}
  },
  '/wd/hub/session/:sessionId/window_handle': {
    GET: {command: 'getWindowHandle'}
  },
...
```






###　参考资料 ###

[https://github.com/appium/appium/blob/master/CONTRIBUTING.md](https://github.com/appium/appium/blob/master/CONTRIBUTING.md)   
[https://github.com/appium/appium/blob/master/docs/en/contributing-to-appium/appium-packages.md](https://github.com/appium/appium/blob/master/docs/en/contributing-to-appium/appium-packages.md)   
[https://github.com/appium/appium/blob/master/docs/en/advanced-concepts/grid.md](https://github.com/appium/appium/blob/master/docs/en/advanced-concepts/grid.md)   
[https://github.com/fool2fish/selenium-doc](https://github.com/fool2fish/selenium-doc)   

