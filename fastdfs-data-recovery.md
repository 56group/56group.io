---
title: FastDFS数据恢复
date: 2022-07-01 09:51:00
tags: [FASTDFS]
categories: FASTDFS
---

### FastDFS数据恢复

##### 旧FastDFS服务器

```
shell> cat /etc/fdfs/storage.conf # 查看旧服务的storage的文件存储路径
# store_path0 = /var/fastdfs/files 或者 base_path = /var/fastdfs/storage
# 注:如果没有store_pathX(X表示具体的存储位置数),需要备份的数据就在base_path下面
# 压缩store_pathX下的data目录(文件数据目录)
shell> cd $store_path0
shell> tar -zcf storage_data.tar.gz data/
# 压缩tracker下的data目录
shell> cat /etc/fdfs/tracker.conf # 查看旧服务的tracker的文件存储路径
shell> cd $base_path # 注意是tracker.conf文件中的
shell> tar -zcf tracker_data.tar.gz data/
```

##### 新FastDFS服务器storage数据恢复

```
# 上传storage_data.tar.gz到新服务器
shell> cat /etc/fdfs/storage.conf # 查看新服务的storage的文件存储路径
shell> /usr/bin/fdfs_storaged /etc/fdfs/storage.conf stop
shell> /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf stop
shell> cd $store_path0/
shell> cd ..
shell> mv $store_path0 NAME_YYYYMMDDHHMM
shell> mv $storage_data.tzr.gz .
shell> mkdir $store_path0/
shell> tar -xvf storage_data.tzr.gz -C $store_path0/ # 解压后目录下面有对应的data目录
```

> 注意:上面的过程是只恢复(迁移)了store_path0,如果要迁移其他的数据,重复上面的store_path0的步骤

> 注意:如果在恢复storage的data目录中.data数据目录($store_path0/data)下有.data_init.flag和data数据同步目录($store_path0/data/sync)下有$ip_addr_$port.mark文件更改这几个文件.data_init.flag和*.mark中的ip为当前storage服务的ip或者tracker服务的Ip,应为本次迁移没有这个文件所以没有执行这一步操作

##### 新FastDFS服务器tracker数据恢复

> 注:编写此脚本时候项目只有store_path0一个数据存储节点,也是只有一个storage和一个tracker服务,所以没有在新的服务器上恢复tracker的data目录数据

```
shell> cat /etc/fdfs/tracker.conf # 查看新服务的tracker的文件存储路径 base_path
shell> cd $base_path # 注是tracker.conf文件配置的base_path
shell> cd ..
shell> mv $base_path NAME_YYYYMMDDHHMM
shell> tar -xvf tracker_data.tzr.gz -C $base_path
shell> vi $base_path/data/storage_groups_new.dat # 更改其中ip为新服务的ip地址
shell> vi $base_path/data/storage_servers_new.dat # 更改其中ip为新服务的ip地址
shell> vi $base_path/data/storage_sync_timestamp.dat # 更改其中ip为新服务的ip地址
```

---

***本次恢复过这种本质的操作只有一个就是把旧机器的storage服务中的storage.conf配置文件中的store_path0下的data目录的所有数据打包压缩放到新的服务器的storage服务中storage.conf配置文件中store_path0目录的data中,其实就是data目录到对应的data的复制和粘贴**

- 1.在新服务器安装FastDFS服务 
- 2.打包旧storage服务的对应的存储的data文件夹 
- 3.停止新服务器的tracker和storeage服务 
- 4.备份新storage服务的对应的存储的data文件夹 
- 5.上传旧服务器的data文件 
- 6.解压到新的storage的存储目录中,作为新的storage的服务的data目

> 注:没有备份和迁移旧的tracker服务的数据,相当于只把旧服务的data放过来就可以了