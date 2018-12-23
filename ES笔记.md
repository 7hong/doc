# 什么ES
基于Apache Lucene
提供RESTFul API
支持横向扩展，支持PB级别的结构化或者非结构化数据处理

# 使用场景
站内搜索、数据仓库等

# 单实例安装
1. 官网下载
https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.5.2.zip
2. 解压
3. 启动./bin/ElasticSearch.bat
4. 访问http://127.0.0.1:9200 OK

# Head图形化插件
1. 下载 
2. 修改es  ./config/elasticsearch.yml加上
	http.cors.enabled: true
	http.cors.allow-origin: "*"
3. npm install
4. npm run start

#多实例安装
1个master 2个slave

1.1 主节点配置
```
cluster.name: qh
node.name: es1
node.master: true
network.host: 127.0.0.1
http.port: 9200
http.cors.enabled: true
http.cors.allow-origin: "*"
```
1.2 slave1配置
```
cluster.name: qh
node.name: es2
network.host: 127.0.0.1
http.port: 8210
discovery.zen.ping.unicast.hosts: ["127.0.0.1"]
http.cors.enabled: true
http.cors.allow-origin: "*"
```
1.3 slave2配置
```
cluster.name: qh
node.name: es3
network.host: 127.0.0.1
http.port: 8220
discovery.zen.ping.unicast.hosts: ["127.0.0.1"]
http.cors.enabled: true
http.cors.allow-origin: "*"
```
`启动ES一定要在控制台启动，不然集群里就看不到。`
#基础概念
* 索引：含有相同属性的文档集合 索引 -- 数据库
* 类型：一个索引可以定义一个或多个类型，文档必须属于一个类型。 类型 -- 表 
* 文档：可以被索引的基本数据单位，例如一个用户基本信息。（最小的存储单位） 文档 -- 一行记录 
* 分片：每个索引都有多个分片、每个分片是一个Lucene索引（默认5个，只能创建时指定）
* 备份：拷贝一份分片就完成了分片的备份。默认一个，创建后可以更改）

# 基本用法
## API基本格式
`http://<ip>:<port>/<索引>/<类型>/<文档id>`
## 常用HTTP动词
GET PUT POST DELETE 
## 例子
### 使用Head插件创建一个索引book
1. 结构化索引
2. 非结构化索引 索引信息 Mapping为{} 

###使用API创建一个结构化索引
```
//PUT http://127.0.0.1:9200/people
{
	"settings":{
		"number_of_shards":3,
		"number_of_replicas":1
	},
	"mappings":{
		"man":{
			"properties":{
				"name":{
					"type":"text"
				},
				"country":{
					"type":"keyword"
				},
				"age":{
					"type":"integer"
				},
				"date":{
					"type":"date",
					"format":"yyyy-MM-dd HH:mm:ss||epoch_millis"
				}
			}
		},
		"woman":{
			
		}
	}
}
```
### 数据插入
* 指定文档id插入
```
PUT http://127.0.0.1:9200/people/man/1
{
	"name":"Qihong",
	"country":"China",
	"age":22,
	"date":"1996-2-21"
}
```
* 自动产生文档id插入
```
POST http://127.0.0.1:9200/people/man
{
	"name":"Hongji",
	"country":"China",
	"age":28,
	"date":"1996-02-21 00:00:00"
}

返回值
{
    "_index": "people",
    "_type": "man",
    "_id": "AWewDOqfvE_7Btn7POuM",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 2,
        "failed": 0
    },
    "created": true
}
```

### 修改数据
* 直接修改文档
```
POST http://127.0.0.1:9200/people/man/1/_update
{
	"doc":{
		"name":"我是Qihong"
	}
}
```

* 脚本修改
```
POST http://127.0.0.1:9200/people/man/1/_update
第一种
{
	"script":{
		"lang":"painless",
		"inline":"ctx._source.age += 10"
	}
}

第二种
{
	"script":{
		"lang":"painless",
		"inline":"ctx._source.age=params.age",
		"params":{
			"age":90
		}
	}
}
```
`ctx:ES上下文`
`ctx._cource:当前文档`

