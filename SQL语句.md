## SQL语句

根据表（cert_info）中的某一项（cert_md5）查询重复的记录

```sql
select * from fdp.cert_info where cert_md5 in (select  cert_md5  from  fdp.cert_info  group  by  cert_md5  having  count(cert_md5) > 1)
```

查询同一列所有值出现的次数

```sql
select issuer_organization as issuer, count(issuer_organization) as count from fdp.cert_info group by issuer_organization
```

