---
tags: rails,自关联
date: 2018-08-15 12:22:06
---

关于 Rails 的模型自关联有一个非常有意思的题目，大概是这样的：

```ruby
lisa = Person.create(name:'Lisa')
tom = Person.create(name:'Tom',parent_id:lisa.id)
andy = Person.create(name:'Andy',parent_id:lisa.id)
tom.parent.name => 'Lisa'
lisa.children.map(&:name) => ['Tom','Andy']

thomas = Person.create(name: 'Thomas',parent_id: tom.id)
peter = Person.create(name:'Peter',parent_id:tom.id)
gavin = Person.create(name:'Gavin', parent_id: andy.id)
lisa.grandchildren.map(&:name) => ['Thomas','Peter','Gavin']
```

问如何定义 Person 模型来满足以上需求？

题目考察了对模型自关联的理解，通过审题我们可以得出以下几点：

- Person 对象的 Parent 同样是 Person 对象（自关联）
- Person 对象对 Parent 是多对一关系
- Person 对象对 Children 是一对多关系
- Person 对象通过 Children 与 GrandChildren 建立了一对多关系
  在不考虑 GrandChildren 时，不难得出模型定义如下：

```ruby
class Person < ActiveRecord::Base
  belongs_to :parent, class_name: 'Person', foreign_key: 'parent_id'
  has_many :children, class_name: 'Person', foreign_key: 'parent_id'
end
```

其中 Person 包含两个自关联关系：

第一个就是非常常见的从子到父的关系，在 Person 对象创建时指定 parent_id 来指向父对象；
第二个关系用来指定 Person 对象对应的所有子对象
接下来更近一步，我们要找到 Person 对象子对象的子对象，换句话说：孙子对象。
如我们上面的分析，Person 对象通过 Children 与 GrandChildren 建立了一对多关系，其代码表现为：

`has_many :grandchildren, :through => :children, :source => :children`
:source 选项的官方文档说明如下：

> The :source option specifies the source association name for a has_many :through association. You only need to use this option if the name of the source association cannot be automatically inferred from the association name. —— rails guide

在这里我们通过:source 选项告诉 Rails 在 children 对象上查找 children 关联关系。
于是该题目完整的模型定义如下：

```ruby
class Person < ActiveRecord::Base
belongs_to :parent, class_name: 'Person', foreign_key: 'parent_id'
has_many :children, class_name: 'Person', foreign_key: 'parent_id'
has_many :grandchildren, :through => :children, :source => :children
end
```

> 作者：李小西 033
> 链接：https://www.jianshu.com/p/076b5fec4dad
> 來源：简书
> 简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。
