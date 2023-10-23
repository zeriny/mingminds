# ElasticSearch学习笔记

文档链接：<https://www.elastic.co/guide/en/elasticsearch/reference/6.0/_basic_concepts.html>

下载链接：http://www.elastic.co/downloads



#### Cluster Health

- 利用_cat API	

```sh
GET /_cat/health?v

curl -X GET "localhost:9200/_cat/health?v"
```

- 获得node list

```sh
GET /_cat/nodes?v

curl -X GET "localhost:9200/_cat/nodes?v"
```



#### 获取所有索引 Indices

```sh
GET /_cat/indices?v

curl -X GET "localhost:9200/_cat/indices?v"
```



#### 增加索引

例如增加一个customer字段

```sh
curl -X PUT "localhost:9200/customer?pretty"
curl -X GET "localhost:9200/_cat/indices?v"
```



#### 向指定字段插入或查询数据

```sh
#向customere中插入数据，index为1，value为John Doe
curl -X PUT "localhost:9200/customer/doc/1?pretty" -H 'Content-Type: application/json' -d'
{
  "name": "John Doe"
}
'

#response 
{
  "_index" : "customer",
  "_type" : "doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}

#取出刚刚插入的数据
curl -X GET "localhost:9200/customer/doc/1?pretty"

#response
{
  "_index" : "customer",
  "_type" : "doc",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : { "name": "John Doe" }
}

#查询所有数据
GET _search
{
  "query": {
    "match_all": {}
  }
}
```



#### 删除索引

```sh
curl -X DELETE "localhost:9200/customer?pretty"
curl -X GET "localhost:9200/_cat/indices?v"
```



#### 修改数据

直接重新插入一次就会覆盖原始值



#### 更新Document

```sh
#将doc中的ID=1的name改为Jane Doe
curl -X POST "localhost:9200/customer/doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
  "doc": { "name": "Jane Doe" }
}
'
#将doc中ID=1的name改为Jane Doe，并添加age field
curl -X POST "localhost:9200/customer/doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
  "doc": { "name": "Jane Doe", "age": 20 }
}
'
#通过简单的script将年龄增加5
curl -X POST "localhost:9200/customer/doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
  "script" : "ctx._source.age += 5"
}
'
```



#### 批处理数据

```sh
#方法一
curl -X POST "localhost:9200/customer/doc/_bulk?pretty" -H 'Content-Type: application/json' -d'
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }
'
#修改id=1的名字，然后删除id=2的数据
curl -X POST "localhost:9200/customer/doc/_bulk?pretty" -H 'Content-Type: application/json' -d'
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}
'

#方法二 从文件中批量导入数据
curl -H "Content-Type: application/json" -XPOST 'localhost:9200/bank/account/_bulk?pretty&refresh' --data-binary "@accounts.json"
curl 'localhost:9200/_cat/indices?v'

#accounts.json中的数据格式全部为：
{
    "account_number": 0,
    "balance": 16623,
    "firstname": "Bradshaw",
    "lastname": "Mckenzie",
    "age": 29,
    "gender": "F",
    "address": "244 Columbus Place",
    "employer": "Euron",
    "email": "bradshawmckenzie@euron.com",
    "city": "Hobucken",
    "state": "CO"
}
```



#### 查询数据

```sh
GET /symantect/certs/_search?q=SAN:flowers

#查询所有数据
GET _search
{
  "query": {
    "match_all": {}
  }
}

```





#### 问题

1. 如何远程访问elasticsearch

   ```sh
   vim config/elasticsearch.ym
   
   #增加
   network.host:0.0.0.0
   ```

   ```sh
   #报错
   [2]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
   
   $sysctl -w vm.max_map_count=262144
   #并重新登录用户
   ```

   ```sh
   #继续报错
   ERROR: [1] bootstrap checks failed
   [1]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
   ```

2. 

