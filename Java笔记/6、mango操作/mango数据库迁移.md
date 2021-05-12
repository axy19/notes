# mango数据库迁移

1、切换到mango的安装目录的/bin目录下

2、先将要迁移的库整导出

```
mongodump -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -o 文件存在路径 
```

如果没有用户谁，可以去掉-u和-p。
如果导出本机的数据库，可以去掉-h。
如果是默认端口，可以去掉--port。
如果想导出所有数据库，可以去掉-d。

举例：

```
mongodump -h 172.30.7.217:20000 -d formal_wdntest -o /opt/mongodb/data_export
mongodump -h 172.30.7.217:20000 -d dw_test -o /opt/mongodb/data_export
```

公司data环境导出的命令：

```
mongodump -h 127.0.0.1:10001 -u root -p Abc123\!\@Gwssi -d jck_metadata_dev -o /home/dbDataBak/mongoBak/20200921-zwp --gzip
```

3、将要迁入的数据库做一个备份，以防数据错误

4、将导出的数据还原到要迁移

```
mongorestore -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 --drop 文件存在路径 
```

举例：

```
mongorestore -h 172.30.7.217:20000 -d dw_stats /home/mongodb/mongo_export/dw_201711301700
mongorestore -h 172.30.7.58:22001 -d hshgk /opt/mongodb/data_rec/formal
```

公司data环境还原的命令：

```
mongorestore -h localhost:10001 -u root -p Abc123\!\@Gwssi -d jck_dataquery_dev /home/dbDataBak/mongoBak/20200921-zwp/jck_metadata_dev --gzip
```

