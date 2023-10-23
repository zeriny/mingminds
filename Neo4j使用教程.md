#Neo4j使用教程

https://www.w3cschool.cn/neo4j/neo4j_cypher_api_example.html

### Cypher

#### 删除

删除节点及关联属性

```cypher
MATCH (n:Cert)
DETACH DELETE n
```

删除关系

```cypher
MATCH ()-[r:Hosting]->() DELETE r
MATCH (:Artist)-[r:RELEASED]-(:Album) DELETE r
MATCH (:Artist {name: "筷子兄弟"})-[r:RELEASED]-(:Album {name: "猛龙过江"}) DELETE r
```

删除重复节点

1）找到重复节点

```cypher
MATCH (p:IP)
WITH p.ip as id, collect(p) AS nodes 
WHERE size(nodes) >  1
RETURN [ n in nodes | n.ip] AS ids, size(nodes)
ORDER BY size(nodes) DESC
LIMIT 10
```

2) 找到重复节点，并计算出相连关系数，再根据关系数从大到小排序

```cypher
MATCH (p:IP)
WITH p 
ORDER BY p.ip, size((p)--()) DESC
WITH p.ip as ip, collect(p) AS nodes 
WHERE size(nodes) >  1
RETURN [ n in nodes | {ip: n.ip, rels: size((n)--()) } ] AS ids, size(nodes)
ORDER BY size(nodes) DESC
LIMIT 10
```

3) 删除重复项

```cypher
MATCH (p:IP)
WITH p 
ORDER BY p.ip, size((p)--()) DESC
WITH p.ip as ip, collect(p) AS nodes 
WHERE size(nodes) >  1
UNWIND nodes[1..] AS n
DETACH DELETE n
```



#### 创建

创建唯一约束

```cypher
CREATE CONSTRAINT ON (p:IP)
ASSERT p.ip IS UNIQUE
```

创建节点

CREATE命令总是将新节点添加到数据库；MERGE会检查节点是否在数据库或不可用，如果它不存在则创建新的节点。



为已经存在的两个节点添加关系 (并为关系添加属性)

```cypher
MATCH (a:Person {name: "张三"}),
    (b:Person {name: "李四"})
MERGE (a)-[r:FRIENDS]->(b)
ON CREATE SET r+={follow:"20200101"}
```





#### 更新节点

```cypher
// 修改name属性，并添加age属性
MATCH (n {name:'张三'}) 
SET n.name='李四'
SET n.age=20 
RETURN n
  
MATCH (n {name:'张三'}) SET n += {age:20} 
RETURN n
```

```cypher
// 覆盖节点属性
MATCH (n {name:'张三'}) 
SET n={age:20}
```





