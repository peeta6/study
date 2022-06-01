

# ElasticSearch

## 初识elasticsearch

### 正向索引和倒排索引

![image-20220114105646822](/Users/mac/Library/Application Support/typora-user-images/image-20220114105646822.png)

![image-20220114100108790](/Users/mac/Library/Application Support/typora-user-images/image-20220114100108790.png)

### 概念对比

![](/Users/mac/Library/Application Support/typora-user-images/image-20220114110532331.png)

![image-20220114110820140](/Users/mac/Library/Application Support/typora-user-images/image-20220114110820140.png)

![image-20220114110932902](/Users/mac/Library/Application Support/typora-user-images/image-20220114110932902.png)

### MySQL和ElasticSearch架构对比

![image-20220114111150897](/Users/mac/Library/Application Support/typora-user-images/image-20220114111150897.png)

![image-20220114111220217](/Users/mac/Library/Application Support/typora-user-images/image-20220114111220217.png)

## 安装部署elasicsearch

先创建一个net

```shell
docker network create mynet01
```

-e “ES_JAVA_OPTS=-Xms512m -Xmx512m” 配置堆内存（JVM）。因为ES底层是java实现的，所以要配置jvm内存大小。默认值是1T，对于轻量级服务器太大了，所以适当减少为512M(但是不能再弄少了，再少的话可能跟着视频走，会出现内存不足的问题)

-e “discovery.type=single-node” 单点模式运行（区别于集群模式运行）

两个-v参数：数据卷挂载，分别是数据保存目录(data)，和插件目录(plugins)

–network mynet01 将ES容器加入到刚刚创建的

docker网络中

-p 9200:9200 和 -p 9300:9300 是暴露的端口，9200是用户访问的http协议端口，9300是ES容器节点互联的端口

elasticsearch:7.12.1 镜像名称

```shell
docker run -d --name es -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" -e "discovery.type=single-node" -v es-data:/usr/share/elasticsearch/data -v es-plugins:/usr/share/elasticsearch/plugins --privileged --network mynet01 -p 9200:9200 -p 9300:9300 elasticsearch:7.12.1
```

## 部署Kibana

运行docker命令，部署kibana

```shell
 docker run -d --name kibana -e ELASTICSEARCH_HOSTS=http://es:9200 --network mynet01 -p 5601:5601 kibana:7.12.1
```

## 分词器

![image-20220114124649322](/Users/mac/Library/Application Support/typora-user-images/image-20220114124649322.png)

### 安装ik分词器

#### 在线安装ik分词器

1、进入容器

```shell
docker exec -it es /bin/bash
```

2、执行命令在线下载ik分词器

```shell
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.12.1/elasticsearch-analysis-ik-7.12.1.zip
```

#### 离线安装ik分词器

1、下载ik分词器zip包

https://github.com/medcl/elasticsearch-analysis-ik/releases?page=3

2、查看挂载目录

```shell
docker volume inspect es-plugins
```

3、cd到本地挂载目录

将下载好的ik分词器复制到挂载目录中，然后执行解压

```shell
unzip 文件 -d ik
```

4、重启es容器

```shell
docker restart 容器id或者容器名
```

### ik分词器扩展与停用

![image-20220114141708760](/Users/mac/Library/Application Support/typora-user-images/image-20220114141708760.png)

![image-20220114145715980](/Users/mac/Library/Application Support/typora-user-images/image-20220114145715980.png)

## 索引库操作

![image-20220114150339919](/Users/mac/Library/Application Support/typora-user-images/image-20220114150339919.png)

![image-20220114150457964](/Users/mac/Library/Application Support/typora-user-images/image-20220114150457964.png)

### 创建索引库

![image-20220114151038174](/Users/mac/Library/Application Support/typora-user-images/image-20220114151038174.png)

```json
PUT /heima
{
  "mappings":{
    "properties":{
      "info":{
        "type":"text",
        "analyzer":"ik_smart"
      },
      "email":{
        "type":"keyword",
        "index":false
      },
      "name":{
        "type":"object",
        "properties":{
          "firstname":{
            "type":"keyword"
          },
          "lastname":{
            "type":"keyword"
          }
        }
      }
    }
  }
}
```

