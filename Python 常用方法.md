## Python 常用方法

### 配置文件

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



### Python tqdm模块

Tqdm是一个python模块，用来显示循环迭代的进度，官方Github链接<https://github.com/tqdm/tqdm>

#### 安装

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

#### 使用

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

#### 问题

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



### 操作文件系统

- `os.path.exists(path)` 判断一个目录是否存在
- `os.makedirs(path)` 多层创建目录
- `os.mkdir(path)` 创建目录



### csv 库

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



### pandas库

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

### 字符串编码处理

参考教程：[w3ccoo](https://www.w3ccoo.com/python/howto_unicode.asp#:~:text=1%20%E8%BD%AC%E6%8D%A2%E4%B8%BA%E5%AD%97%E8%8A%82%20bytes.decode%20%28%29%20%E7%9A%84%E9%80%86%E6%96%B9%E6%B3%95%E6%98%AF%20str.encode%20%28%29%20%EF%BC%8C%E5%AE%83%E4%BC%9A%E8%BF%94%E5%9B%9E,%E8%AE%A9%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E6%AF%94%E8%BE%83%E5%8F%98%E5%BE%97%E5%A4%8D%E6%9D%82%E4%BA%86%E4%B8%80%E4%BA%9B%EF%BC%8C%E5%9B%A0%E4%B8%BA%E5%90%8C%E4%B8%80%E7%BB%84%E5%AD%97%E7%AC%A6%E5%8F%AF%E8%83%BD%E7%94%B1%E4%B8%8D%E5%90%8C%E7%9A%84%E7%A0%81%E4%BD%8D%E5%BA%8F%E5%88%97%E7%BB%84%E6%88%90%E3%80%82%20...%205%20Unicode%20%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F%20re%20%E6%A8%A1%E5%9D%97%E6%94%AF%E6%8C%81%E7%9A%84%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F%E5%8F%AF%E4%BB%A5%E7%94%A8%E5%AD%97%E8%8A%82%E4%B8%B2%E6%88%96%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E5%BD%A2%E5%BC%8F%E6%8F%90%E4%BE%9B%E3%80%82%20)


#### bytes转str

可以用 bytes 的 decode() 方法创建一个字符串。 该方法可以接受 encoding 参数，比如可以为 UTF-8 ，以及可选的 errors 参数。

若无法根据编码规则对输入字符串进行编码，errors 参数指定了响应策略。 该参数的合法值可以是 'strict' (触发 UnicodeDecodeError 异常)、'replace' (用 U+FFFD、REPLACEMENT CHARACTER)、'ignore' (只是将字符从 Unicode 结果中去掉)，或 'backslashreplace' (插入一个 \xNN 转义序列)。 以下示例演示了这些不同的参数:
```shell
>>> b'\x80abc'.decode("utf-8", "strict")  
Traceback (most recent call last):
    ...
UnicodeDecodeError: 'utf-8' codec can't decode byte 0x80 in position 0:
  invalid start byte
>>> b'\x80abc'.decode("utf-8", "replace")
'\ufffdabc'
>>> b'\x80abc'.decode("utf-8", "backslashreplace")
'\x80abc'
>>> b'\x80abc'.decode("utf-8", "ignore")
'abc'
```

#### str转bytes
bytes.decode() 的逆方法是 str.encode()，它会返回 Unicode 字符串的 bytes 形式，已按要求的 encoding 进行了编码。

参数 errors 的意义与 decode() 方法相同，但支持更多可能的handler。除了 'strict' 、 'ignore' 和 'replace' （这时会插入问号替换掉无法编码的字符），还有 'xmlcharrefreplace' （插入一个 XML 字符引用）、 backslashreplace （插入一个 \uNNNN 转义序列）和 namereplace （插入一个 \N{...} 转义序列 ）。

以下例子演示了各种不同的结果：
```shell
>>> u = chr(40960) + 'abcd' + chr(1972)
>>> u.encode('utf-8')
b'\xea\x80\x80abcd\xde\xb4'
>>> u.encode('ascii')  
Traceback (most recent call last):
    ...
UnicodeEncodeError: 'ascii' codec can't encode character '\ua000' in
  position 0: ordinal not in range(128)
>>> u.encode('ascii', 'ignore')
b'abcd'
>>> u.encode('ascii', 'replace')
b'?abcd?'
>>> u.encode('ascii', 'xmlcharrefreplace')
b'&#40960;abcd&#1972;'
>>> u.encode('ascii', 'backslashreplace')
b'\ua000abcd\u07b4'
>>> u.encode('ascii', 'namereplace')
b'\N{YI SYLLABLE IT}abcd\u07b4'
```


#### Python编码清单
Python 有大约 100 种不同的编码格式；清单详见 Python 库参考文档 标准编码


#### 创建Unicode字符串
利用内置函数 chr() 还可以创建单字符的 Unicode 字符串，该函数可接受整数参数，并返回包含对应码位的长度为 1 的 Unicode 字符串。内置函数 ord() 是其逆操作，参数为单个字符的 Unicode 字符串，并返回码位值：
```shell
>>> chr(57344)
'\ue000'
>>> ord('\ue000')
57344
》》》 
```

#### Python中的Unicode文字
在 Python 源代码中，可以用 \u 转义序列书写特定的 Unicode 码位，该序列后跟 4 个代表码位的十六进制数字。\U 转义序列用法类似，但要用8 个十六进制数字，而不是 4 个：
```shell
>>> s = "a\xac\u1234\u20ac\U00008000"
... #     ^^^^ two-digit hex escape
... #         ^^^^^^ four-digit Unicode escape
... #                     ^^^^^^^^^^ eight-digit Unicode escape
>>> [ord(c) for c in s]
[97, 172, 4660, 8364, 32768]
```

