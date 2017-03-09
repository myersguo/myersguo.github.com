---
layout: wp
title: Java interview
---

一个很「扯淡」的`java`面试题，如下：    

```
public class main{

    public static void test() {
        Integer a = 15;
        Integer b = 132;
        Integer[] c = {15,132};
        
    
        for(Integer i: c) {
            if(i == a){
                System.out.println("a is in c");
            }else if(i == b){
                System.out.println("b is in c");
            }
            if(i.equals(b)){
                System.out.format("b equal %s\n" ,i);
            }
        }

        System.out.println(a==15);
        System.out.println(b==132);

    }

    public static void main(String[] argv) 
    {

        main t= new main();
        t.test();
    }
}
```


请问输出什么呢？    

结果是：   


```
a is in c
b equal 132
true
true
```

(具体原因，请google)   


