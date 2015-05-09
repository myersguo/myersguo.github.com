---
layout: wp
title: get the next
comments: true
---

哈哈，花了连个小时把autouitest的v0.1弄出来了，感觉不错，先出去吃个饭，回来写xxxxxx:

following is my auto uitest structure
![my auto ui test ](/public/autoui.png)  

后补： 刚从云盘上download下来之前实习的已加公司，用C语言写的与PHP交互的工具。代码看上又幼稚、又性感啊.

>    
 	int or_xml_getter::get_text(const char * value,char * text,int num)    
	{    
		if ( !flag ) return -1;    
		TiXmlElement * root_element = doc->RootElement();    
		if (root_element == NULL ||  strcmp(root_element->Value(),"root") !=0 )    
			return -1;    
		TiXmlNode * valuenode = doc->FirstChild("root")->FirstChild(value);    
		if ( valuenode == NULL)return -1;    
		TiXmlElement * value_element = valuenode->ToElement();    
		if (NULL == value_element)return -1;    
		   
		   
		if(value_element->GetText()!= NULL)       
			strncpy(text,value_element->GetText(),num);// note,this may overflow       
		else      
			strcpy(text,"\0");       
		return 1;       
	}    
	
	









