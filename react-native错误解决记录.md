---
tags: [react-native,error-records]
date: 2018-11-03 16:10:19
---

### unable to load script from assets 'index.android.bundle

1.  mkdir android/app/src/main/assets/
2.  rm -r node_modules
3.  yarn install
4.  react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
5.  react-native run-android

### Error type 3; Error: Activity class {xxxxxx.MainActivity} does not exist.

`adb uninstall <package name>`

ex: adb uninsall com.wall
