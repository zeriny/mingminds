# Go环境配置与常用命令

# 安装

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



### macOS m1

需要arm64的发行版本



# go vendor机制

1. 从 Go1.6 起，默认开启 vendor 机制。所谓 vendor 机制，就是每个项目的根目录下可以有一个 vendor 目录，里面存放了该项目的依赖的 package。go build 的时候会先去 vendor 目录查找依赖，如果没有找到会再去 GOPATH 目录下查找。

   vendor 将原来包共享模式转换为每个工程独立维护的模式，vendor的另一个好处是保证了工程目录下代码的完整性，将工程代码复制到其他Go编译环境，不需要再去下载第三方包，直接就能编译，这种隔离和解耦的设计思路是一大进步。

2. 

# go mod使用

1. 参考：https://cloud.tencent.com/developer/article/2020911

2. 使用go modules时，项目代码可以放在任意位置，不必非要放在GOPATH目录下

3. 临时环境变量

   ```shell
   $ set GO111MODULE=on
   $ set GOPROXY=https://goproxy.io #也可以用阿里云的代理https://mirrors.aliyun.com/goproxy
   ```

4. 下载依赖项

   ```bash
   $ go get -u github.com/kataras/iris/v12@latest
   # 此时这些包并没有下载到 GOPATH/src 目录下，而是在 GOPATH/pkg/mod 目录中。
   ```

5. 初始化go.mod文件：go modules 最重要的是 go.mod 文件的定义，它用来标记一个 module 和它的依赖库以及依赖库的版本。会放在 module 的主文件夹下，一般以 go.mod 命名。

   ```bash
   $ go mod init example.com/myproject #在项目的根目录下生成一个go.mod文件,包含模块信息和依赖信息
   $ go mod tidy #加载module->go.mod
   ```

5. go.mod内容

   - 模块路径
   - Go的版本信息
   - 依赖信息：语义化版本

   ```golang
   module github.com/dablelv/go-huge-util
   
   go 1.17
   
   replace github.com/coreos/bbolt => ../r
   
   require (
   	github.com/cenk/backoff v2.2.1+incompatible
   	github.com/edwingeng/doublejump v0.0.0-20200330080233-e4ea8bd1cbed
   	github.com/go-sql-driver/mysql v1.5.0
   	github.com/spf13/cast v1.4.1
   	github.com/stretchr/testify v1.7.0
   	golang.org/x/text v0.3.2
   )
   
   require (
   	github.com/davecgh/go-spew v1.1.1 // indirect
   	github.com/pmezard/go-difflib v1.0.0 // indirect
   	gopkg.in/yaml.v3 v3.0.0-20200313102051-9f266ea9e77c // indirect
   )
   
   exclude (
   	go.etcd.io/etcd/client/v2 v2.305.0-rc.0
   	go.etcd.io/etcd/client/v3 v3.5.0-rc.0
   )
   
   retract (
       v1.0.0 // 废弃的版本，请使用v1.1.0
   )
   ```

    indirect 表示间接的使用了这个库：

   - 当前项目依赖 A，但是 A 的go.mod 遗漏了 B，那么就会在当前项目的 go.mod 中补充 B，加 indirect 注释；
   - 当前项目依赖 A，但是 A 没有 go.mod，同样就会在当前项目的 go.mod 中补充 B，加 indirect 注释；
   - 当前项目依赖 A，A 又依赖 B。当对 A 降级的时候，降级的 A 不再依赖 B，这个时候 B 就标记 indirect 注释。我们可以执行`go mod tidy`来清理不依赖的 module。

   replace用来解决一些错误的依赖库的引用或者调试依赖库

   - replace github.com/panicthis/A v1.1.0 => github.com/panicthis/R v1.8.0 
   - replace github.com/coreos/bbolt => ../r

6. **go.sum文件**：跟随 go.mod 文件一起被创建的还有 go.sum 文件。该文件包含依赖模块的校验和。保证下载的模块的完整性。

7. **更新依赖：**初始化之后，可以使用 go get 增加新的依赖或者更新依赖。执行后会更新go.mod文件的内容。
8. **版本控制：**Go 模块使用语义版本管理（semver）来管理依赖关系。在 go.mod 文件中，你可以指定模块所需的依赖关系的最小版本，Go 模块会尝试使用最新的兼容版本。
9. **Vendor Directory：**默认情况下，Go 模块会将依赖关系存储在项目中的 Vendor Directory中。该目录包含模块及其依赖项的源代码副本。



# 注意事项

1. main package默认不会加载其他文件，其他package都是默认加载的。如果 main 包有多个文件，则在执行的时候需要将其它文件都带上，即执行 go run *.go。