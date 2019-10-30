---
tags: [postgresql,备份,恢复]
date: 2018-12-11 13:47:40
---

**一.导出数据库及具体表**

1.导出数据库：方式一：pg_dump -U postgres -f c:\db.sql postgis

​ 方式二：pg_dump -U postgres postgis > c:\db.sql

2.导入数据库：方式一：psql -d postgis -f c:\db.sql postgres

3.导出具体表：方式一：pg_dump -Upostgres -t mytable -f dump.sql postgres

4.导入具体表：方式一：psql -d postgis -f c:\ dump.sql postgres

**参数：**

​ postgres：用户

​ postgis：数据库名称

​ mytable：表名称

​ -f, --file=文件名： 输出文件名

​ -U, --username=名字：以指定的数据库用户联接

**二.导出数据格式详解**

用法:
pg_dump [选项]... [数据库名字]

一般选项:
-f, --file=文件名 输出文件名
-F, --format=c|t|p 输出文件格式 (定制,tar, 明文)
-v, --verbose 详细模式
-Z, --compress=0-9 被压缩格式的压缩级别
--lock-wait-timeout=TIMEOUT 在等待表锁超时后操作失败
--help 显示此帮助信息,然后退出
--versoin 输出版本信息,然后退出

控制输出内容选项:
-a, --data-only 只转储数据,不包括模式
-b, --blobs 在转储中包括大对象
-c, --clean 在重新创建之前，先清除（删除）数据库对象
-C, --create 在转储中包括命令,以便创建数据库
-E, --encoding=ENCODING 转储以 ENCODING 形式编码的数据
-n, --schema=SCHEMA 只转储指定名称的模式
-N,--exclude-schema=SCHEMA 不转储已命名的模式
-o, --oids 在转储中包括 OID
-O, --no-owner 在明文格式中,忽略恢复对象所属者

-s, --schema-only 只转储模式,不包括数据
-S, --superuser=NAME 在转储中, 指定的超级用户名
-t, --table=TABLE 只转储指定名称的表
-T, --exclude-table=TABLE 只转储指定名称的表
-x, --no-privileges 不要转储权限 (grant/revoke)
--binary-upgrade 只能由升级工具使用
--inserts 以 INSERT 命令，而不是 COPY 命令的形式转储数据
--column-inserts 以带有列名的 INSERT 命令形式转储数据
--disable-dollar-quoting 取消美元 (符号)引号, 使用 SQL 标准引号
--disable-triggers 在只恢复数据的过程中禁用触发器
--no-tablespaces 不转储表空间分配信息
--role=ROLENAME 在转储前运行 SETROLE
--use-set-session-authorization
​ 使用 SESSION AUTHORIZATION 命令代替
​ ALTER OWNER 命令来设置所有权

联接选项:
-h, --host=主机名 数据库服务器的主机名或套接字目录
-p, --port=端口号 数据库服务器的端口号
-U, --username=名字 以指定的数据库用户联接
-w, --no-password 永远不提示输入口令

-W, --password 强制口令提示 (自动)
