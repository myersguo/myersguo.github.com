---
layout: wp
title: jquery form seriaslize
---

今天在处理form表单提交时，使用jquery的form serialize，结果有一些数据没有出来。揪出jquery的源码看一下：  

```
jQuery.fn.extend( {
    serialize: function() {
        return jQuery.param( this.serializeArray() );
    },
    serializeArray: function() {
        return this.map( function() {

            // Can add propHook for "elements" to filter or add form elements
            var elements = jQuery.prop( this, "elements" );
            return elements ? jQuery.makeArray( elements ) : this;
        } )
        .filter( function() {
            var type = this.type;

            // Use .is( ":disabled" ) so that fieldset[disabled] works
            return this.name && !jQuery( this ).is( ":disabled" ) &&
                rsubmittable.test( this.nodeName ) && !rsubmitterTypes.test( type ) &&
                ( this.checked || !rcheckableType.test( type ) );
        } )
        .map( function( i, elem ) {
            var val = jQuery( this ).val();

            return val == null ?
                null :
                jQuery.isArray( val ) ?
                    jQuery.map( val, function( val ) {
                        return { name: elem.name, value: val.replace( rCRLF, "\r\n" ) };
                    } ) :
                    { name: elem.name, value: val.replace( rCRLF, "\r\n" ) };
        } ).get();
    }
} );
```

先看第一个： 
var elements = jQuery.prop( this, "elements" );  
此语句相当于：  
$("form").prop("elements")  
即：  
document.forms[0].elements  

这样，就获得了所有的控件，继续看filter:  
filter实际上执行的:

```
// Implement the identical functionality for filter and not
function winnow( elements, qualifier, not ) {
    if ( jQuery.isFunction( qualifier ) ) {
        return jQuery.grep( elements, function( elem, i ) {
            /* jshint -W018 */
            return !!qualifier.call( elem, i, elem ) !== not;
        } );

    }

    if ( qualifier.nodeType ) {
        return jQuery.grep( elements, function( elem ) {
            return ( elem === qualifier ) !== not;
        } );

    }

    if ( typeof qualifier === "string" ) {
        if ( risSimple.test( qualifier ) ) {
            return jQuery.filter( qualifier, elements, not );
        }

        qualifier = jQuery.filter( qualifier, elements );
    }

    return jQuery.grep( elements, function( elem ) {
        return ( indexOf.call( qualifier, elem ) > -1 ) !== not;
    } );
}
```

即，grep filter的函数返回的为true的元素。  
因此，serialized筛选的条件是：  
*** 有一个name的属性 *** 
*** 没有disabled ***  

看到这里，已经知道为什么我的form表单中缺少一些元素了，因为，有的表单中不包含name,被filter过滤掉了。那么jquery为什么这么设计呢？  
在w3的[标准文档中](https://www.w3.org/TR/html401/interact/forms.html#h-17.2),写道：  

>A control's "control name" is given by its name attribute. The scope of the name attribute for a control within a FORM element is the FORM element.  

这个原因？  











