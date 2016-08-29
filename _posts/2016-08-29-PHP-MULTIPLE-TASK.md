---
layout: wp
title: PHP API批量操作
---
按：
>代码写了一半发现架构有问题
>正确的设计是不断的修正架构的细节中得来的。   


在使用PHP处理多个任务的过程中，我对此场景有所疑问，边写边解惑一下：  
场景： 
一个API中需要重复针对每项内容做相同校验，类似：  

```
function genResult($code, $data)
{
    return array(
        'code' => $code,
        'data' => $data,
    );
}
function process()
{
    $ret = array();
    for($i=0; $i<2; $i++) {
       $ret[$i] = get(); 
    }
    return genResult(0,$ret);
}
function get()
{
    return 1;
}

```

假如get的处理中有各种异常情况，get处理不能直接异常退出，而应该对各种异常有一个统一的接口定义，比如：

```
$ret = array(
    'code' => 0,
    'data' => 'ok',
);
```

假如get依赖多个第三方，仍然需要将各种返回值收敛到code,data的结构中。对此，process处理过程中，code标识者处理结果是否成功，而不管get的处理结果是否成功，因为这种多任务的处理，在交给第三方调用的时候，code为0标识处理成功，data的返回进行解包，每一个解包，继续使用code,data来判断就行了。   


