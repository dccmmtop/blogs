---
tags: rails
date: 2018-11-21 15:35:41
---

### 什么是多态关联

假如有三个模型，分别是 用户， 产品， 图片。图片为用户所有，也为产品所有。我们可以创建两个 picture 的模型，如下

`rails g modle picture_user user_id:integer name:string url:string`

`rails g modle picture_product product_id:integer name:string url:string`

```ruby
class PictureUser < ApplicationRecord
  belongs_to :user
end

class PictureProduct < ApplicationRecord
  belongs_to :product
end
```

这样我们就可以使用`user.prictures` 和 `product.pictures`来分别获得用户下和产品下的图片了。但是我们发现，两个图片模型除了外键不一样，其他字段都是一样的，那么有没有一种办法只创建一个 picture 模型，同时属于 user 和 product 呢，这种既属于一个模型又属于另外一个模型（可以是很多个）的关联就是多态关联。

### 多态关联的实现

为了能同时使用 `user.pictures` 和 `product.pictures` 来获得各自的图片，我们就需要对 picture 模型做一些修改，使其能够标识一张图片是属于 user 还是属于 product ，当然外键是必不可少的。我们还需要一个外键对应类的名称，如下：

`rails g modle picture pictureable_id:integer pictureable_type:string name:string url:string`

```ruby
class Picture < ApplicationRecord
  belongs_to :pictureable, polymorphic: true
end

class User < ApplicationRecord
  has_many :pictures, as: :pictureable
end

class Product < ApplicationRecord
  has_many :pictures, as: :pictureable
end
```

pictureable 相当于一个接口，凡是拥有图片的模型都可以像 User 那样使用关联。

可以使用`user.pictures.create(name: 'user_0', url: 'https://dcc.com')`来创建一条关联对象，创建之后我们发现在 picture 表中多了一条记录：

```txt
id: 1, pictureable_type: 'User', pictureable_id: 1, name:"user_0", url:'https://dcc.com'
```

pictureable_type: 'User' 就是所属对象的标识，这样才可以使用 user.pictures 进行查询。由此我们知道，多态关联中，`xxxable_type, xxxable_id`字段是必不可少的。

\-------------------------------------------------------------------------------------

