### 数据切包

```
editcap -c 500000 q1_final.pcap q1_split.pcap # 分成了21个数据包
```



### TShark 1.10.14 

- 以5秒为单位，统计不同方向上的数据包个数

```shell
tshark -z io,stat,5,"ip.addr!=45.80.170.1" -n -q -r q1_final.pcap > not-ip.csv
```

 利用 tshark 输出 数据包的摘要 (https://www.zkaq.org/?t/1137.html)

```
幸运的是，tshark可以让我们指定我们希望看到的确切字段。使用命令“-T fields”来标识我们希望指定要查看的确切字段，而不是显示默认信息。然后可以使用“-e”来标识要打印的特定字段。

下面一个只打印源和目标IP地址的栗子：tshark -r interesting-host.pcap -T fields -e ip.src -e ip.dst ip.dst==192.168.1.10 | head
```

如果我想要把它组织起来，我可以添加`“-E header = y”`开关，如最最最上面的那张图所示。这将打印出第一行列标题。

在本次DNS流量检测使用的命令(-e 的参考：https://www.wireshark.org/docs/dfref/d/dns.html)

```shell
tshark -r tcp_q1_final.pcap -Y "dns" -T fields -E separator=, -e frame.time_delta_displayed -e frame.len -e ip.src -e ip.dst -e ip.proto -e ip.len -e ip.ttl -e tcp.srcport -e tcp.dstport -e udp.srcport -e udp.dstport -e tcp.len -e dns.id -e dns.flags.opcode -e dns.flags.response -e dns.count.queries -e dns.count.answers -e dns.count.auth_rr -e dns.count.add_rr -e dns.flags.rcode -e dns.qry.type -e dns.qry.name| head 
# _________________________
tshark -r not-ip.pcap -Y "dns" -T fields -E separator=, -e frame.time_delta_displayed -e frame.len -e ip.src -e ip.dst -e ip.proto -e ip.len -e ip.ttl -e tcp.srcport -e tcp.dstport -e udp.srcport -e udp.dstport -e tcp.len -e dns.id -e dns.flags.opcode -e dns.flags.response -e dns.count.queries -e dns.count.answers -e dns.count.auth_rr -e dns.count.add_rr -e dns.flags.rcode -e dns.qry.type -e dns.qry.name| head 
#_____________
tshark -r q1_final.pcap -Y "dns" -T fields -E separator=, -e udp.stream -e tcp.stream -e frame.time_delta_displayed -e frame.len -e ip.src -e ip.dst -e ip.proto -e ip.len -e ip.ttl -e tcp.srcport -e tcp.dstport -e udp.srcport -e udp.dstport -e tcp.len -e dns.id -e dns.flags.opcode -e dns.flags.response -e dns.count.queries -e dns.count.answers -e dns.count.auth_rr -e dns.count.add_rr -e dns.flags.rcode -e dns.qry.type -e dns.qry.name > feature-tshark.extract
#_____________
tshark -r q1_final.pcap -Y "dns" -T fields -E separator=, -e udp.stream -e tcp.stream -e frame.number -e frame.time -e frame.len -e ip.src -e ip.dst -e ip.proto -e ip.len -e ip.ttl -e tcp.srcport -e tcp.dstport -e udp.srcport -e udp.dstport -e tcp.len -e dns.id -e dns.flags.opcode -e dns.flags.response -e dns.count.queries -e dns.count.answers -e dns.count.auth_rr -e dns.count.add_rr -e dns.flags.rcode -e dns.qry.type -e dns.qry.name > feature-tshark/feature-tshark1.extract
#____________
tshark -r q2_final.pcap -Y "dns" -T fields -E separator=, -e dns.qry.name -e dns.qry.name.len > feature-tshark/feature-tshark1.extract

-R 读取过滤规则，需要用双引号引起来。 需要 -2 参数（这个参数的意义还没彻底搞懂）
-Y 显示过滤规则
-n ===>禁用名称解析功能
-e 指定要显示的字段
frame.time_delta_displayed 精确级别比较高的
frame.len 单位字节
dns.qry.type：A是1，AAAA是2，CNAME是5，ANY是255
来自 <https://osqa-ask.wireshark.org/questions/37423/tshark-dns-query-type> 

tshark -r q1_final.pcap -Y "dns and dns.flags.response == 0" -T fields -e frame.time_delta_displayed > dnsquery_time.extract
```

 https://ask.wireshark.org/question/2405/how-do-i-extract-the-individual-flows-from-the-total-packets-in-a-pcap-file/ 标识流tcp.stream 但是没有udp.stream

 自定义格式： 

```
tshark -r not-ip.pcap -R "dns" -o column.format:""No.","%m", "Time", "%Yt","Source", "%s", "ID", "%Cus:dns.id", "Info", "%i"" | head
```

 

### 基础知识

 数据包时间在Frame层 

> IP是网络层协议，IP头中的协议号用来说明IP报文中承载的是哪种协议（一般是传输层协议，比如6 TCP，17 UDP；但也可能是网络层协议，比如1 ICMP；也可能是应用层协议，比如89 OSPF）。

> TCP/UDP是传输层协议，TCP/UDP的端口号用来说明是哪种上层应用，比如TCP 80代表WWW，TCP 23代表Telnet，UDP 69代表TFTP。

> 目的主机收到IP包后，根据IP协议号确定送给哪个模块（TCP/UDP/ICMP...）处理，送给TCP/UDP模块的报文根据端口号确定送给哪个应用程序处理。

 

### tcpdump

> 过滤速度最快，而且是实时输出！

- 最简单的–过滤出 src.pcap 中端口号为 22 的数据包

```
tcpdump -Z root -r src.pcap "tcp port 22" -w dst.pcap
```

- 指定interface

```
tcpdump -i eth0 -c 10
```

- 过滤条件写法

```
“udp”
"tcp host www.baidu.com"
"port 443", "dst port 443 or dst port 80"

```



- 过滤出端口为22， 且含有 FIN 标记的数据包

```
tcpdump -Z root -r src.pcap "tcp port 22 and (tcp[tcpflags] & tcp-fin != 0)" -w dst.pcap
```

- 根据应用层数据进行过滤，如HTTP GET的请求路径， 注意tcp[xx:offset]中的偏移最多为4

```
## 示例: GET /bidimg/hello
# tcp[24:4]==0x2f626964 匹配 /bid; tcp[28:4]==696d67ef 匹配 img/ 字段; 至于GET字段的匹配，可以自己去尝试！
tcpdump -Z root -r src.pcap "((tcp[24:4]==0x2f626964 and tcp[28:4]==696d67ef) and dst port 80)" -w dst.pcap
 
```

### 参考

 http://codeshold.me/2017/08/tcpdump_tshark_note.html 很多实例 

- tshark 是一次性将整个数据包读入内存的，分析好后再统一输出，所以针对超大文件的分析，需要注意！**但是和wireshark相比，tshark能分析的文件已经很大了**，具体和系统配置有关！

 

### Tshark 

tshark -r q1_final.pcap -Y "dns" -T fields -E separator=, -e frame.number -e udp.dstport -e dns.flags.rcode > results/find_udp_flood.txt