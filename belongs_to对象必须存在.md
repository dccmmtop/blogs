---
tags: [rails,belongs_to]
date: 2018-08-17 14:40:07
---

在 Rails 5 中，每当我们定义 belongs_to 关联时，都需要在此更改后默认存在关联记录。
如果不存在关联记录，则会触发验证错误。

![](http://ogbkru1bq.bkt.clouddn.com/选区_135.png)

我们可以看到，如果没有关联的用户记录，我们就无法创建任何帖子记录。

### 如何在 Rails 5 之前实现此行为

在 Rails 4.x 世界中要在 belongs_to 关联上添加验证，我们需要添加 `require:true`。

![](http://ogbkru1bq.bkt.clouddn.com/选区_136.png)

默认情况下，这个选项是 false

### 在 Rails 5 中选择退出此默认行为

我们可以设置 `optional:true` 来跳过 `belongs_to`的验证

![](http://ogbkru1bq.bkt.clouddn.com/选区_137.png)

### rails API

![](http://ogbkru1bq.bkt.clouddn.com/选区_140.png)

但是，如果我们在整个应用程序中的任何地方都不需要这种行为而不仅仅是单个模型呢？

### 全局跳过 belongs_to 存在验证

![](http://ogbkru1bq.bkt.clouddn.com/选区_138.png)