下面是关于多态 view 页面使用的讲解[原文](https://blog.csdn.net/qwbtc/article/details/52035603)

### 什么是多态

Rails 模型中的关系有一对一，一对多还有多对多，这些关联关系都比较直观，除此之外 Rails 还支持多态关联，所谓的多态关联其实可以概括为一个模型同时与多个其它模型之间发生一对多的关联。并且在实际的应用中这种关系也十分普遍，比如可以应用到站内消息模块，评论模块，标签模块等地方，下图就是多态关系下的评论模块的 E-R 图。

通过 E-R 图，我们能直观的看到系统中的事件，文章以及照片都可以被用户评论，并且这些评论都被存储在一张叫 comments 表中。Ok，现在我们已经搞清楚了多态的含义，下面继续看下 Rails 中是如何实现多态关联的。

### Rails 中实现多态的步骤

这里我们通过将 Rails Guides 中给出的例子线性化(转化为详细步骤)来说明这个问题。

#### Step 1： 通过 Migration 创建表

执行下面命令来生成 Migration 文件

```css
rails g model picture name:string imageable_id:integer  imageable_type:string
```

生成的 Migration 文件如下:

```ruby
class CreatePictures < ActiveRecord::Migration
  def change
    create_table :pictures do |t|
      t.string  :name
      t.integer :imageable_id
      t.string  :imageable_type
      t.timestamps null: false
    end

    add_index :pictures, :imageable_id
  end
end
```

其中需要特别关注， `imageable_id` 与 `imageable_type` 两个字段，前者用来存储相关联内容的外键键值，后者则用来存储相关联内容的类型名。后面在通过模型查找关联内容的时候，可以通过这两个值来定位到要找到的内容。特别是后者 `imageable_type` 的存在是多态实现的关键。

#### Step 2: 修改各 Model 得关联关系

![](https://i.loli.net/2018/11/21/5bf5282bce812.png?filename=/home/mc/图片/1542793224.png)

按照上面 E-R 图和代码修改模型结构，因为 Employee，Product 分别与 Picture 是一对多的关系，所以用到了 has_many 与 belongs_to 方法，再使用 polymorphic 与 as 来指明是多态关联。

#### Step 3: Controller 中应用

上面的两步完成后就能在 Controller 中通过多态关联关系进行相互访问了，并且通过关联关系创建的新评论 Rails 也会自动帮你设置 commentable_id 与 commentable_type 两个字段的值。

```
event = Event.create name: "event1"
event1= event.comments.create content: “comment1”
event1.commentable_type #=> “Event”
```

Done！到此就算完整应用到了多态关联关系，后续需要处理的就是如何来组织代码让多态关系更加灵活便捷的被你操作，不过这个就应该是另一篇文章的内容了。:)

刚开始看 Rails Guide 的时候对多态的表关联真的是一头雾水。后来自己写了一个博客应用的时候用到了 acts_as_commentable 这个 gem，它就是用到了多态表的关联，然后我又看了 Terry 在 railscasts china 上的 [视频](http://railscasts-china.com/episodes/file-uploading-by-carrierwave?autoplay=true) ，对多态的理解就深了很多。

#### 理解什么是多态

一般表的关联有一对一，一对多，多对多，这些都是非常好理解的，然后对于多态的表关联可能稍微有点不好理解。其实多态关键就是一个表关联到多个表上。就如 Comment（评论）表吧，一个 Topic 应该有 Comment（一个帖子应该有许多的评论），除此之外 Micropost（微博）也可能有很多的 Comment。然后一个网站中既有 Topic 的论坛功能，又有 Micropost 的功能，我们怎么处理 Comment 表呢？当然我们可以建两个独立的表比如 TopicComment 和 MicropostComment，再分别关联到 Topic 和 Micropost 上，但这不是一种好的选择，我们可以只建一个表，然后去关联这两个表，甚至多个表。这也就实现了多态的能力。

#### 一个例子

1.首先我们先生成一个 Comment 的 model，假设已经有 Topic 和 Micropost 这两个 model 了

```css
rails g model comment content:text commentable_id:integer comment_type:string
```

2.然后我们 会得到一个 migration

```ruby
class CreateComments < ActiveRecord::Migration
  def change
    create_table :comments do |t|
      t.text :content
      t.integer :commentable_id
      t.string  :commentable_type
      t.timestamps
    end
  end
end
```

也可以通过 t.references 来简化上面的

```ruby
class CreateComments < ActiveRecord::Migration
  def change
    create_table :comments do |t|
      t.text :content
      t.references :commentable, :polymorphic => true #这里指明了多态，这样会生成comment_id和comment_type这两个字段的，如上
      t.timestamps
    end
  end
end
```

多态魔法就在这里，commentable_typle 字段用于指明 comment 所关联的表的类型，如 topic 或 micropost 等，而 comment_id 用于指定那个关联表的类型对象的 id。如：可以把一个 comment 关联到第一篇 topic 上，那么 comment_type 字段为 topic，而 comment_id 为对应 topic 对象的 id 1,同理这样就可以关联到不同表了，从而实现多态的关联。

3,数据迁移 `rake db:migrate` 就能生成我们要的表了

4,对 model 进行操作从而现实表的关联

```ruby
####comment model
class Comment < ActiveRecord::Base
  belongs_to :commentable, :polymorphic => true
end
```

看到没有，这里的 comment belongs_to 没有写 topic，micropost 等，而写了 commentable,因为 commentable 中有 type 和 id 两个字段，可以指定任何其他 model 对象的，从而才能实现多态，如果这里写 belongs_to topic 的话就没办法实现多态了。然后我们看看 topic 和 mocropost 的 model 该如何写。

```ruby
class Topic < ActiveRecord::Base
  has_many :comments, :as => :commentable
end
class Micropost < ActiveRecord::Base
  has_many :comments, :as => :commentable
end
```

看到这里的 as 了吗？as 在这我们可以解释为：作为（我的理解，可能这种理解补科学，哈哈），也就是说 Topic 有许多的 comments，但是它是通过将自己作为 commentable，实现的。Micropost 同理。

然后就是 controller 和 views 中（如 form 表单）的设计了，这也是我刚学的时候，最头疼这个了，因为对 params 参数通过表单到 controller 的传递没掌握好。

在写这些之前，我们先看看如何写路由吧，因为一个 topic 有多个 comments，Micropost 同理。所以我们可以这样写

```ruby
resources :topics do
  resources :comments
end

resources :microposts do
  resources :comments
end
```

然后我们通过命令 `rake routes` 就可以得到相应的路由了如：

```ruby
          topic_comments GET    /topics/:topic_id/comments(.:format)          comments#index
                                    POST   /topics/:topic_id/comments(.:format)          comments#create
       new_topic_comment GET    /topics/:topic_id/comments/new(.:format)      comments#new
      edit_topic_comment GET    /topics/:topic_id/comments/:id/edit(.:format) comments#edit
           topic_comment GET    /topics/:topic_id/comments/:id(.:format)      comments#show
                                    PUT    /topics/:topic_id/comments/:id(.:format)      comments#update
                                   DELETE /topics/:topic_id/comments/:id(.:format)      comments#destroy
```

这些待会我们会用到。

然后我们再来分析 controller 和 views 之间的参数传递。我们通过完整的创建 comment 的过程进行说明

(1)首先页面上肯定有一个创建 comment 的连接或按钮（假设创建 comment 的表单和 topic show 页面不在统一页面上），代码应该是这样的：

```
<%= link_to "发表评论", new_topic_comment_path%>
```

(2)点击这个链接后，通过路由来到 controller 中的 new 方法(同时会将对应的 topic 相关的参数传给 controller)

```ruby
def new
  @topic = Topic.find(parmas[:id]) #找到comment属于的topic
  @comment = @topic.comments.build #建立这个关系
end
```

(3)经过这个方法（action）后，页面来到了 comments/new.html.erb,在这个页面中有一个评论的表单，大概是这样的

```
 <%= form_for([@comment.commentable, @comment]) do |f| %>
  ......
<%end%>
```

这个表的参数是一个数组，[email protected]

@comment，如果没有关联的化，[email protected]，

[email protected]

��，还有一个就是 commentable，这里也就是 topic。

还记得 new 中的 `@comment = @topic.comments.build` 的吗，这里就暂时将对应的 topic 对象写入 commentable（注意：只是暂时建立关系，还没有写入数据库），[email protected]

@topic。

(4)然后你填完表单后，按提交按钮后，表单中的参数（包括 commentable，@post 的 id 等信息），一起来到 controller 的 create 方法中

```ruby
def create
  Topic.find(parmas[:topic_id]).comments.create(parmas[:comment])
  ......
end
```

这样就真正创建了一个新的 comment。micropost 同理。

其实多态讲的也差不多了，但在提一个地方

**重要知识点:**假设一个 comment 已经建立了，它的 commentable_type 是:topic.comment_id 是 1。如果我们得到了这个 id 为 1 的 topic，@topic，那么我们怎么得到它的 comments 呢？是的很简单，直接 `@topic.comments` 就 ok 了。但是反过来呢，我们得到了这个 comment，@comment，我们如何得到对应的 topic 的信息呢？我以前刚学的时候，就用了`@comment.topic` ，呵呵，没错，得到的是一串错误，正确一概是 `@comment.commentable`

关于多态我们已经讲的差不多了。

补充：上面的例子 comment 的表单是独立在 comments/new.html.erb 中的，但是一般的应用 comment 的表单是在 topics/show.html.erb 中，也就是上面一个 topic，topic 下有一个 comment 表单。这样的话在 controller 中我们就不需要 new 这个方法了，那么我们在哪建立关系呢？

```ruby
@comment = @topic.comments.build #建立这个关系
```

我们就在表单的 `<%= form_for ...%>` 前面写 `<@comment = @topic.comments.build>`
