## 简介

使用脚本，可以在 ES 中计算自定义表达式，或者对文档进行复杂的处理

例如，可以使用脚本将计算值作为字段返回，或计算查询的自定义分数

默认内置的是 painless scripting（安全，性能，简单）

## 格式

```
  "script": {
    "lang":   "...", # 语言，不指定默认是 painless
    "source" | "id": "...", 
	# source 为 inline scripts
	# id 为 stored scripts（即事先使用 `_scripts` api 将脚本存储起来，使用时指定 id 即可）
    "params": { ... } # 变量，类似于函数的入参
  }
```

## 示例

```shell
PUT my-index-000001/_doc/1
{
  "my_field": 5
}

# 简单例子
GET my-index-000001/_search
{
  "script_fields": { # 使用 script_fields api 来选择返回的结果
    "my_doubled_field": {
      "script": {
        "source": "doc['my_field'].value * 2" 
        # 通过 doc['field_name'] 来访问文档中的字段
        # my_field 的值乘以 2
      }
    }
  }
}
```

## 参数

ES 每收到一个脚本，都会编译然后缓存起来（默认最大缓存的数量是10个），编译比较消耗资源，所以要避免硬编码，应该使用参数（类似于函数的入参）

```shell
# 使用参数
GET my-index-000001/_search
{
  "script_fields": {
    "my_doubled_field": {
      "script": { 
        "source": "doc['my_field'].value * params['multiplier']", 
        "params": { # 参数
          "multiplier": 2
        }
      }
    }
  }
}
```

## stored scripts

前面的示例是 inline scripts，也可以事先将编写好的脚本存储，在使用时指定 id 直接用，即 stored scripts

**这样的好处是减少了编译的时间使的搜索更快**

```shell
# 创建/更新
POST _scripts/add-value
{
  "script": {
    "lang": "painless",
    "source": "doc['my_field'].value + params['my_modifier']"
  }
}

# 查看
GET _scripts/add-value

# 删除
DELETE _scripts/add-value

GET my-index-000001/_search
{
  "script_fields": {
    "new_source": {
      "script": {
        "id": "add-value", # 通过 id 指定脚本
        "params": {
          "my_modifier": 6
        }
      }
    }
  }
}
```

## 更新文档

使用脚本可以很方便的对文档进行复杂的更新操作

```shell
# 准备数据
PUT my-index-000001/_doc/1
{
  "counter" : 1,
  "tags" : ["red"]
}
```

### 数值运算

```shell
POST my-index-000001/_update/1
{
  "script": {
    "source": "ctx._source.counter += params.count", 
    # 使用 ctx._source.field_name 来获取文档中字段的值
    # params.count 和 params['count'] 都可以
    "params": {
      "count": 4
    }
  }
}
```

### list 操作

#### add

```shell
# 向 list 中添加元素
POST my-index-000001/_update/1
{
  "script": {
    "source": "ctx._source.tags.add(params.tag)", # add 方法
    "params": {
      "tag": "yellow"
    }
  }
}
```

#### remove

```shell
# 从 list 中移除元素
POST my-index-000001/_update/1
{
  "script": {
    "source": "ctx._source.tags.remove(1)" # remove 方法
  }
}

# 注意：上面的代码健壮性不好，如果 tags list 长度小于 2 会发生数组越界的 runtime error

# 将上面的命令再执行一遍的返回结果
"caused_by" : {
        "type" : "index_out_of_bounds_exception",
        "reason" : "Index 1 out of bounds for length 1"
}

# 优化版本
POST my-index-000001/_update/1
{
  "script": {
    "source": """
if (ctx._source.tags.contains(params['tag'])) { # contains
  int i = ctx._source.tags.indexOf(params['tag']); # indexOf ❓❓❓
  ctx._source.tags.remove(i); # remove
}
    """,
    # 可以看到上面的脚本和 java 语法基本一样
    "params": {
      "tag": "red"
    }
  }
}
```

### 增加字段

```shell
POST my-index-000001/_update/1
{
  "script" : "ctx._source.new_field = 'I AM NEW FIELD'"
}
```

### 删除字段

```shell
POST my-index-000001/_update/1
{
  "script" : "ctx._source.remove('new_field')"
}
```

### operation ❓❓❓

```shell
POST my-index-000001/_update/1
{
  "script": {
    "source": """
if (ctx._source.tags.contains(params['tag'])) {
  ctx.op = 'delete'
} else {
  ctx.op = 'none'
}
    """,
    "params": {
      "tag": "red"
    }
  }
}
```

## 缓存

### 大小

ES 通过缓存编译后的脚本来加速查询，所以缓存池的大小非常重要

```shell
# 查看节点脚本缓存情况
GET /_nodes/stats/script_cache

{
	"script_cache": {
		"sum": {
			"compilations": 40,
			"cache_evictions": 0, # 被驱逐的缓存数量
			"compilation_limit_triggered": 0 # 已触发编译限制数量
			# 上面两个值较大时，需要排查是否滥用脚本或缓存池容量设置太小
		},
		"contexts": [{❓❓❓
				"context": "aggregation_selector",
				"compilations": 0,
				"cache_evictions": 0,
				"compilation_limit_triggered": 0
			},
			{
				"context": "aggs",
				"compilations": 1,
				"cache_evictions": 0,
				"compilation_limit_triggered": 0
			},
			{
				"context": "aggs_combine",
				"compilations": 1,
				"cache_evictions": 0,
				"compilation_limit_triggered": 0
			},
			......
		]
	}
}
```

使用 ``script.context.$CONTEXT.cache_max_size`` 设置缓存大小

### 过期时间

默认情况下，脚本缓存没有基于时间的过期，使用 ``script.context.$CONTEXT.cache_expire`` 可以设置过期时间

## 性能优化

> Scripts are incredibly useful, but can’t use Elasticsearch’s index structures or related optimizations. This relationship can sometimes result in slower search speeds.

虽然脚本用起来挺爽，但是在数据量大的情况下，会有较大的性能问题，严重时可能查询直接超时

### 数据预处理

通过合适的数据预处理来避免脚本的使用

```json
"sort": [
    {
      "_script": {
        "type": "number",
        "script": {
          "source": "doc['math_score'].value + doc['verbal_score'].value"
        },
        "order": "desc"
      }
    }
  ]
```

如上的查询按照 math_score + verbal_score 的和来排序，哪么我们设计 mapping 的时候就可以考虑直接增加 total_score 字段，虽然入库可能会较之前慢一些，但是可以提高搜索性能

### stored scripts

高频使用的脚本可以事先存储起来，防止每次查询时现编译（inline scripts 的缓存被驱逐或者过期）

##  其他

### 通过 painless scripting 访问字段 ❓❓❓

| 上下文               | 语法                   |
| -------------------- | ---------------------- |
| ingestion            | ctx.field_name         |
| update               | ctx._source.field_name |
| search & aggregation | doc["field_name"]      |

##  参考

https://www.elastic.co/guide/en/elasticsearch/reference/7.15/modules-scripting.html

https://www.elastic.co/guide/en/elasticsearch/painless/7.15/painless-guide.html