### 删除
* 删除文档
```
DELETE http://127.0.0.1:9200/people/man/1
```

* 删除索引
```
DELETE http://127.0.0.1:9200/people
```

### 查询
* 简单查询
```
GET http://127.0.0.1:9200/
返回
{
    "_index": "people",
    "_type": "man",
    "_id": "1",
    "_version": 4,
    "found": true,
    "_source": {
        "name": "我是其宏",
        "country": "China",
        "age": 90,
        "date": "1996-02-21 00:00:00"
    }
}
```
* 条件查询
1. 查询所有(指定条件)
```
POST http:/127.0.0.1:9200/people/_search
{
	"query":{
		"match_all":{}
	},
	"form":1,
	"size":1
}
返回
{
    "took": 6,//花费时间 6ms
    "timed_out": false,
    "_shards": {
        "total": 3,
        "successful": 3,
        "failed": 0
    },
    "hits": {
        "total": 2,
        "max_score": 1,
        "hits": [
            {
                "_index": "people",
                "_type": "man",
                "_id": "AWewDOqfvE_7Btn7POuM",
                "_score": 1,
                "_source": {
                    "name": "Hongji",
                    "country": "China",
                    "age": 28,
                    "date": "1996-02-21 00:00:00"
                }
            },
            {
                "_index": "people",
                "_type": "man",
                "_id": "1",
                "_score": 1,
                "_source": {
                    "name": "我是其宏",
                    "country": "China",
                    "age": 90,
                    "date": "1996-02-21 00:00:00"
                }
            }
        ]
    }
}
```

2. 关键字查询
```
POST http:/127.0.0.1:9200/people/_search
{
	"query":{
		"match":{
			"name":"qi"
		}
	},
	"sort":[
		{"age":{
			"order":"desc"
			}
		}
	]
}
```
* 聚合查询
```
POST http:/127.0.0.1:9200/people/_search
{
	"aggs":{
		"grop_by_age":{
			"terms":{
				"field":"age"
			}
		},
		"grop_by_country":{
			"terms":{
				"field":"country"
			}
		}
	}
}
返回
{
    "took": 212,
    "timed_out": false,
    "_shards": {
        "total": 3,
        "successful": 3,
        "failed": 0
    },
    "hits": {
        "total": 2,
        "max_score": 1,
        "hits": [
            {
                "_index": "people",
                "_type": "man",
                "_id": "AWewDOqfvE_7Btn7POuM",
                "_score": 1,
                "_source": {
                    "name": "Hongji",
                    "country": "China",
                    "age": 28,
                    "date": "1996-02-21 00:00:00"
                }
            },
            {
                "_index": "people",
                "_type": "man",
                "_id": "1",
                "_score": 1,
                "_source": {
                    "name": "我是其宏",
                    "country": "China",
                    "age": 90,
                    "date": "1996-02-21 00:00:00"
                }
            }
        ]
    },
    "aggregations": {
        "grop_by_age": {
            "doc_count_error_upper_bound": 0,
            "sum_other_doc_count": 0,
            "buckets": [
                {
                    "key": 28,
                    "doc_count": 1
                },
                {
                    "key": 90,
                    "doc_count": 1
                }
            ]
        },
        "grop_by_country": {
            "doc_count_error_upper_bound": 0,
            "sum_other_doc_count": 0,
            "buckets": [
                {
                    "key": "China",
                    "doc_count": 2
                }
            ]
        }
    }
}
```
```
{
	"aggs":{
		"grop_by_age":{
			"min":{
				"field":"age"
			}
		}
	}
}
```