### 查看、删除索引库

![image-20220114154309352](/Users/mac/Library/Application Support/typora-user-images/image-20220114154309352.png)



![image-20220114154635162](/Users/mac/Library/Application Support/typora-user-images/image-20220114154635162.png)

```json
# 新增
PUT /heima
{
  "mappings":{
    "properties":{
      "info":{
        "type":"text",
        "analyzer":"ik_smart"
      },
      "email":{
        "type":"keyword",
        "index":false
      },
      "name":{
        "type":"object",
        "properties":{
          "firstname":{
            "type":"keyword"
          },
          "lastname":{
            "type":"keyword"
          }
        }
      }
    }
  }
}
# 查询
GET /heima

# 修改
PUT /heima/_mapping
{
  "properties":{
    "age":{
      "type":"long"
    }
  }
}

#删除
DELETE /heima
```

## 文档操作

### 添加文档

![image-20220114155218119](/Users/mac/Library/Application Support/typora-user-images/image-20220114155218119.png)

### 修改文档

![image-20220114160923300](/Users/mac/Library/Application Support/typora-user-images/image-20220114160923300.png)

### 文档增删改查

```json
# 插入文档
POST /heima/_doc/1
{
  "info":"我是你妈妈吗",
  "email":"zy@dagege.cn",
  "name":{
    "firstname":"云",
    "lastname":"赵"
  }
}

# 查询文档
GET /heima/_doc/1

# 删除文档
DELETE /heima/_doc/1

# 全量修改文档
POST /heima/_doc/1
{
  "info":"我是你妈妈啊",
  "email":"zy@dagege.com",
  "name":{
    "firstname":"子龙",
    "lastname":"赵"
  }
}

# 局部修改文档
POST /heima/_update/1
{
  "doc":{
    "email":"wonendie@dage.com"
  }
}
```

![image-20220114161137705](/Users/mac/Library/Application Support/typora-user-images/image-20220114161137705.png)

## RestClient操作索引库

官网查看：https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/index.html

![image-20220114161946828](/Users/mac/Library/Application Support/typora-user-images/image-20220114161946828.png)

![image-20220114174533078](/Users/mac/Library/Application Support/typora-user-images/image-20220114174533078.png)

```shell
# 酒店的mapping
PUT /hotel
{
  "mappings": {
    "properties": {
      "id":{
        "type": "keyword"         
      },
      "name":{
        "type": "text"
        , "analyzer": "ik_max_word",
        "copy_to": "all"
      },
      "address":{
        "type": "keyword"
        , "index": false
      },
      "price":{
        "type": "integer"
      },
      "score":{
        "type": "integer"
      },
      "brand":{
        "type": "keyword",
        "copy_to": "all"
      },
      "city":{
        "type": "keyword"
      },
      "starName":{
        "type": "keyword"
      },
      "business":{
        "type": "keyword",
        "copy_to": "all"
      },
      "location":{
        "type": "geo_point"
      },
       "pic":{
        "type": "keyword"
        , "index": false
      },
      "all":{
        "type": "text",
        "analyzer": "ik_max_word"
      }
    }
  }
}
```

## RestClient操作文档

![image-20220117110456975](/Users/mac/Library/Application Support/typora-user-images/image-20220117110456975.png)

### 批量增加文档

![image-20220117110947784](/Users/mac/Library/Application Support/typora-user-images/image-20220117110947784.png)

# 分布式搜索引擎

es官方DLS文档:https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html

![image-20220117112609908](/Users/mac/Library/Application Support/typora-user-images/image-20220117112609908.png)

## DSL Query基本语法

![image-20220117112802377](/Users/mac/Library/Application Support/typora-user-images/image-20220117112802377.png)

