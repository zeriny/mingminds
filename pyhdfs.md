## pyhdfs

```python
import pyhdfs

ROOT_PATH = "/home/fdp/certificate/ct/"
hdfs_hosts = "m2.bigdata.lyjt.360es.cn,m3.bigdata.lyjt.360es.cn"
client = pyhdfs.HdfsClient(hosts=hdfs_hosts, user_name='zhangmingming')


rootpath =  client.get_home_directory()

client.listdir("/user/hadoop")

# 打开一个远程节点上的文件，返回一个HTTPResponse对象
response = client.open("/user/hadoop/speech_text.txt")
 
# 查看文件内容
response.read()

# 从本地上传文件至集群之前，集群的目录
print "Before copy_from_local"
print client.listdir("/user/hadoop")
 
# 从本地上传文件至集群
client.copy_from_local("D:/Jupyter notebook/ipynb_materials/src/test.csv","/user/hadoop/test.csv")
 
# 从本地上传文件至集群之后，集群的目录
print "After copy_from_local"
print client.listdir("/user/hadoop")


# 向一个已经存在的文件中插入文本
# 先看看文件中的内容
response = client.open("/user/hadoop/test.csv")
response.read()

# 使用append函数插入string
client.append("/user/hadoop/test.csv","0,2,0\r\r\n")
 
# 再看看文件中的内容
response = client.open("/user/hadoop/test.csv")
response.read()


```