## Android手机安装证书

安卓7.0之后，系统安全策略升级，很多app默认只信任系统证书，直接在手机中安装的是用户证书，所以很多app的https流量无法抓包分析。两种解决方案：

1. 修改apk包--添加证书--重新打包
2. 手机root后--添加系统证书

#### 添加用户级根证书

1. 证书传到手机上，命名为xxx.crt(不同手机可能可识别证书后缀不一样，基本是.crt或.cer)
2. 点击证书文件安装，或“设置-安全和隐私-更多安全设置-从SD卡安装”

#### 添加系统根证书

1. 手机要root

2. 电脑安装adb，并连接手机

   ```shell
   #查看已连接设备
   $adb devices
   
   #将证书文件push到手机文件系统
   $adb push path/to/cert /sdcard
   
   #连到手机shell
   $adb shell
   > $su
   > #重新挂载/system为可读可写权限
   > $mount -o remount, rw /system
   > $cp /sdcard/certfile /system/etc/security/cacerts/
   
   > #修改证书文件权限！！(重要)！
   > $chmod 644 /system/etc/security/cacerts/certfile
   > $exit
   
   #重启设备
   $adb reboot
   ```

   

> 注：
>
> 1. 直接传文件到/system下，会遇到权限问题“Read-only file system”，需要重新mount /system为可读可写的权限；
> 2. 但是在mount时可能会遇到mount: '/system' not in /proc/mounts错误 （原因和解决方法不明，无奈只能换测试手机了）
> 3. 华为测试机通过我电脑adb连不上: 提示unauthorized（无解，在博博电脑上可以连，无奈只能用他电脑往手机的文件系统传文件了）



#### 网上给的第三种解决方案（未尝试）

1. 安装 虚拟大师，官网:

2. 配置证书
   1. 打开电脑上charles（burpsuite类似），配置手机网络代理到电脑charles

   2. 打开虚拟大师，配置虚拟大师的网络到charles地址

   3. 通过浏览器访问 [https://chls.pro/ssl](https://links.jianshu.com/go?to=https%3A%2F%2Fchls.pro%2Fssl) （but-suite 访问http://burp）下载证书到手机

   4. 把下载证证书传到电脑，执行如下命令

      ```
      openssl x509 -subject_hash_old -in getssl.crt
      ```

      得到一个 3fd23b4d 这样的字符串

3. 重命名getssl.crt 为 3fd23b4d.0

4. 拷贝 3fd23b4d.0 到虚拟大师的 /system/etc/security/cacerts/



#### 参考链接

https://www.jianshu.com/p/3d981ae674c5

https://segmentfault.com/a/1190000017035564)https://segmentfault.com/a/1190000017035564