### 案例

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  }
}
```

![image-20220117113602359](/Users/mac/Library/Application Support/typora-user-images/image-20220117113602359.png)

## 全文检索查询

![image-20220117115033503](/Users/mac/Library/Application Support/typora-user-images/image-20220117115033503.png)

### 案例

```json
# match查询
GET /hotel/_search
{
  "query": {
    "match": {
      "all": "外滩"
    }
  }
}
```

```json
# multi_match查询
GET /hotel/_search
{
  "query": {
    "multi_match": {
      "query": "外滩如家", 
      "fields": ["name","brand","business"]
    }
  }
}
```

## 精确查询

![image-20220117122526946](/Users/mac/Library/Application Support/typora-user-images/image-20220117122526946.png)

![image-20220117122805949](/Users/mac/Library/Application Support/typora-user-images/image-20220117122805949.png)

### 案例

```json
# 精确查询，term查询
GET /hotel/_search
{
  "query": {
    "term": {
      "city": {
        "value": "上海"
      }
    }
  }
}
```

```json
# 范围查询，range查询
GET /hotel/_search
{
  "query": {
    "range": {
      "price": {
        "gte": 500,
        "lte": 2000
      }
    }
  }
}
```

![image-20220117123146996](/Users/mac/Library/Application Support/typora-user-images/image-20220117123146996.png)

## 地理查询

![image-20220117123222271](/Users/mac/Library/Application Support/typora-user-images/image-20220117123222271.png)

![image-20220117123358922](/Users/mac/Library/Application Support/typora-user-images/image-20220117123358922.png)

![image-20220117123514221](/Users/mac/Library/Application Support/typora-user-images/image-20220117123514221.png)

### 案例

```shell
GET /hotel/_search
{
  "query": {
    "geo_distance":{
      "distance":"15km",
      "location":"31.12,121.5"
    }
  }
}

```

## Function Score Query

![image-20220117143126895](/Users/mac/Library/Application Support/typora-user-images/image-20220117143126895.png)

### 案例

![image-20220117143323970](/Users/mac/Library/Application Support/typora-user-images/image-20220117143323970.png)

```json
GET /hotel/_search
{
  "query": {
    "function_score": {
      "query": {
        "match": {
          "all": "外滩"
        }
      },
      "functions": [
        {
          "filter": {
            "term": {
              "brand": "如家"
            }
          },
          "weight": 10
        }
      ],
      "boost_mode":"sum"
    }
    
  }
}
```

![image-20220117145051514](/Users/mac/Library/Application Support/typora-user-images/image-20220117145051514.png)

## 复合查询BooleanQuery

![image-20220117145627954](/Users/mac/Library/Application Support/typora-user-images/image-20220117145627954.png)

![image-20220117151439567](/Users/mac/Library/Application Support/typora-user-images/image-20220117151439567.png)

### 案例

```json
GET /hotel/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "如家"
          }
        }
      ],
      "must_not": [
        {
          "range": {
            "price": {
              "gt": 400
            }
          }
        }
      ],
      "filter": [
        {"geo_distance": {
          "distance": "10km",
          "location": {
            "lat": 31.21,
            "lon": 121.5
          }
        }}
      ]
    }
  }
}
```

![image-20220117152147847](/Users/mac/Library/Application Support/typora-user-images/image-20220117152147847.png)

## 搜索结果处理

### 排序

![image-20220117152435988](/Users/mac/Library/Application Support/typora-user-images/image-20220117152435988.png)

#### 案例

![image-20220117153238498](/Users/mac/Library/Application Support/typora-user-images/image-20220117153238498.png)

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "score":"desc"
    },
    {
      "price":"asc"
    }
  ]
}
```

![image-20220117153355768](/Users/mac/Library/Application Support/typora-user-images/image-20220117153355768.png)

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "_geo_distance": {
        "location": {
          "lat": 31.21,
          "lon": 121.46
        }, 
        "order": "asc",
        "unit": "km"
      }
    }
  ]
}
```

### 分页

![image-20220117160002622](/Users/mac/Library/Application Support/typora-user-images/image-20220117160002622.png)

#### 案例

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "price": "asc"
    }
  ],
  "from": 20,
  "size": 10
}
```

