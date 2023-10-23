# Go环境配置

### Linux

**安装GO**

1. 下载go安装包

32位：wget https://storage.googleapis.com/golang/go1.7.3.linux-386.tar.gz

64位：

1. 版本说明：基于项目时，考虑兼容性安装，否则安装最新版本
2. 解压缩go1.8.3.linux-amd64.tar.gz得到文件夹go
3. 将文件夹go放到/usr/local中，得到/usr/local/go
4. 在/home/xxxx/.bash_profile中添加go的环境变量（或/etc/profile）：
   1. export PATH=$PATH:/usr/local/go/bin
   2. export GOROOT=/usr/local/go

**创建GO工程目录**

go工程目录只能创建在用户目录

1. 创建工程目录：/home/xxx/GoglandProjects
2. 配置GOPATH：export GOPATH=/home/xxx/GoglandProjects
3. 设置GOPATH的bin环境变量：export PATH=$PATH:$GOPATH/bin

其中，xxx为用户名，eg:/home/erin/…….

$source /etc/profile 更新

**安装Beego框架**

参考官方文档：https://beego.me/

1. 安装beego：$ go get [github.com/astaxie/beego](http://github.com/astaxie/beego)
2. 安装bee：$ go get [github.com/beego/bee](http://github.com/beego/bee) （注意不需要sudo）
3. Permission denied: cd到go的安装路径下（eg. /usr/local）$sudo chmod -R 777 go/



### Windows

下载go：https://www.golangtc.com/download

![image-20220814110455235](/Users/evelyn/Library/Application Support/typora-user-images/image-20220814110455235.png)