### 高级查询
* 子条件查询（特定字段查询所指特定值）
	1. Query Context(查询过程中会计算一个`_score来标识匹配的程度)
	+ 全文本查询
```
	//name中
	{
		"query":{
			"match_phrase":{ 
				"name":"蒋其宏"
			}
		}
	}
	//name 和 desc中包含dd的文档
	{
		"query":{
			"multi_match":{ 
				"quwey":"dd",
				"fields":["name","desc"]
			}
		}
	}
	//语法查询
	{
		"query":{
			"query_string":{
				"query":"(aa AND bb) OR cc",
				fields:["name","desc"]
			}
		}
	}
	//
	{
		"query":{
			"term":{
				"name":"aa"
			}
		}
	}
	//范围查询
	{
		"query":{
			"range":{
				"age":{
					"gte":10,
					"lte":20
				}
			}
		}
	}
```
+ 字段级别查询
2. Filter Context(查询过程中。只判断该文档是否满足条件，YES or No)
```
	{
		"query":{
			"bool":{
				"filter":{
					"term":{
						"age":10
					}
				}
			}
		}
	}
```
* 复合条件查询（以一定的逻辑组合子条件查询）
# 整合springboot
* 配置类 EsConfig.java
```
import org.elasticsearch.client.transport.TransportClient;
import org.elasticsearch.common.settings.Settings;
import org.elasticsearch.common.transport.InetSocketTransportAddress;
import org.elasticsearch.transport.client.PreBuiltTransportClient;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.net.InetSocketAddress;


@Configuration
public class ESConfig {

    @Bean
    public TransportClient client() {

        //第二个参数是tpc端口 默认是9300
        //InetSocketAddress inetSocketAddress = new InetSocketAddress("127.0.0.1",9300);
        InetSocketTransportAddress inetSocketTransportAddress = new InetSocketTransportAddress(new InetSocketAddress("127.0.0.1",9300));
        Settings setting = Settings.builder()
                .put("cluster.name","qh")
                .build();
        TransportClient client = new PreBuiltTransportClient(setting);
        client.addTransportAddress(inetSocketTransportAddress);
        return client;
    }
}
```

* 具体操作
```
import org.elasticsearch.action.delete.DeleteResponse;
import org.elasticsearch.action.get.GetRequestBuilder;
import org.elasticsearch.action.get.GetResponse;
import org.elasticsearch.action.index.IndexResponse;
import org.elasticsearch.action.update.UpdateRequest;
import org.elasticsearch.action.update.UpdateResponse;
import org.elasticsearch.client.transport.TransportClient;
import org.elasticsearch.cluster.node.DiscoveryNode;
import org.elasticsearch.common.xcontent.XContentBuilder;
import org.elasticsearch.common.xcontent.XContentFactory;
import org.junit.Test;
import org.springframework.beans.factory.annotation.Autowired;

import java.util.Arrays;
import java.util.Date;
import java.util.List;
import java.util.Map;

/**
 * @Auther: Jiang Qihong
 * @Date: 2018/12/15 17:20
 * @Description:
 */
public class EsTest extends ApplicationTests {

    @Autowired
    private TransportClient client;



    @Test
    public void queryById(){
        GetRequestBuilder builder = client.prepareGet("people","man","1");
        GetResponse response =  builder.get();
        Map map = response.getSource();
        System.out.print(map);
    }


    @Test
    public void addPeople() throws Exception{

        XContentBuilder builder =  XContentFactory.jsonBuilder()
                .startObject()
                .field("name","peiqi")
                .field("age",23)
                .field("country","China")
                .field("date",new Date().getTime())
                .endObject();
        IndexResponse response = client.prepareIndex("people","man")
                .setSource(builder)
                .get();
        System.out.println(response);
    }


    @Test
    public void deletePeople() throws Exception {

        DeleteResponse response = client.prepareDelete("people","man","1").get();
        System.out.println(response);
    }


    @Test
    public void update() throws Exception {
        UpdateRequest request = new UpdateRequest("people","man","AWexQkPavE_7Btn7POuO");
        XContentBuilder builder = XContentFactory.jsonBuilder()
                .startObject()
                .field("name","qiaozhi")
                .field("age",6)
                .field("country","China")
                .field("date",new Date().getTime())
                .endObject();
        request.doc(builder);
        UpdateResponse response = client.update(request).get();
        System.out.println(request);

    }

}

```

