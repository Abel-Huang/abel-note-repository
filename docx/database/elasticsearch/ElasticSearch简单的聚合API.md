### ElasticSearch简单的聚合分析
```
GET  /shop/_mapping/product
{
  "properties":{
    "tags":{
      "type":"text",
      "fielddata":true
    }
  }
}
```
#### 分组聚合
* 按照tags进行分组
```
GET  /shop/product/_search
{
    "size": 0, 
    "aggs": {
      "group_by_tags": {
         "terms": {
           "field": "tags"
             }
         }
    }
}
```
* 按照查询条件查询，然后使用tags分组
```
GET  /shop/product/_search
{
  "size": 0, 
  "query": {
    "match": {
      "producer": "fangcao"
    }
  }, 
  "aggs": {
    "group_by_tags": {
        "terms": {
          "field": "tags"
        }
      }
    }
}
```
* 先按tags进行分组，分组完成后再按照组求平均值
```
GET  /shop/product/_search
{
  "size": 0, 
   "aggs": {
        "group_by_tags": {
        "terms": {
          "field": "tags"
            },
        "aggs": {
              "avg_prices": {
                "avg": {
                      "field": "price"
                     }
                 }
            }
         }
    }
}
```
* 先按tags进行分组，分组完成后再按照组求平均值, 并且按照avg_prices进行排序
```
GET  /shop/product/_search
{
  "size": 0, 
  "aggs": {
        "group_by_tags": {
        "terms": {
          "field": "tags",
          "order": {
            "avg_prices": "asc"
          }
      },
        "aggs": {
              "avg_prices": {
                "avg": {
                      "field": "price"
                    }
                 }
            }
        }
    }
}
```
* 按照指定的价格区间进行分组，然后按照每组内的tag分组，然后计算每组的平均价格
```
GET  /shop/product/_search
{
  "size": 0, 
  "aggs": {
    "group_by_price": {
      "range": {
        "field": "price",
        "ranges": {
            "from": 20,
            "to": 30
          }, {
            "from": 30,
            "to": 40
          }, {
            "from": 40,
            "to": 50
          }
        ]
      }, 
      "aggs": {
        "group_by_tags": {
          "terms": {
            "field": "tags"
          },
          "aggs": {
            "avg_prices": {
              "avg": {
                "field": "price"
                }
            }
          }
        }
      }
    }
  }
}
```