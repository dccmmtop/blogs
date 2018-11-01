---
tags: ssh
date: 2018-05-29 11:24:54
---

- 生成 key
  在本地执行`ssh-keygen`
- 将本地的公钥拷贝到远程服务器
  ```shell
  ssh-copy-id -i ~/.ssh/id_rsa.pub -p 22222 username@ip
  ```
