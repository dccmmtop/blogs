## ssh免密码登录
* 生成key
  在本地执行`ssh-keygen`
* 将本地的公钥拷贝到远程服务器
  ```shell
  ssh-copy-id -i ~/.ssh/id-rsa.pub -p 22222 username@ip
  ```

