---
layout: wp
title: sass unicode字符编码问题续
---


### 楔子 ###   

昨天的[编码疑问](https://myersguo.github.io/2016/11/03/sass-unicode-error.html)暂时搁浅。把在我面前的是一个细节问题？***什么是编码？***,让我们一步一步的探究。   


### base64_encode ###   

php中有一个base64_encode的编码函数。`base64_encode — Encodes data with MIME base64`

```
while (length > 2) { /* keep going until we have less than 24 bits */
        *p++ = base64_table[current[0] >> 2];
        *p++ = base64_table[((current[0] & 0x03) << 4) + (current[1] >> 4)];
        *p++ = base64_table[((current[1] & 0x0f) << 2) + (current[2] >> 6)];
        *p++ = base64_table[current[2] & 0x3f];

        current += 3;
        length -= 3; /* we just handle 3 octets of data */
}
if (length != 0) {
        *p++ = base64_table[current[0] >> 2];
        if (length > 1) {
            *p++ = base64_table[((current[0] & 0x03) << 4) + (current[1] >> 4)];
            *p++ = base64_table[(current[1] & 0x0f) << 2];
            *p++ = base64_pad;
        } else {
            *p++ = base64_table[(current[0] & 0x03) << 4];
            *p++ = base64_pad;
            *p++ = base64_pad;
        }
    }
    *p = '\0';
```

即对二进制处理过程如下：   

将3个字节(24bit)化分为4组，每组是6bit,这样每组最大数就是63，这样不管是什么样的字符都能用64个字符来表示(A-Z,a-z,0-9,+/,\0)）。对于剩余的2个字节或1个字节进行补位：2个字节转换成3组6bit(6bit+6bit+[4bit+2bit])；1个字节(6bit+[2bit+4bit])，然后再进行补位转换。   

### js base64_encode ###  


我在网上找的一个js 的 base64的编码函数：  

```
function b64EncodeUnicode(str) {
    return btoa(encodeURIComponent(str).replace(/%([0-9A-F]{2})/g, function(match, p1) {
        return String.fromCharCode('0x' + p1);
    }));
}
```

解释一下：   
btoa: 将字符串进行base64编码   
encodeURIComponent:对URL进行编码(这个我们稍后说),转化成了utf-8。   
replace将%xx%xx这种字符编程十六进制返回。   

### encodeURIComponet ###   

上面说到的URL编码用的是什么呢？  
URI使用百分号编码方式。

比如我们有一个ajax请求:/search?q=小米,那么这个请求实际上是什么呢？   

`search?keyword=%E5%B0%8F%E7%B1%B3`   

其中`%E5%B0%8F%E7%B1%B3`是小米的utf-8编码.   

UIR规范规定，除了Unreserved Characters之外的字符都必须使用percent encoded,  


>需要把该字符的ASCII的值表示为两个16进制的数字，然后在其前面放置转义字符("%")，置入URI中的相应位置。(对于非ASCII字符, 需要转换为UTF-8字节序, 然后每个字节按照上述方式表示.)   

### UTF-8 ###

8个bit的二进制最多表示256个(0-0xFF)个字符。但256个哪里够？汉字、韩语、日文那么多....  

那几个字节够，2个字节能表示65,535个，3个字节(24bit)能表示(0xFFFFFF,即16,777,215)一千六百多万，应该够了。 

但英文字符用1个字节就够了，让它们也使用3个字节岂不浪费？怎么办？能不能编码长度可变，但你读取的时候(parse)聪明一点就行了？   

UNICODE是世界上所有字符的集合。(有10FFFF即4177777个），它的编码实现有很多,UTF-8就是一种。    

`UTF-8`是变长编码，

>1）对于单字节的符号，字节的第一位设为0，后面7位为这个符号的unicode码。因此对于英语字母，UTF-8编码和ASCII码是相同的。
>2）对于n字节的符号（n>1），第一个字节的前n位都设为1，第n+1位设为0，后面字节的前两位一律设为10。剩下的没有提及的二进制位，全部为这个符号的unicode码。


***UTF-8是对unicode的查找实现***    


### 编码表示 ###   

回到昨天的编码问题，编码是怎么表示的呢？   

\u unicode ?   

昨天的问题，sass中的变量是:`\f26e`,它处理当作unicode进行了专业转义。为什么呢？   

以<-为例，它的[unicode编码](http://unicode-table.com/en/21E0/)是21E0，我们在CSS中使用`\21e0`可以显示出箭头来。因此sass在对将unicode编码直接转换成了符号了。

ruby代码看不到，似乎是[这里](https://github.com/sass/sass/blob/master/lib/sass/scss/rx.rb#L52)   


[未完待续]   




名字解释： 
scheme: 协议、规格   



参考资料：  

[http://www.ruanyifeng.com/blog/2008/06/base64.html)(http://www.ruanyifeng.com/blog/2008/06/base64.html)  
[https://github.com/php/php-src/blob/master/ext/standard/base64.c)(https://github.com/php/php-src/blob/master/ext/standard/base64.c#L56)  
[http://www.ruanyifeng.com/blog/2010/02/url_encoding.html](http://www.ruanyifeng.com/blog/2010/02/url_encoding.html)  
[RFC1738](http://www.ietf.org/rfc/rfc1738.txt)   
[RFC3986](https://tools.ietf.org/rfc/rfc3986.txt)  
[百分号编码,percent-encoding,url encoding](https://en.wikipedia.org/wiki/Percent-encoding)  
[unicode 编码查询](http://unicode-table.com/)   
[UTF-8](https://en.wikipedia.org/wiki/UTF-8)   
[http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)   


