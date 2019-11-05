---
layout: wp
title: log
comments: true
---

一般 logger 会包括以下几个部分：  

* logger   
* handler   
* formatter   

```
import logging
logger = logging.getLogger("my_log")
handler = logging.StreamHandler()
logger.addHandler(handler)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)
logger.warn("hi")
```

执行上面的代码会输出：   

`2019-11-04 15:47:49,656 - my_log - WARNING - hi`   

类似的 java 也是如此：   

```
   Logger logger = Logger.getLogger("my_log");
   logger.setUseParentHandlers(false);
   ConsoleHandler handler = new ConsoleHandler();
   handler.setFormatter(new SimpleFormatter());
   logger.addHandler(handler);
   logger.info("hi");
```

golang:   

golang 官方并没有提供具体的 `handler` 方法。需要自定义 `file handler`, `console handler` 。    


php(log4php):   

```
include('Logger.php');
$logger = Logger::getLogger("main");
$logger->info("This is an informational message.");
```


