## Python 常用方法

#### 配置文件

config.ini

```ini
[NAME1]
CONFIG1 = xxx
CONFIG2 = yyy
```

test.py

```python
import configparser
config = configparser.ConfigParser()
config.read("./config.ini")
conf1 = config["NAME1"]["CONFIG1"]
conf2 = config["NAME1"]["CONFIG2"]
```



#### Python tqdm模块

Tqdm是一个python模块，用来显示循环迭代的进度，官方Github链接<https://github.com/tqdm/tqdm>

##### 安装

获取最新版本的PyPI stable release

```shell
pip install tqdm
#pip3 install tqdm
```
Docker release
```shell
docker pull tqdm/tqdm
docker run -i --rm tqdm/tqdm --help
```

##### 使用

基本用法

```python
from tqdm import tqdm
import time
text = ""
for char in tqdm(["a", "b", "c", "d"]):
    time.sleep(0.25)
    text = text + char
```
```shell
100%|██████████| 4/4 [00:01<00:00,  3.93it/s]
```

为进度条添加描述

```python
pbar = tqdm(["a", "b", "c", "d"])
for char in pbar:
    time.sleep(0.25)
    pbar.set_description("Processing %s" % char)
```

```shell
Processing d: 100%|██████████| 4/4 [00:06<00:00,  2.24s/it]
```

在循环结束记得close掉tqdm对象

```python
pbar = tqdm(total=100)
for i in range(10):
    time.sleep(0.1)
    pbar.update(10)
pbar.close()
```

当然也可以通过with的方法调用，避免忘记del或close（）

```python
with tqdm(total=100) as pbar:
    for i in range(10):
        time.sleep(0.1)
        pbar.update(10)
```

##### 问题

在使用的过程中，有时调试时手动中断循环过程，再次启用tqdm时会出现进度条无法在同一行显示的现象。原因: 有些 IDE 不支持回车(CR), 导致每次新的进度条不能覆盖旧的进度条, 出现原因的问题也可能是手动终止进程导致 tqdm 没有完全退出导致的, 应使用 `t.close()` 使其正确关闭:

```python
try:
    with tqdm(...) as t:
        for i in t:
            ...
except KeyboardInterrupt:
    t.close()
    raise
t.close()
```



#### 操作文件系统

- `os.path.exists(path)` 判断一个目录是否存在
- `os.makedirs(path)` 多层创建目录
- `os.mkdir(path)` 创建目录



#### csv 库

写入.csv文件

```python
import csv

keys = ['key1', 'key2', 'key3']
values = [
  ['a', 'b', 'c'],
  ['d', 'e', 'f'],
  ['g', 'h', 'i']
]
with open(output_file, 'a+') as f:
    csv_write = csv.writer(f)
    # 先写入表头
    csv_write.writerow(keys)
    # 写入表数据
    for value in values:
      csv_write.writerow(value)
```



#### pandas库

读取excel文件

```python
import pandas as pd

df = pd.read_excel(filename, sheet_name=1)
count_dict = dict()
for row_id, row_list in df.iterrows():
  value1 = row_list['key1']
  value2 = row_list['key2']
```

写入excel文件

```python
df.to_excel(filename, sheet_name=attr, encoding='utf-8')
```

但是这个方法每次写入excel时，都会重新创建一个新的文件（或覆盖原始文件），所以如果想写入多个表数据，只能得到最后一个DF写入的结果。对此，我们可以创建一个ExcelWriter对象来解决上述问题：

```python
writer = pd.ExcelWriter(path=filepath)
df1.to_excel(writer, sheet_name=sheetname, encoding='utf-8')
df2.to_excel(writer, sheet_name=sheetname, encoding='utf-8')
df3.to_excel(writer, sheet_name=sheetname, encoding='utf-8')
df4.to_excel(writer, sheet_name=sheetname, encoding='utf-8')
writer.save() # 一定要save, 才能创建excel工作簿并将上面的dataframe真正写到该文件里
```

或者，也可以将多个表数据写入excel同一个工作不的同一个工作表里：

```python
writer = pd.ExcelWriter(path=filepath)
df1.to_excel(writer, sheet_name=sheetname)
df2.to_excel(writer, sheet_name=sheetname, startcol=10)
df3.to_excel(writer, sheet_name=sheetname, startrow=20)
df4.to_excel(writer, sheet_name=sheetname, startrow=30, startcol=30)
writer.save() 
```



