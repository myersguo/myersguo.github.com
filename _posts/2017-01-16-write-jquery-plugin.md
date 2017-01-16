---
layout: wp
title: How to writejquery plugin——译AND想
---

### 基本插件开发 ###  

最简单的插件开发就是对`$.fn`添加新的成员函数。   

```
$.fn.插件名 = function() {
    //插件代码
};
```

eg:     

```
$.fn.greenify = function () {
    this.css( "color", "green" );
};
$("a").greenify(); // Makes all links green.
```

注意：插件中的`this`是jQuery对象的实例。   

### 链式调用(Chaining) ###   

jquery的一大特点是链式调用。更改上面的插件方法:   

```
$.fn.greenify = function () {
    this.css( "color", "green" );
    return this;
};
$( "a" ).greenify().addClass("greenified");
```

为防止`$`符号同其他的冲突，可以放到[立即执行函数](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)中写：   

```
(function ( $ ) {
 
    $.fn.greenify = function() {
        this.css( "color", "green" );
        return this;
    };
 
}( jQuery ));
```


另外，** 尽量只使用一次`$.fn`**   

### 使用.each ###   

如果需要针对每一个DOM元素进行遍历、处理。则可以使用.each 方法：  


```
$.fn.myNewPlugin = function () {
    return this.each (function() {
        //Do something to each element here
    });
};
```


### 传递参数 ###    


```
(function ( $ ) {
 
    $.fn.greenify = function( options ) {
 
        // This is the easiest way to have default options.
        var settings = $.extend({
            // These are the defaults.
            color: "#556b2f",
            backgroundColor: "white"
        }, options );
 
        // Greenify the collection based on the settings variable.
        return this.css({
            color: settings.color,
            backgroundColor: settings.backgroundColor
        });
 
    };
 
}( jQuery ));

$( "div" ).greenify({
    color: "orange"
});
```

//一般定义默认的参数settings，然后使用$.extend({}, defaultOptions, options)来传参。   

### 参数可修改 ###   

为了扩展插件，有时候需要直接修改默认参数，来方便插件的调用。   

```
// Plugin definition.
$.fn.hilight = function( options ) {
 
    // Extend our default options with those provided.
    // Note that the first argument to extend is an empty
    // object – this is to keep from overriding our "defaults" object.
    var opts = $.extend( {}, $.fn.hilight.defaults, options );
 
    // Our plugin implementation code goes here.
 
};
 
// Plugin defaults – added as a property on our plugin function.
$.fn.hilight.defaults = {
    foreground: "red",
    background: "yellow"
};
$.fn.hilight.defaults.foreground = "blue";
$( "#myDiv" ).hilight();
$( "#green" ).hilight({
    foreground: "green"
});
```

### 方法可修改 ###   

另一种扩展插件的方式是将插件的方法暴漏出去。   

### 实践，写一个插件吧 ###   

```
(function () {
    var formatTimer = function (time, format) {
      if(!time)time = new Date();
      time = Number(time);
      time = time.toString().length === 10 ? time*1e3 : time;
      var addZero = function (number) {
          var num = number.toString();
          return num.length == 1 ? ('0' + num).slice(-2) : num;
        },
        d = new Date(time),
        year = d.getFullYear(),
        month = d.getMonth()+1,
        day = d.getDate(),
        hour = d.getHours(),
        minute = addZero(d.getMinutes()),
        second = addZero(d.getSeconds()),
        formatObj =  {
          '%y': year,
          '%m': month,
          '%d': day,
          '%h': hour,
          '%i': minute,
          '%s': second
        };
 
      if (!format) {
        return year + "-" + month + "-" + day + " " + hour + ":" + minute + ":" + second;
      } else {
        return format.replace(/(\%\w)/g, function(key) {
          return formatObj[key];
        });
      }   
    };

    $.fn.formatDateTime = function (settings) {
        if(settings){
          settings = $.extend({}, settings);
        }
        this.each(function(){
              date = $(this).text();
              $(this).text(formatTimer(date, settings));
            }
        });
        return this;
    };

    

}());
```
上面是对DOM进行时间的格式化显示.  



### 参考资料 ###  

[https://learn.jquery.com/plugins/basic-plugin-creation/](https://learn.jquery.com/plugins/basic-plugin-creation/)  
[http://javascript.ruanyifeng.com/jquery/plugin.html](http://javascript.ruanyifeng.com/jquery/plugin.html)   
