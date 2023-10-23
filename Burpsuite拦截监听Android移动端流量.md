## Burpsuite拦截监听Android移动端流量

- 首先启动Burpsuite，启动代理模式

  Proxy -> Options -> Proxy Listeners -> Add -> Bind Port + Address

  ![image-20200828101425947](/Users/evelyn/Library/Application Support/typora-user-images/image-20200828101425947.png)

  如果只监听本地流量选Loopback Only，监听手机流量可以选择Specific Address，然后选择自己的IP地址。

- 手机和PC端接入同一个Wi-Fi，进入Wi-Fi高级设置代理，选择手动添加，输入服务器主机名（代理的域名或IP地址，此处为想要连接的电脑IP），输入服务器端口号（此处为burpsuite中设置的拦截端口），然后点击保存即可。

![image-20200828102722740](/Users/evelyn/Library/Application Support/typora-user-images/image-20200828102722740.png)

- 然后通过手机上网，Burpsuite就可以拦截到手机的流量（在HTTP history中查看）。

  ![image-20200828103610261](/Users/evelyn/Library/Application Support/typora-user-images/image-20200828103610261.png)

- 如果想要作为中间人监听HTTPS流量，则需要在手机浏览器中访问http://burp/或者http://your-pc-ip/，点击右上角的CA Certificate下载证书。Android手机可以手动安装.cer文件格式的SSL证书，所以如果下载到的是.der格式文件需要自己修改一下文件后缀。然后在手机“设置”里搜索“证书”，选择"安装证书"->从本地文件系统找到刚刚下载好的SSL证书文件，选择安装。