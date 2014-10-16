title: MySQL常用命令
date: 2014-10-15 11:07:55
tags: mysql
categories:
---
##修改数据库编码

用alter语句. 如果数据库已经有数据表了, 那每个表都要修改. (修改数据库的字符集不会改变原有数据表的字符集)
utf8:
```sql
ALTER DATABASE `数据库` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci
ALTER TABLE `数据表` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci
```
gbk (包含gb2312):
```sql
ALTER DATABASE `数据库` DEFAULT CHARACTER SET gbk COLLATE gbk_chinese_ci
ALTER TABLE `数据表` DEFAULT CHARACTER SET gbk COLLATE gbk_chinese_ci
```