![image-20220117160445048](/Users/mac/Library/Application Support/typora-user-images/image-20220117160445048.png)

![image-20220117160856191](/Users/mac/Library/Application Support/typora-user-images/image-20220117160856191.png)

![image-20220117161008549](/Users/mac/Library/Application Support/typora-user-images/image-20220117161008549.png)

### 高亮

![image-20220117161306995](/Users/mac/Library/Application Support/typora-user-images/image-20220117161306995.png)

#### 案例

```json
# 不加pre_tags和post_tags 是因为默认就是em标签
GET /hotel/_search
{
  "query": {
    "match": {
      "all": "如家"
    }
  },
  "highlight": {
    "fields": {
      "name": {
      "require_field_match": "false"
      }
    }
  }
}
```



![image-20220117161637394](/Users/mac/Library/Application Support/typora-user-images/image-20220117161637394.png)

## RestClient查询文档

![image-20220117164138212](/Users/mac/Library/Application Support/typora-user-images/image-20220117164138212.png)

![](/Users/mac/Library/Application Support/typora-user-images/image-20220117164115705.png)

![image-20220117164346131](/Users/mac/Library/Application Support/typora-user-images/image-20220117164346131.png)

![image-20220117164605935](/Users/mac/Library/Application Support/typora-user-images/image-20220117164605935.png)

#### 排序和分页

![image-20220117164822761](/Users/mac/Library/Application Support/typora-user-images/image-20220117164822761.png)

#### 高亮

![image-20220117165211142](/Users/mac/Library/Application Support/typora-user-images/image-20220117165211142.png)

#### 高亮结果解析

![image-20220117165600741](/Users/mac/Library/Application Support/typora-user-images/image-20220117165600741.png)

![image-20220117165829698](/Users/mac/Library/Application Support/typora-user-images/image-20220117165829698.png)

## 数据的聚合

![image-20220117174940799](/Users/mac/Library/Application Support/typora-user-images/image-20220117174940799.png)

![image-20220117175019692](/Users/mac/Library/Application Support/typora-user-images/image-20220117175019692.png)

![image-20220117185638211](/Users/mac/Library/Application Support/typora-user-images/image-20220117185638211.png)

#### 聚合结果排序

![image-20220117190134753](/Users/mac/Library/Application Support/typora-user-images/image-20220117190134753.png)

#### 案例

```json
GET /hotel/_search
{
  "size": 0,
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "size": 10
        , "order": {
          "_count": "asc"
        }
      }
    }
  }
}
```

#### 限定聚合范围

![image-20220117190830443](/Users/mac/Library/Application Support/typora-user-images/image-20220117190830443.png)

```json
# 聚合功能，限定聚合范围
GET /hotel/_search
{
  "query": {
    "range": {
      "price": {
        "lte": 200
      }
    }
  }, 
  "size": 0,
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "size": 10
      }
    }
  }
}
```

![image-20220117190936978](/Users/mac/Library/Application Support/typora-user-images/image-20220117190936978.png)

### Metric聚合

![image-20220117191124969](/Users/mac/Library/Application Support/typora-user-images/image-20220117191124969.png)<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220117191125978.png" alt="image-20220117191125978" style="zoom: 67%;" />

### RestAPI实现聚合

![image-20220117191820172](/Users/mac/Library/Application Support/typora-user-images/image-20220117191820172.png)

![image-20220117192120455](/Users/mac/Library/Application Support/typora-user-images/image-20220117192120455.png)

#### 对过滤条件聚合

![image-20220117194436818](/Users/mac/Library/Application Support/typora-user-images/image-20220117194436818.png)

## 自动补全

![image-20220117195555439](/Users/mac/Library/Application Support/typora-user-images/image-20220117195555439.png)

拼音分词器下载地址：https://github.com/medcl/elasticsearch-analysis-pinyin/releases

### 自定义拼音分词器

![image-20220117200202578](/Users/mac/Library/Application Support/typora-user-images/image-20220117200202578.png)

