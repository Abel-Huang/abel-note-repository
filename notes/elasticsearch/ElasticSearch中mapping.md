## ElasticSearch中mapping
### 基本概念
#### _mapping
自动或者手动为index中的type建立的一种数据结构和相关配置，简称为mapping。可以认为是字段的存储结构。
语法：
`GET  /index/_mapping/type`
mapping就是index的type的元数据
1. 核心的数据类型
string/text
byte, short,  integer, long
float,  double 
boolean
date

2. dynamic mapping
默认情况下自动推导识别的数据类型

#### 倒排索引

#### 分词器
主要用处是切分词语和normalization(提升recall召回率)
分词器有三部分组成：
* character filter：预处理
* tokenizer：分词
* token filter：normalization的操作，大小写，同义词，停用词
