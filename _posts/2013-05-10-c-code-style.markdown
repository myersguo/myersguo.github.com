---
layout: wp
title: c code style
comments: true
blogindex: false
---
## NASA C STYLE GUIDE  
本文简译自:nasa c code style:[http://sdrv.ms/19aCvG8](http://sdrv.ms/19aCvG8)  

### "good style"代码  

+ 有组织的 Organized  
+ 易读的  Easy to read  
+ 易懂的  Easy to understand  
+ 易维护  Maintainable  
+ 有效的  Efficient

本文将研究的讨论的是C程序、文件、函数的组织，及数据结构、变量、表达式、注视的组织方式，提供好的变成方式的指南。  

### 可读，易维护 READABILITY AND MAINTAINABILITY  

+ 使用封装和信息隐藏技术组织程序  
+ 使用空白符增强可读性  
+ 添加注释方便他人理解  
+ 使用有意义的可读的名称  
+ 可行时遵守ANSI C标准

#### 1. 使用封装和信息隐藏技术组织程序  

封装：  


>将程序组织为多个文件，例如用头文件封装单个观点  
>将文件组织为数据段和函数段。  
>将函数组织为逻辑相关的单独文件。  
>将数据组织为逻辑分组（数据结构）  


**信息隐藏**指程序元素的可见性，可以使用C语言中的函数和数据的有效范围进行控制：  


>封装相关的信息到头文件，当且仅当需要头文件时包含之。如当需要计算时间时，插入\#include<time.h>。  
>在当前文件之外定义的变量为external变量。一个external变量仅在声明extern时对函数可见。external声明最好声明在单独的函数中。  


#### **空白符**    

添加空白符，如空白行，空格，缩进，将有效提高代码的可读性。  


+ 空白行  

恰当地使用空白行能通过标记逻辑序列，加强**代码段**的可读性。在代码中，用空白行创建代码段落，或使用注释能够让程序更可读。  

然而，过多使用空白行使程序变得不可读。因此，使用一个空白行来区分代码段落。  

+ 空格

适当空格可以加强“词汇”（如，变量，运算符）间的可读性。下例中第二个例子"/"与"\*"结合组成注释符，因此用空白符分开。第三个例子，逗号后使用空白符增加可读性。


>good spacing  
>`*average = *total / *count; /* compute the average */`  
>  
>poor spacing  
>`*average=*total/*count; /*compute the average */`  
>  
>comma spacing  
>`concat(s1, s2)`


+ 缩进  

缩进可显示代码的逻辑结构。研究标明，4个空格是最适合阅读和管理的缩进。但使用长变量名的高度嵌套的代码可能导致超过行长。  


#### 注释

明智的使用注释能让人不用只通过代码来阅读。注释可用不同方式添加：  


>+ 在程序顶层，添加README提供程序的一般描述和组织结构描述。  
>+ 在文件层(中)，包含文件的开场白("file prolog")解释文件的目的和其他信息。  
>+ 在函数层(中)，用注释做为函数开场白("function prolog")  
>+ 在整个文件中，当有变量声明或定义时，添加注释解释变量的目的。  


下面描述了注释的使用和提供了例子。


+ 盒注释 boxed comment ,使用prologs或做为块分割。  
+ 块注释 block comment ,用在代码的每个主要部分的开头做为代码部分的描述  
+ 短注释 short comment ,在同一行写做为代码或数据描述    
+ 行内注释 inline comment ,  


例子： boxed comment prolog

<pre>
`
/*************************************

* FILE NAME *

* *

* PURPOSE *

* *

**************************************/
`
</pre>

例子： section separator

<pre>
`
/**********************************/
`
</pre>

例子： block comment

<pre>
`
/*

* Write the comment text here, in complete sentences.

* Use block comments when there is more than one

* sentence

*/
`
</pre>

例子： short comment

<pre>
`
double iee_r[]; /* array of IEEE real*8 values */

unsigned char ibm_r[]; /*string of IBM real*8 values */

int count; /* number of real*8 values */
`
</pre>


+ 使用TAB使注释远离代码
+ 多个代码块连续出现的注释区，使用TAB使注释左对齐和右对齐。


例子: inline comment

<pre>
`
switch (ref_type)

{

/*Perform case for either s/c position or velocity

* vector request using the RSL routine c_cal pvs */

case 1:

case 2:

...

}
`
</pre>


一般在变量定义中使用短注释，在过程计算中使用块注释。例如:

<pre>
`
/* 建议：*/

/*

*Main sequence: get and process all user requests

*/

while (!finish())

{

inquire();

process();

}

/*而非： */

while (!finish()) /* Main sequence: */

{ /* */

inquire(); /* Get user request */

process(); /* As long as possible */

}
`
</pre>


#### **有意义的名称**

文件，函数，常量，变量的名称应该有“意义”(Meaningful)。以下是一些参考：


+ 选择没有歧义的名称，并在整个程序中含义一致。  
+ 缩写词遵照统一方案来表示带有前缀的名称。如一些列"data refresher"函数，可使用"dr_"做为前缀。  
+ 避免使用能够组词的造成误解的缩写。例如，"inch"做为"input character"会被误解为词'inch'，使用"in_char"会更好。  
+ 使用长名称提高可读性。但过长使程序结构变得难理解，缩进不方便。 
+ 名称的4个字母中至少应有2个不同。如"systst"和"sysstst"容易混淆。在相似名称里加下划线区分。如  


systst sys_tst  
sysstst sys_s_tst  

+ 不用用大小写区分名称。如"LineLength"与"linelenght"，所有名称应该在大小无关下是不同的。  
+ Do not assign a variable and a typedef(or struct)with the same name,even through C allow this.This type of redundacy can make
the program difficult.  


##### 标准名

下面是一些标准的短名称。当这些名称含义清晰时，推荐使用。但我们推荐使用长的有意义的名称。如"buffer_index."


例子：标准短名称:

>c characters  
>i,j,k indices  
>n counters  
>p,q pointers  
>s strings  


例子： 标准后缀

>_ptr pointer  
>_file variable of type file*  
>_fd file descriptor  


##### 变量名

不要在函数内部使用和全局变量名相同的变量名。使用相同的变量名产生"隐藏变量"，导致函数不按预想工作。下面的例子，内部变量"total"和全局变量重名，正确的方法应该重命名。  

<pre>
`
int total;

int funcl(void)

{

float total; /* this is a hidden variable,should rename to grand_total or others */

}
`
</pre>


>待补此处
>In separate functions, variables that share the same name can be declared. However,the identical name should be used only when the variables also have the identical meaning. When the meanings of two variables are only similar or coincidental, use
unique names to avoid confusion.  


##### 大小写规定


+ 变量：小写字母单词，用下划线分割。
+ 函数名：首字母大写，不适用下划线。
+ 常量：大写字母，用下划线分割。
+ C bingdings：以"c_"开头表示"C binding"


例子：capitalization style


> open_database variables  
> ProcessError function names  
> MAX_COUNT constants  
> c_ephemrd C bindings


##### type and constant names

枚举类型和常量命名方式相同。


#### 程序组织

讨论如何将程序组织为多个文件。如把逻辑相关的函数和数据结构放入相同文件，并控制文件内容的可见性。

![program organization](http://media-cache-ak1.pinimg.com/736x/b2/af/b8/b2afb84a7edef886ecd639329051a359.jpg)


##### 程序文件

C语言程序包含一个或多个程序文件(program files)，其中一个包含main函数，做为程序的驱动者(acts as the driver of the program)。当你的程序由多个文件时，需要使用封装和数据隐藏技术来组织逻辑相关的函数和数据结构到同一文件中：


> + 创建README描述程序  
> + 将main函数和其他逻辑相关的函数放到一个文件  
> + 使用模块文件分组逻辑关联的函数(不包含main函数)  
> + 使用头文件封装关联的变量、函数的定义和声明  
> + 使用Makefile用于编译和处理


#### README

README文件应该用于解释程序做什么，怎么组织的，通常包括：


+ 所有编译条件和标记及含义  
+ 平台依赖的文件  
+ 重用的组件的路径  


#### 标准库文件

标准库是把一些列常用的函数的集合文件。比如，"stdio.h"包含一些列输入/输出函数，"math.h"包含一系列数据函数。当使用这些库文件时，仅仅包含需要的文件。你可能选择你自己的库文件并组织他们到头文件中。


#### 头文件

头文件封装逻辑相关的功能。比如，"time.h"定义了2个常量，3种类型，3个structures,声明了7个处理时间的函数。头文件只有当被需要时才有选择性的被包含到程序文件中。


头文件在编译之前预加入源码中。比如，"stdio.h"的范围为这个系统，必须被C程序包含。其他的被包含在单一程序或多个程序中。


+ 使用#include**<**system_name**>** 表示系统的包含文件  
+ 使用#include**"**user_file**"**表示用户的包含文件    
+ 在头文件中包含 数据定义，声明，typedef，枚举等。这些应被多个文件引用。  
+ Organize header files by function  
+ 把不同子系统的声明放在不同的头文件中  
+ 如果声明与平台相关，将他们放到单独的头文件  
+ 不要使用和标准库函数相同的的头文件。如\#include<math.h>会在当前目录没有时包含标准库文件  
+ 在**定义**函数和变量的文件包含**声明***函数和变量的头文件  
+ 不要将头文件扎堆(do not nest header files)，使用明确的包含语句去包含需要的头文件  
+ 在头文件中描述其他需要包含的头文件的功能


#### 模块文件

模块文件是逻辑相关的函数，变量，类型，数据定义和声明及函数。模块文件和程序文件类似，但模块文件没有main函数。


#### Makefiles

Makefiles在一些平台中用于编译C代码。使用makefiles能够监测编辑改动进行重新编译，它能够存储变量，使长的编译命令变短。


#### 标准文件名后缀

建议源码文件的格式为：可选的前缀（如，表明子系统）+基本名称+可选日期和后缀。

基本名应该是唯一的（长度限制于计算机平台)，包含一个基本的后缀表明文件的类型。一些编译工具需要明确的后缀：


>文件类型-----标准后缀  
>c source file .c  
>Assembler source .s  
>Relocatable object .o  
>Include header .h  
>Yacc source .y  
>Lex source .l  
>Loader output file .out  
>Makefile .mak  
>Linker response files .lnk or .rsp


### 文件组织

![file organization schema](http://media-cache-ak1.pinimg.com/736x/d1/0e/2e/d10e2ed6daef804c59e1b5835bb340ae.jpg)


#### 文件头(File Prolog)

每个文件必须包含文件头。例子：

![program file prolog contents](http://media-cache-ec3.pinimg.com/736x/1a/bb/fa/1abbfae0e547723c945df55f1e7720ae.jpg)


+ File Name：文件的独特名称  
+ Purpose:简单描述单元的作用  
+ File References:用"name","IO","description"三项进行描述，无则标"none"。  
+ External Variables:用"Source","Name","Type","I/O","Description"描述。  
+ External References:"Name","Discription"描述。  
+ Abnormal Termination Conditions,Error,Warning Messages：  
+ Assumptions,Constraints,Restrictions:假设与限定  
+ Notes:    
+ Requirements/Functional Specifications References:  
+ Development History:时间，作者，模块，版本，更改描述。  
+ Algorithm(PDL):算法描述  


![header file prolog](http://media-cache-ec4.pinimg.com/736x/5f/fa/f3/5ffaf3d0bd3ac95c5ffd84493dab55e1.jpg)


#### PDL

PDL通过使用英语语句和简单控制命令描述应用单元的逻辑控制过程。


+ 使用4个空格进行缩进  
+ 在每个控制结构中，使用对齐（如IF,ELSE等），对齐每一对embedded statement  
+ 单个PDL语句跨行时，开始新行和上一行的右侧多一个空格  

PDL包含四种基本的类型：


+ 顺序  
+ 选择  
+ 迭代  
+ 错误与异常  


##### 顺序

`verb object  

C = A + B  

CALL <unit name>  

RETURN  `


#### 选择  

基本的if then语句：  

`IF condition THEN  
  
true processing  

ELSE  
 
false processing  

ENDIF

PDL：

IF shuttle and payload mode THEN

CALL addstr to display shuttle title

ELSE IF freeflyer only mode THEN

call addstr to display ff title

ELSE

CALL addrstr to display both titles

ENDIF

C CODE:

if (objdisp == SHUT_PAYLOAD)

addrstr("SHUTTLE DATA");

else if (objdisp == FF)

addstr("FREEFLYER DATA");

else

addstr("SHUTTLE/FF DATA");

CASE：

DO CASE of axes color

black:

set color to black

yellow:

set color to yellow

red:

set color to red

OTHERWISE:

ENDDO CASE
`

##### 迭代  

`DO WHILE ui buffer not empty

CALL process_ui issue requests

ENDDO WHILE

DO FOR each axis view (X, Y, Z)

CALL setview to create view

ENDDO FOR

DO UNTIL no ui requests remain

CALL process_ui to issue requests

ENDDO UNTIL`  


#### 异常  


`DO WHILE more records remain to be processed

read next record from file

IF an invalid record is encountered

ABORT to INV_REC_FND //UNDO

ENDIF

(process this record)

ENDDO WHILE

...

RETURN

INV_REC_FND:

inform user of the invalid record just found

set invalid record indicator

RETURN`  

#### 头文件  

	#include <stdio.h>(or stdlib.h)  

	#include <other system headers>  

	#include "user header files"  


#### 定义

在包含所有需要的头文件之后，定义变量，类型，宏。按以下顺序：


+ Enums  
+ Typedefs  
+ Constant maros(#define identifier token-string)  
+ Function maros(#define identifier(identifier,...,identifier)token-string)  


#### 外部数据的声明和定义

在定义了变量，类型，宏之后是外部变量的声明区域。按以下顺序：


+ 外部变量定义的声明  
+ 非静态的外部定义  
+ 静态的只在本文件使用的外部定义  


#### 函数列表

+ 如果文件有main函数，main函数应为第一个出现  
+ 在同一文件放置逻辑相关的函数  
+ 函数的存放顺序按照有意义的顺序:  

  - **宽度优先**（函数在相似层级）比深度优先（函数有调用关系）更加approach  
  - 如果定义了很多独立的函数，使用按**字母排序**的顺序  
  
+ 使用一行星号分割函数，提高可读性    
+ 把函数放到最后，除非使用外部变量申明  


### 函数组织

![Function Organization Schema](http://media-cache-ak0.pinimg.com/736x/28/2b/36/282b36ba83ec22d4cfe82df6d3948a29.jpg)  


#### Function Prologs  

每一个函数应该有一个函数说明：


+ Function name  

 - 一个或多个小写字母，由下划线分割单词  
 - 如果名字包含专有名词，可用大写(e.g.,Gaussian_distribution)  
 - 附加简短的描述信息  
 
+ Arguments 每行写type,I/O,简短描述  
+ Return value 描述函数的返回  


<pre>

`

/**********************************************

* *

* FUNCTION NAME: *

* ARGUMENTS: *

* ARGUMENT TYPE I/O DESCRIPTION *

*-------- ---- --- ------------ *

* *

*RETURNS: *

* *

***********************************************/
`
</pre>


如果函数的返回值是『非布尔型』或void，函数名应该为一个命令式动词，来描述函数的行为；或一个名词短语。如:   

`
obtain_next_token

increment_line_counter

top_of_stack

sensor_reading  
`

如果函数的返回值是一个布尔型，函数名应为待断言的短语，如： 
`

stack_is_empty

file_is_saved

#### Function Arguments
`  

<pre>`int getline (char *str, int length)

{

..

}`</pre>


#### External Variable Declarations

在函数开始处，立即声明外部变量

<pre>

`

char *save_string(char *string)

{

extern char *malloc();

...

}

`

</pre>


#### Internal Variable Declarations

内部变量-即仅被函数使用（也叫局部变量）-应该在外部定义之后定义：


+ 对齐内部变量的声明，让其第一个字母在相同的列  
+ 声明每一个内部变量于单独行，并附上详细注释  
  - 例外：当使用循环索引(loop indices)时，在同一行，并用一个注释  
+ 如果一组函数使用**相同**的参数或内部变量，在每一个函数中使用**相同**的名称。  
+ 避免override变量


#### Statement Paragraphing

用空白行分割声明组或代码块。添加inline comments解释函数的部分代码,如：

<pre>

` char *save-string(char *string)

{

register char *ptr;

/*

* if allocation of input string is successful,

* save the string and return the pointer; otherwise,

* return null pointer.

* /

if((ptr = (char *) malloc(strlen(string) + 1)) !=

(char *) NULL)

strcpy(ptr, string);

return(ptr);
}

`

</pre>


#### Return Statement

return (expression)

+ 使用expression可提高代码效率，但过度使用会导致调试困难
+ 不推荐在一个函数中使用多个return和exit退出，除非程序复杂度较高，且不会难以维护
+ 必须声明函数类型。不要总是使用int做为默认，如果没有返回值，就用void
+ 在函数最后使用一个单独一行return
+ The single-return structure is easier to change. If there is more to do after a search,just add the statement(s) between the for loop and the return.  
 

<pre>
	single return:  
	found = FALSE;  
	
	for (i=0; i<max && !found ; i++)
	
	if (vec[i] == key )
	
	found = TRUE;
	
	return(found);
	
	multiple returns:
	
	for (i=0 ; i<max ; i++)
		if (vec[i] == key])
			return (TRUE);
	return FALSE;

</pre>



### 数据类型，操作符，表达式

+ 每行定义一个变量和常量

+ 使用简短注释解释变量和常量

+ 对变量和常量分组


#### Variables

当变量为相同类型，除非变量是"self-explanatory"和相关的，否则单独一行声明，如:

	int year, month, day;

添加一个简短说明:


	int x; /* comment */  
	int y; /* comment */


将变量分组:



	int x, y, z;  
	int year, month, day;



#### Constants

使用大写常量名。在ANSI C中有以下常量表示法：

<pre>

	const int SIZE 32; /* size in inches */

	const int SIZE 16 + 16; /* both evaluate to the number 32 */  
</pre>

避免使用硬编码的数值常量和数组常量。用#define分配每一个有意义的名称和不变的值。
<pre>



	#define IDENTIFIER token-string

	#define NULL 0

	#define EOS '\0'

	#define FALSE 0

	#define TRUE 1

</pre>

使用枚举时，没一行放一个值,值左对齐。
<pre>


	enum position

	{

		LOW,

		MIDDLE,

		HIGH

	}

	enum stack_operation_result

	{

		FULL = -2,

		BAD_STACK = -1,

		OKAY = 0,

		NOT_EMPTY = 0,

		EMPTY = 1

	}
</pre>

使用const代替#define，因为#define不能传递地址的地址，仅作替换，这样可能带来错误:
<pre>  


	#define SIZE 10 + 10 /* ... */

	area = SIZE * SIZE

	const int SIZE = 10 + 10; /* ... */

	area = SIZE * SIZE;

</pre>  


参考阅读：  

[1]变量与声明的区别:http://goo.gl/9H5QW    
[2]nasa c code style:http://sdrv.ms/19aCvG8    






THE END  

  
### 附言：VIM 配置     
代码的组织艺术，还表现在编写代码和查看代码的工具上。所谓"The mechanic,who wishes to do his work well,must first sharpen his tools.".我将自己的VIM配置上传到github中。里面包括几个大家常用的插件。截图如下：  
![my vim enviroment](http://media-cache-ak1.pinimg.com/736x/20/f2/91/20f291d6e57ee829b8799e9e00df6e60.jpg)