![image-20220117200440489](/Users/mac/Library/Application Support/typora-user-images/image-20220117200440489.png)

```json
# 自定义拼音分词器
PUT /test
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer":{
          "tokenizer":"ik_max_word",
          "filter":"py"
        }
      },
      "filter":{
      "py":{
        "type":"pinyin",
        "keep_full_pinyin" : false,
        "keep_joined_full_pinyin": true,
        "keep_original": true,
        "limit_first_letter_length": 16,
        "remove_duplicated_term": true,
        "none_chinese_pinyin_tokenize": false
    }
  },
  "mappings": {
    "properties": {
      "name":{
        "type": "text",
        "analyzer": "my_analyzer"
        
      }
      
    }
    
  }
      
    }
    
  }
}

# 测试
GET /test/_analyze
{
  "text": "如家酒店",
  "analyzer": "my_analyzer"
}
```

![image-20220117202509953](/Users/mac/Library/Application Support/typora-user-images/image-20220117202509953.png)

![image-20220117202721313](/Users/mac/Library/Application Support/typora-user-images/image-20220117202721313.png)

```json
PUT /test
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer":{
          "tokenizer":"ik_max_word",
          "filter":"py"
        }
      },
      "filter":{
      "py":{
        "type":"pinyin",
        "keep_full_pinyin" : false,
        "keep_joined_full_pinyin": true,
        "keep_original": true,
        "limit_first_letter_length": 16,
        "remove_duplicated_term": true,
        "none_chinese_pinyin_tokenize": false
    }
  },
  "mappings": {
    "properties": {
      "name":{
        "type": "text",
        "analyzer": "my_analyzer",
        "search_analyzer":"ik_smart"
        
      }
      
    }
    
  }
      
    }
    
  }
}
```

![image-20220117202833891](/Users/mac/Library/Application Support/typora-user-images/image-20220117202833891.png)

### Completion suggester查询

![image-20220117203023062](/Users/mac/Library/Application Support/typora-user-images/image-20220117203023062.png)![image-20220117223052292](/Users/mac/Library/Application Support/typora-user-images/image-20220117223052292.png)

#### 案例

```json
PUT /test2
{
  "mappings": {
    "properties": {
      "title":{
        "type": "completion"
      }
    }
  }
}

POST test2/_doc
{
  "title":["Sony","WH_1000XM3"]
}

POST test2/_doc
{
  "title":["SK-II","PITERA"]
}

POST test2/_doc
{
  "title":["Nintendo","switch"]
}

# 自动补全查询

GET /test2/_search
{
  "suggest": {
    "titleSuggest": {
      "text": "s",
      "completion": {
        "field": "title",
        "skip_duplicates":true,
        "size":10
      }
    }
  }
}
```

![image-20220117223029411](/Users/mac/Library/Application Support/typora-user-images/image-20220117223029411.png)

### 自动补全酒店案例

![image-20220117223234686](/Users/mac/Library/Application Support/typora-user-images/image-20220117223234686.png)

#### 修改索引库结构，设置自定义拼音分词器

```json
PUT /hotel
{
  "settings": {
    "analysis": {
      "analyzer": {
        "text_analyzer":{
          "tokenizer":"ik_max_word",
          "filter":"py"
        },
        "completion_analyzer":
        {
          "tokenizer":"keyword",
          "filter":"py"
        }
      },
      "filter":{
      "py":{
        "type":"pinyin",
        "keep_full_pinyin" : false,
        "keep_joined_full_pinyin": true,
        "keep_original": true,
        "limit_first_letter_length": 16,
        "remove_duplicated_term": true,
        "none_chinese_pinyin_tokenize": false
      }
      }
    }
  },
  "mappings": {
    "properties": {
      "id":{
        "type": "keyword"         
      },
      "name":{
        "type": "text", 
        "analyzer": "text_analyzer",
        "search_analyzer": "ik_smart", 
        "copy_to": "all"
      },
      "address":{
        "type": "keyword"
        , "index": false
      },
      "price":{
        "type": "integer"
      },
      "score":{
        "type": "integer"
      },
      "brand":{
        "type": "keyword",
        "copy_to": "all"
      },
      "city":{
        "type": "keyword"
      },
      "starName":{
        "type": "keyword"
      },
      "business":{
        "type": "keyword",
        "copy_to": "all"
      },
      "location":{
        "type": "geo_point"
      },
       "pic":{
        "type": "keyword"
        , "index": false
      },
      "all":{
        "type": "text",
        "analyzer": "text_analyzer",
        "search_analyzer": "ik_smart"
      },
      "suggestion":{
        "type": "completion",
        "analyzer": "completion_analyzer"
      }
    }
  }
}
```

