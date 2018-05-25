## linux添加新用户
1. adduser username
2. 输入密码
3. 再次输密码
4. 输入电话号等，按enter使用默认值
5. 新用户创建成功
**这时新用户没有权限使用sudo**
打开`/etc/sudoers`文件，找到`root    ALL=(ALL) ALL`
在下方添加一行
`username  ALL=(ALL) ALL`
强制保存
done！
