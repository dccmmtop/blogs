1. 查看手机已经安装的应用
`adb shell pm list packages`

2. 获得安装包的路径
假如安装包额名称是 'com.reactnativemixedland'
`adb shell pm path com.reactnativemixedland`

3. 将安装包文件导出到计算机
` adb pull apk_path`
