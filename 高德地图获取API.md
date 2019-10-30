---
tags: [react-native]
date: 2018-11-01 22:13:25
---

在独立开发者，不会与人合作开发的情况下，在开发阶段不需要使用发布版，可以把发布版的 sha1 码填写成调试版的 sha1 码。

(多人合作开发时也可以使用这种方法，只是需要每个人修改成自己机器对应的 key，麻烦)

1.  进入到`~/.android`目录下，执行，`keytool -list -keystore debug.keystore` ，提示输入密码，密码是 `android`
2.  显示的结果 sha1 粘贴到高德对应的表单中
3.  填写包名
4.  把`<meta-data android:name="com.amap.api.v2.apikey" android:value="你的key" />` 复制到 `AndroidManifest.xml`文件下的 `application`内，
5.  给应用开启定位权限，这里是指 android 系统给 app 开启应用权限，不是说在 `AndroidManifest.xml`文件内添加定位权限，注意是开启，不是允许询问
