# Spark学习笔记

**从HDFS读取文件**

```scala
df = spark.read.parquet(path)
df = spark.read.option("header",true).csv("path1,path2,path3")
```

**保存文件到HDFS目录**

```scala
df.repartition(1).write.format("csv").option("header", "true").save(outputPath)

```



**自定义函数，并应用到dataframe某一列上**

```python
function_udf = func.udf(defined_function,  returnType=StringType())
new_df = df.withColumn("new_col_name",  function_udf(df.colname))
```



### 问题/坑

1. 使用PySpark中的UDF函数时，一些Library可能会失效