#### 自动补全搜索DSL

```json
GET /hotel/_search
{
  "suggest": {
    "suggestions": {
      "text": "上地",
      "completion": {
        "field": "suggestion",
        "skip_duplicates":true,
        "size":10
      }
    }
  }
}
```

### RestAPI实现自动补全

![image-20220117231157900](/Users/mac/Library/Application Support/typora-user-images/image-20220117231157900.png)

![image-20220117231513853](/Users/mac/Library/Application Support/typora-user-images/image-20220117231513853.png)

### 数据同步

![image-20220117233609729](/Users/mac/Library/Application Support/typora-user-images/image-20220117233609729.png)

## ES集群

### es集群结构

![image-20220117235934013](/Users/mac/Library/Application Support/typora-user-images/image-20220117235934013.png)

### 创建es集群

首先编写一个docker-compose文件，内容如下：

```yaml
version: '2.2'
services:
  es01:
    image: elasticsearch:7.12.1
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic
  es02:
    image: elasticsearch:7.12.1
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data02:/usr/share/elasticsearch/data
    ports:
      - 9201:9200
    networks:
      - elastic
  es03:
    image: elasticsearch:7.12.1
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elast
      ic
    ports:
      - 9202:9200
volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge
```

es运行需要修改一些linux系统权限，修改`/etc/sysctl.conf`文件

```shell
vi /etc/sysctl.conf
```

添加下面的内容：

```shell
vm.max_map_count=262144
```

然后执行命令，让配置生效：

```shell
sysctl -p
```

通过docker-compose启动集群：

```shell
docker-compose up -d
```

### 集群状态监控

kibana可以监控es集群，不过新版本需要依赖es的x-pack 功能，配置比较复杂。

这里推荐使用cerebro来监控es集群状态，官方网址：https://github.com/lmenezes/cerebro

安装点击cerebro.bat直接运行

创建索引库

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220118001217223.png" alt="image-20220118001217223" style="zoom: 67%;" />

celebro也可以创建索引库然后设置分片和副本

![image-20220118001634235](/Users/mac/Library/Application Support/typora-user-images/image-20220118001634235.png)



### es集群节点角色

![image-20220118001514866](/Users/mac/Library/Application Support/typora-user-images/image-20220118001514866.png)

### es集群的分布式架构

![image-20220118002114086](/Users/mac/Library/Application Support/typora-user-images/image-20220118002114086.png)

### es集群的脑裂问题

![image-20220118002427124](/Users/mac/Library/Application Support/typora-user-images/image-20220118002427124.png)

![image-20220118002503602](/Users/mac/Library/Application Support/typora-user-images/image-20220118002503602.png)

### 分布式存储

![image-20220118003044013](/Users/mac/Library/Application Support/typora-user-images/image-20220118003044013.png)

![image-20220118003213722](/Users/mac/Library/Application Support/typora-user-images/image-20220118003213722.png)

### 分布式查询

![image-20220118003326552](/Users/mac/Library/Application Support/typora-user-images/image-20220118003326552.png)

![image-20220118003407510](/Users/mac/Library/Application Support/typora-user-images/image-20220118003407510.png)

### 故障转移

![image-20220118003802530](/Users/mac/Library/Application Support/typora-user-images/image-20220118003802530.png)

![image-20220118003724503](/Users/mac/Library/Application Support/typora-user-images/image-20220118003724503.png)