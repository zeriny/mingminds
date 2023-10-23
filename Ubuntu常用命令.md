## Ubuntu常用命令

##### 登录server

ssh username@server

##### 本地传文件到服务器

sudo scp -r /home/erin/桌面/new_proxysite.tar.gz ubuntu@202.112.26.109:/home/ubuntu/new_proxysite



#####Service

$service --status-all  查看所有service状态

$service httpd status  查看某个service状态

$service httpd start  启动服务



#####关于pcap

$ editcap -F libpcap sourcefile destfile.pcap 将.pcapng文件转换为.pcap



#####tar压缩解压缩

tar

tar命令可以用来压缩打包单文件、多个文件、单个目录、多个目录。

常用格式：

单个文件压缩打包 tar czvf my.tar file1

多个文件压缩打包 tar czvf my.tar file1 file2,...

单个目录压缩打包 tar czvf my.tar dir1

多个目录压缩打包 tar czvf my.tar dir1 dir2

解包至当前目录：tar xzvf my.tar



将cm-11.tar.gz分割成N个指定大小的文件：

split -b 4000M -d -a 1 cm-11.tar.gz cm-11.tar.gz.

//使用split命令，-b 4000M 表示设置每个分割包的大小，单位还是可以k

// -d "参数指定生成的分割包后缀为数字的形式

//-a x来设定序列的长度(默认值是2)，这里设定序列的长度为1



分割后的压缩包解压命令：

cat cm-11.tar.gz.* | tar -zxv



##### 查看文件个数

统计某文件夹下文件的个数

ls -l |grep "^-"|wc -l



统计某文件夹下目录的个数

ls -l |grep "^ｄ"|wc -l



统计文件夹下文件的个数，包括子文件夹里的

ls -lR|grep "^-"|wc -l



如统计/home/han目录(包含子目录)下的所有js文件则：

ls -lR /home/han|grep js|wc -l 或 ls -l "/home/han"|grep "js"|wc -l



统计文件夹下目录的个数，包括子文件夹里的

ls -lR|grep "^d"|wc -l

说明：

ls -lR

长列表输出该目录下文件信息(R代表子目录注意这里的文件，不同于一般的文件，可能是目录、链接、设备文件等)



grep "^-"

这里将长列表输出信息过滤一部分，只保留一般文件，如果只保留目录就是 ^d



wc -l

统计输出信息的行数，因为已经过滤得只剩一般文件了，所以统计结果就是一般文件信息的行数，又由于一行信息对应一个文件，所以也就是文件的个数。



##### OpenSSL

查看证书列表

$openssl crl2pkcs7 -nocrl -certfile ca-certificates.crt | openssl pkcs7 -print_certs -text -noout

$keytool -printcert -v -file ca-certificates.crt



