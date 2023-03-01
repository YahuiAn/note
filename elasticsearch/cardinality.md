## 介绍

``approximate`` ``distinct`` 近似的，去重的

## precision_threshold

``precision_threshold`` 参数可以通过牺牲内存来提高精度，默认值是 3000，最大值是 40000

当统计的字段唯一值低于该值时，结果接近准确值（**注意并不是完全精确**），当高于该值时，结果会有一点不准确

```
POST test/_search
{
  "size": 0,
  "aggs": {
    "ip": {
      "cardinality": {
        "field": "ip.keyword",
        "precision_threshold": 40000
      }
    }
  }
}
```

## 近似值

cardinality 聚合基于 HLL 算法，有如下的优点：

- 可以配置精确度来决定牺牲多少内存来换精确性
- 在低基数据集上具有卓越的精度
- 固定的内存使用量，不管 10 个或者数 10 亿唯一的值，内存使用量仅取决于设置的精度，`c * 8` bytes，比如设置最大值 40000 * 8 bytes = 320KB

![cardinality error](image/cardinality_error.png)

横坐标：实际的基数，纵座标：相对错误，三条线分别是设置了不同的 ``precision_threshold``

>  Accuracy in practice depends on the dataset in question.  In general, most datasets show consistently good accuracy. // TODO 取决于数据集的什么特征？

**注意**：

- 横坐标是数据集的实际基数，而不是数据集的文档数量
- 计算结果可能大于或小于实际基数

## 提前计算 hash

对于高基数的 string 字段，可以在索引的的时候就计算并存储其 hash 值 （[客户端](https://github.com/search?q=go+murmur3&type=Repositories) 和 [ES](https://www.elastic.co/guide/en/elasticsearch/plugins/current/mapper-murmur3-usage.html) 都可以计算），来加快统计时的速度（空间换时间）

对于 numeric 字段来说，计算 hash 是非常快的

## 参考

https://www.elastic.co/guide/en/elasticsearch/reference/7.16/search-aggregations-metrics-cardinality-aggregation.html

