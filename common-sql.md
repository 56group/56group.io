---
title:  SQL常用代码
date: 2018-09-20 14:27:00
tags: [常用, SQL, CODE]
categories:  常用
---
### 常用SQL

##### 使用LEFT JOIN代替IN和NOT IN

```
# IN
SELECT
    RP.repairGroupId, groupName
FROM
    bus_basic_repair_group RP
        LEFT JOIN
    bus_basic_repair_group_printer RGP ON RP.repairGroupId = RGP.repairGroupId
WHERE
    RGP.printerId = #{value}

# NOT IN
SELECT
    RP.repairGroupId, RP.groupName
FROM
    bus_basic_repair_group RP
        LEFT JOIN
    (SELECT
        repairGroupId, printerId
    FROM
        bus_basic_repair_group_printer
    WHERE
        printerId = #{value}) A ON A.repairGroupId = RP.repairGroupId
WHERE
    A.repairGroupId IS NULL
```

##### 查询一个数据库有多少个表

```sql
mysql> SELECT COUNT(*) TABLES, table_schema FROM information_schema.TABLES WHERE table_schema = '数据库名';
```
