---
layout: post
title: Elasticsearch Dsl Aggs
date: 2018-10-24
description:  Elasticsearch Dsl Aggs 学习# Add post description (optional)
img: es-img.jpeg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [Elasticsearch]
---
最近需要使用ES，所有准备系统学习下。  
这篇是ES 的Aggs，这是边整理边写的。So,有不好的地方只能请见谅了~。~
---
### 概念

#### 桶（Buckets）  
    满足特定条件的文档的集合。简单来说就是分组，类似sql 中的Group By


#### 指标（Metrics）
    对桶内的文档进行统计计算。统计方法吧

>简单理解就是：通过桶将数据进行分组，然后通过指标可以对每个组数据进行计算。当然桶还可以嵌套

#### 聚合简单结构
```Json
"query":{}, #范围选择
"aggs":{  #关键字aggs或者aggregations 定义一个聚合
	"name":{ #自定义的这个聚合的名称，返回结果以这个名字为key
		"global" : {}, #全局桶 在聚合中添加之后无视前面query条件
		"terms":{ #聚合类型 桶或者指标都可以
			"field":"字段"
		}
	}
}
```
#### 聚合之过滤桶
```Json
"query":{},
"aggs":{
	"filter-name" : {
		"filter":{
			"range": {    # filter-body
               "sold": {
                  "from": "now-1M"  
               }
            }
		}
	},
	"aggs":{} #将继承上面的filter，满足上面filter的结果才会进入这个集合
}
```
#### 后过滤
```Json
"post_filter": {    # 后过滤关键字 在下面过滤结束之后，这个过滤才会执行
    "term" : {		# 也就是说他是在下面聚合结果中去过滤
        "color" : "green"
    }
},
"aggs" : { #这个聚合不会使用post_filter的条件
    "all_colors": {
        "terms" : { "field" : "color" }
    }
}
```

>性能考虑（Performance consideration）
>当你需要对搜索结果和聚合结果做不同的过滤时，你才应该使用 post_filter ， 有时用户会在普通搜索使用 post_filter 。不要这么做！ post_filter 的特性是在查询 之后 执行，任何过滤对性能带来的好处（比如缓存）都会完全失去。

>在我们需要不同过滤时， post_filter 才与聚合一起使用。

#### 小结
* 在 filter 过滤中的 non-scoring 查询，同时影响搜索结果和聚合结果
* filter 桶影响聚合
* post_filter 只影响搜索结果
