---
layout: post
title: Python对Elasticsearch的基本操作
keywords: 'Elasticsearch, Python'
categories:
  - Elasticsearch
  - 大数据
  - Python
tags: Elasticsearch
date: 2019-01-23 00:01:54
---


> 在公司的大数据项目中，经常涉及对elasticsearch的查询操作，也会涉及利用Python对elasticsearch的数据的进行存取，为了系统地学习用Python语言操作elasticsearch，最近查阅了网上的一些资料，发现CSDN上有两篇文章写得很好，这篇笔记主要参考了这两篇文章（文末会附上这两篇文章的链接）

###  Python Elasticsearch Clien 的介绍
Elasticsearch 的语法在前面写的笔记中已有介绍，这里只介绍通过Python操作elasticsearch。

Elasticsearch 不仅仅是一个开源的全文搜索引擎，它还是一个分布式实时分析搜索引擎，同时它还是支持分布式的实时文档存储，每个字段可以被索引与搜索，支持 PB 级别的结构化或者非结构化数据。

Elasticsearch 提供了一系列 Restful API 来进行存取和查询操作，我们可以使用 curl 等命令来进行操作，但毕竟命令行模式没那么方便，所以这里我们就直接介绍利用 Python 来对接 Elasticsearch 的相关方法。

用 Python 操作 elasticsearch 可以使用 Python Elasticsearch Client，它是ES官方推荐的python客户端，这里以它为工具操作elasticsearch。

关于 Python Elasticsearch Client ，官方给出了相关的文档，，所有的用法都可以在文档里查的到，包括方法的说明、参数及返回值等。地址如下：

https://elasticsearch-py.readthedocs.io/en/master/api.html

![Python-elasticsearch.jpg](https://upload-images.jianshu.io/upload_images/12273007-fcfb173f32384b26.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- Python 中对接 Elasticsearch

使用的是一个同名的库，安装方式非常简单：

pip3 install elasticsearch

###  创建 Index

在操作之前，我们先来看一下 elasticsearch 中有哪些索引，我们可以访问这个链接：

http://localhost:9200/_cat/indices?v

![查看所有的索引.jpg](https://upload-images.jianshu.io/upload_images/12273007-43a241f4a2f4e7c8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，elasticsearch 中现在没有名称为 bank 的索引，下面我们使用 Python 来创建一个名为 bank 的 索引：

```
from elasticsearch import Elasticsearch

es = Elasticsearch()
result = es.indices.create(index='bank', ignore=400)
print(result)
```
输出的结果如下：
```
{'acknowledged': True, 'shards_acknowledged': True, 'index': 'bank'}
```
我们再次访问：
http://localhost:9200/_cat/indices?v
发现索引创建成功了

###  删除索引

删除 Index 也是类似的，代码如下：
```
from elasticsearch import Elasticsearch

es = Elasticsearch()
result = es.indices.delete(index='bank', ignore=[400, 404])
print(result)
```
返回结果如下：
```
{'acknowledged': True}
```
我们再次访问：
http://localhost:9200/_cat/indices?v
发现索引被删除了

###  增加一条文档

通过以下代码可以增加一条文档：
```
from elasticsearch import Elasticsearch
es = Elasticsearch([{"host":"localhost","port":9200}])
b= {"name": 'lu', 'sex':'female', 'age': 10}
es.index(index='bank', doc_type='typeName',body=b,id=None)
```
代码执行完成之后，我们在 kibana 里面查询 bank 索引中的数据，发现新增的文档已经被插入到索引中：

![插入数据.jpg](https://upload-images.jianshu.io/upload_images/12273007-3e329f44e59a826a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###  增加一条文档

通过以下代码可以删除一条文档
```
from elasticsearch import Elasticsearch
es = Elasticsearch([{"host":"localhost","port":9200}])
es.delete(index='bank', doc_type='typeName', id="AWh2Jj_Ey6XG0WYYFrgI")
```
在代码中，我们传入文档的 id ，代码执行完成后，我们在kibana中查询 bank 索引，发现文档被删除了，如下图：

![删除文档.jpg](https://upload-images.jianshu.io/upload_images/12273007-c7b8a80242a840b5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###  修改一条文档
通过以下代码可以修改一条文档
```
from elasticsearch import Elasticsearch
es = Elasticsearch([{"host":"localhost","port":9200}])

b= {"name": 'lu', 'sex':'female', 'age': 101}
es.update(index='bank', doc_type='typeName',body=b,id='AWh2Mmuoy6XG0WYYFrgL')

```

### 
通过以下代码可以查询一条文档
```
from elasticsearch import Elasticsearch
es = Elasticsearch([{"host":"localhost","port":9200}])
find=es.get(index='bank', doc_type='typeName', id='AWh2Mmuoy6XG0WYYFrgL')
print(find)
```
输出的结果如下：
```
{'_index': 'bank', '_type': 'typeName', '_id': 'AWh2Mmuoy6XG0WYYFrgL', '_version': 1, 'found': True, '_source': {'name': 'lu', 'sex': 'female', 'age': 10}}
```
