---
tags: [postgresql]
date: 2019-02-16 20:31:00
---

整理自 https://blog.csdn.net/elesos/article/details/80059841

### 需求

装的新系统，要把之前备份的数据迁移过来

### 步骤

之前旧的目录是 `/run/media/dccmmtop/manjaro_backup/var/lib/postgres/data`

新的数据库目录是 `/var/lib/postgres/data`

1. 停止数据库服务

```shell
systemctl stop postgresql
```

2. 删除新的 data

```shell
su root
cd /var/lib/postgres
rm -r ./data
```

3. 迁移数据

```shell
cd /var/lib/postgres
cp -r /run/media/dccmmtop/manjaro_backup/var/lib/postgres/data ./
sudo chown -R postgres:postgres data
sudo chmod 700 datasudo chown -R postgres:postgres data
sudo chmod 700 data
```

4. 启动数据库服务

```shell
systemctl start postgresql
```
