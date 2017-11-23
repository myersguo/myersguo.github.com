---
layout: wp
title: About FastText
comments: true
---

### 基础知识 ###

#### Precision_and_recall ####

这个在[维基百科](https://en.wikipedia.org/wiki/Precision_and_recall)上解释的非常好，我这里翻译一下：   


>假如图片集合中有12 张狗，其它的是其他动物。一个自动识别的程序，识别之后的结果是： 
>8张狗，其他是其他动物。  
>假如识别的8张狗中有5张是对的，那么，识别的 正确率是 5/8。
识别的 recall 值是 5/12 （12张狗，只识别了5张）。   

precision 范围: 0-1, 越大越好；   
recall 范围：0-1, 越大越好。   


#### 文本/单词的数学表示 ####

现在机器学习流行的方式是使用向量矩阵(vector)来表示

举个例子：   

```
wget https://s3-us-west-1.amazonaws.com/fasttext-vectors/cooking.stackexchange.tar.gz && tar xvzf cooking.stackexchange.tar.gz
head -n 12404 cooking.stackexchange.txt > cooking.train
tail -n 3000 cooking.stackexchange.txt > cooking.valid
```

这个下载好的数据，就是「训练集」，它是已经做好的分类数据。内容为：   

类别，  文本内容   

**文本分类** 的 **步骤** 包括：   

训练集--> 预处理 --> 特征提取（一般为向量集表示） ---> 建模 --> 使用模型进行分类   

### fasttext ###  

下面是官方的例子演练：   

训练生成模型分类器:  

>./fasttext supervised -input cooking.train -output model_cooking -epoch 25

交互式测试分类器：  

>./fasttext predict model_cooking.bin -   

用测试样本进行测试：  

>./fasttext test model_cooking.bin cooking.valid   

测试结果是 precision 和 recall 的表示。   

**文本训练**的 **基础** 是样本。没有训练样本就是白扯。以下是一些公开的文本分类数据：   

维基百科数据：  [https://dumps.wikimedia.org/](https://dumps.wikimedia.org/),例如中文数据为：[https://dumps.wikimedia.org/zhwiki/latest/zhwiki-latest-pages-articles.xml.bz2](https://dumps.wikimedia.org/zhwiki/latest/zhwiki-latest-pages-articles.xml.bz2)   
dbpedia/yelp_review_full/amazon_review_full/sogou_news:  [https://drive.google.com/drive/folders/0Bz8a_Dbh9Qhbfll6bVpmNUtUcFdjYmF2SEpmZUZUcVNiMUw1TWN6RDV3a0JHT3kxLVhVR2M?spm=5176.100239.blogcont128589.13.L2tfdg](https://drive.google.com/drive/folders/0Bz8a_Dbh9Qhbfll6bVpmNUtUcFdjYmF2SEpmZUZUcVNiMUw1TWN6RDV3a0JHT3kxLVhVR2M?spm=5176.100239.blogcont128589.13.L2tfdg)   
搜狗实验室预料：  [http://www.sogou.com/labs/resource/list_yuliao.php](http://www.sogou.com/labs/resource/list_yuliao.php)   
[THUCTC](http://thuctc.thunlp.org/)   
...

### 原理篇 ###




### 参考资料 ###  

[fasttext vs word2vec](http://www.jianshu.com/p/b7ede4e842f1)  
[fasttext](https://fasttext.cc/docs/en/supervised-tutorial.html#content)  
[Bayes_theorem](https://en.wikipedia.org/wiki/Bayes%27_theorem)  
[Positive_and_negative_predictive_values](https://en.wikipedia.org/wiki/Positive_and_negative_predictive_values)   
[Binary_classification](https://en.wikipedia.org/wiki/Binary_classification)   
[Sensitivity_and_specificity](https://en.wikipedia.org/wiki/Sensitivity_and_specificity)   
[Precision_and_recall](https://en.wikipedia.org/wiki/Precision_and_recall)   
[中英文维基百科语料上的Word2Vec实验](http://www.52nlp.cn/%E4%B8%AD%E8%8B%B1%E6%96%87%E7%BB%B4%E5%9F%BA%E7%99%BE%E7%A7%91%E8%AF%AD%E6%96%99%E4%B8%8A%E7%9A%84word2vec%E5%AE%9E%E9%AA%8C)   


