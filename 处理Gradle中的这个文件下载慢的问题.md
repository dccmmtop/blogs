---
tags: [android]
---

使用阿里云的国内镜像仓库地址，就可以快速的下载需要的文件修改项目根目录下的文件

```text
build.gradle ：buildscript {
    repositories {
        maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    }
}

allprojects {
    repositories {
        maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    }
}
```

然后选择重新构建
