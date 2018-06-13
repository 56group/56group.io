---
title:  SQL编码优化
date: 2018-06-12 09:10:00
tags: [SQL, 优化]
categories:  SQL
---

### SQL编码优化

##### 单条记录查询优化

```
LIMIT 0,1
```

##### NOT EXIST 和 NOT IN 优化

```
# NOT IN 和 NOT EXIST在SQL查询中性能比较差,可以用LEFT JOIN链接两个表,然后通过IS NOT NULL获取值
mysql> SELECT SP.permissionId, permissionName FROM system_permission SP LEFT JOIN (SELECT roleId, permissionId FROM system_role_permission WHERE roleId = #{value}) T ON SP.permissionId = T.permissionId WHERE T.roleId IS NULL
```