---
tags: [gradle]
date: 2018-12-04 00:37
---

> 本文根据[原文](https://www.jianshu.com/p/a03f4f6ae31d)稍加整理

# 介绍

Gradle 是google开发的基于[groovy语言](http://www.groovy-lang.org/) ，用于代替 ant 构建的一种配置型语言

Gradle 是基于groovy语言实现（基于JVM的语法和java类似的脚本语言）的一个Android编译系统， google针对Android编译用groovy语言开发了一套 DSL 语言
有额外需要直接使用groovy，或者java代码解决

# gradle wrapper

每个基于gradle构建的工程都有一个gradle**本地**代理，叫做 gradle wrapper
在 `/gradle/wrapper/gralde-wrapper.properties` 目录中声明了指向目录和版本

官方的各个版本的代理下载地址 <https://services.gradle.org/distributions/>
如果 gradle 初次构建缓慢，可以手动下载代理放到 \$HOME/.gradle/wrapper/dists

### 注意设置代理的方式与位置

在用户的 `.gradle`目录下建立 `gradle.properties` 文件作为全局设置，参数有

```config
# 开启并行编译
org.gradle.parallel=true
# 开启守护进程
org.gradle.daemon=true
# 按需编译
org.gradle.configureondemand=true
# 设置编译jvm参数
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8

# 设置代理--选择你对应的代理，笔者使用的是 socks5

systemProp.socks.proxyHost=127.0.0.1
systemProp.socks.proxyPort=1080

# systemProp.http.proxyHost=127.0.0.1
# systemProp.http.proxyPort=10384
# systemProp.https.proxyHost=127.0.0.1
# systemProp.https.proxyPort=10384
# 开启JNI编译支持过时API
android.useDeprecatedNdk=true
```

安装一个全局的gradle，并配置好Path变量，避免每个项目重复下载，这样后面编译项目就可以直接运行gradle build

# 常用命令

> 注意:在window下可以直接运行 `gradlew` 如果是Linux 或者 mac 命令为 `gradle gradlew` 这里都简写成 `./gradlew`

## gradle 任务查询命令

- 所有后面的命令，都必须在 tasks --all 可见，不然报告找不到这个任务

```
# 查看任务
./gradlew tasks
# 查看所有任务 包括缓存任务等
./gradlew tasks --all
# 对某个module [moduleName] 的某个任务[TaskName] 运行
./gradlew :moduleName:taskName
```

> 说明，module 定义在 工程根 `settings.gradle` 下，由 `include 指定`
> 子模块任务，不代表工程根也有同样的任务，所以需要单独查询
>
> > moduel 最佳命名实践为 `全小写英文` 防止编译兼容问题

## 快速构建命令

```
# 查看构建版本
./gradlew -v
# 清除build文件夹
./gradlew clean
# 检查依赖并编译打包
./gradlew build
# 编译并安装debug包
./gradlew installDebug
# 编译并打印日志
./gradlew build --info
# 译并输出性能报告，性能报告一般在 构建工程根目录 build/reports/profile
./gradlew build --profile
# 调试模式构建并打印堆栈日志
./gradlew build --info --debug --stacktrace
# 强制更新最新依赖，清除构建并构建
./gradlew clean build --refresh-dependencies
```

注意`build`命令把 `debug、release`环境的包都打出来的
如果需要指定构建使用如下命令

### gradle 指定构建目标命令

```
# 编译并打Debug包
./gradlew assembleDebug
# 这个是简写 assembleDebug
./gradlew aD
# 编译并打Release的包
./gradlew assembleRelease
# 这个是简写 assembleRelease
./gradlew aR
```

### gradle 更新最新依赖问题

这个是困扰不少开发者的问题，其实研究一下就知道

- gradle 相对 maven 做了一层本地缓存 `${user}/.gradle/caches/modules-2`（默认缓存更新是 24小时）
- gradle 在当前工程也做了一层缓存 `${project.root}/.gradle`
- 使用 IDE 这种集成开发环境，也加了一层缓存(在 IDE 的缓存目录里面)
- 工程开发配置文件（当前工程下 .idea .vsc 等等），这个会影响到代码提示

所以，经常出现 gradle 命令更新到最新依赖代码，IDE 不显示的问题，你需要自行处理好缓存
一般命令行 加入 `--refresh-dependencies` 可以更新 gradle 部分，但不会影响到 IDE
如果想要 IDE 在写代码时知道更新，你需要刷新或者修改 IDE 的缓存，具体怎么操作需要根据情况自行解决
这里提供2个工具脚本辅助

- [脚本工具 当前目录 IDEA 类工程清理工具](https://raw.githubusercontent.com/sinlov/maintain-python/master/ide/jetbrain/idea_project_fix.py)
- [脚本工具 gradle 本地缓存 SNAPSHOT 清理工具](https://raw.githubusercontent.com/sinlov/maintain-python/master/language/gradle/clean_gradle_snapshot.py)

> 脚本工具由 python2 编写，怎么做到全局使用，请配置在环境变量中，需要额外功能，请自行修改脚本，本人不提供免费的技术服务

## gradle 构建并安装调试命令

```
# 编译并打Debug包
./gradlew assembleDebug
# 编译app module 并打Debug包
./gradlew install app:assembleDebug
# 编译并打Release的包
./gradlew assembleRelease
#  Release模式打包并安装
./gradlew installRelease
# 卸载Release模式包
./gradlew uninstallRelease
```

### gradle 多渠道打包

> assemble还可以和productFlavors结合使用，如果出现类似 `Task 'install' is ambiguous in root project` 这种错误，请查看配置的多个渠道然后修改命令为
> `./gradlew install[productFlavorsName] app:assembleDebug`
> 来用命令构建调试

```
# Release模式打包并安装
./gradlew installRelease
# 卸载Release模式包
./gradlew uninstallRelease
# Release模式全部渠道打包
./gradlew assembleRelease
# Release模式 test 渠道打包
./gradlew assembleTestRelease
# debug release模式全部渠道打包
./gradlew assemble
```

## gradle 查看包依赖

```
./gradlew dependencies
# 或者模组的 依赖
./gradlew app:dependencies
# 检索依赖库
./gradlew app:dependencies | grep CompileClasspath
# windows 没有 grep 命令
./gradlew app:dependencies | findstr "CompileClasspath"

# 将检索到的依赖分组找到 比如 multiDebugCompileClasspath 就是 multi 渠道分发的开发编译依赖
./gradlew app:dependencies --configuration multiDebugCompileClasspath
# 一般编译时的依赖库，不是固定配置方式，建议检索后尝试
./gradlew app:dependencies --configuration compile
# 一般运行时的依赖库，不是固定配置方式，建议检索后尝试
./gradlew app:dependencies --configuration runtime
```

## gradle 依赖管理

- 传递依赖特性

```
dependencies {
    transitive true
}
```

> 手动配置transitive属性为false，阻止依赖的下载

- 强制指定编译版本

```
configurations.all{
  // transitive false
  // 强制指定版本
  resolutionStrategy{
    force 'org.hamcrest:hamcrest-core:1.3'
  // 强制不编译
  all*.excludegroup: 'org.hamcrest', module:'hamcrest-core'
  }
}
```

- 动态依赖特性

```
dependencies {
    // 任意一个版本
    compile group:'b',name:'b',version:'1.+'
    // 最新的版本
    compile group:'a',name:'a',version:'latest.integration'
}
```

查看详细依赖信息

## 使用离线模式

```
./gradlew aDR --offline
```

## 守护进程

```
./gradle build --daemon
```

## 并行编译模式

```
./gradle build --parallel --parallel-threads=N
```

## 按需编译模式

```
./gradle build --configure-on-demand
```

### 不使用snapshot依赖仓库

前提是离线可以使用时

```
./gradlew clean aDR
```

## 设定编码

```
allprojects {
...
    tasks.withType(JavaCompile){
        options.encoding = "UTF-8"
    }
...
}
```

# 仓库设置

## 设置中心仓库

默认是jcenter、可以是mavenCentral

```
repositories {
    // gralde 4.0 以后出现，访问仓库为 https://dl.google.com/dl/android/maven2/
    google()
    // 私有，或者国内镜像仓库配置方法
    maven { url "http://maven.oschina.net/content/groups/public" }
    // maven centeral 由Sonatype公司提供的服务，它是ApacheMaven、SBT和其他构建系统的默认仓库
    mavenCentral()
    // jcenter 由JFrog公司提供的Bintray中的Java仓库,是GoovyGrape内的默认仓库，Gradle内建支持
    jcenter()
    // mavenCentral 和 jcenter 搜索库 http://mvnrepository.com/

}
```

> repositories 仓库的写作顺序，会影响到拉取的速度，因为 groovy 脚本执行时，是按数组下标进行的

# Android Studio 提速

## 禁用插件

去掉一些没有用的插件，这个不是固定的，如果你能解决网络问题，开启这些插件对你写代码有好处
Google Cloud Testing、Google Cloud Tools For Android Studio、Goole Login、Google Services、JavaFX、SDK Updater、TestNG-J

## android studio 2.2.2新特性 编译缓存

工程根目录 `gradle.properties` 文件里加上

```
android.enableBuildCache=true
```

这个设置可以让Android Studio 会把依赖的 jar 或 arr 缓存到本地，并且把模块名称设置为 hash 值

> 这个开启后，可能导致 includeJarFilter 配置失效，Android Studio 升级到 2.3.0修复这个问题

每次编译生成的缓存在 `$HOME/.android/build-cache`
如果缓存过多可以手动删除该目录进行清除

## 升级到 Android Studio 2.3 后编译不兼容问题

升级到 Android Studio 2.3 后，Gradle Plugin 也升级到 2.3.0

- 对应推荐使用的 Gradle 版本是 3.3

这时候会发现工程模块目录下 `{module name}/build/intermediates/exploded-aar/`
目录没了

它会在 `$HOME/.android/build-cache` 下生成一部分缓存文件，来代替 `exploded-aar`
如果需要生成`exploded-aar`，可以配置项目目录下的 `gradle.properties` ，添加一行内容

```
android.enableBuildCache=false
```

然后重新构建项目即可在 `{module name}/build/intermediates/`看到 explod
