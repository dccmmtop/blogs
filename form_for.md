---
tags: [form-for,rails]
date: 2018-08-23 15:26:20
---

生成一个允许用户 创建 modle 或者更新 model 属性的表单。

### 概览

```erb
<%= form_for :person do |f| %>
  First name: <%= f.text_field :first_name %><br />
  Last name : <%= f.text_field :last_name %><br />
  Biography : <%= f.text_area :biography %><br />
  Admin?    : <%= f.check_box :admin %><br />
  <%= f.submit %>
<% end %>
```

其中`<%= f.text_field :first_name %>` 可以写成 `<%= text_field :person, :first_name %>`.

我们将会得到一个 `input`html 标签，并且 name 属性为 `person[first_name]` ,这意味着，当表单提交时，我们可以在 controller 中通过`params[:person][firt_name]`的方式或者该字段的值

`form_for`的参数是一个`Hash`,下面它可以接收的参数:

- `:url`- 指明表单将要提交的地方。它与`url_for` 和 `link_to`的用法相同,你可以直接使用命名路由,当模型用`string` 或者 `symbol`表示时，（比如上面的方式，`url`没有特别的给出，默认情况下，表单将被发送回当前 URL（我们将在下面描述另一种面向资源的 form_for 用法，其中不需要明确指定 URL

- `:namespace` - 表单的命名空间，以确保表单元素上 id 属性的唯一性。 namespace 属性将在生成的 HTML id 上以下划线为前缀。

- `:method`- 指明表单的提交方式，通常使用`get` 或者 `post` 方式提交，如果使用 “patch”, “put”, “delete“ 等其他的方式提交时，会成生成一个 name 等于`_method`的隐藏`input`标签,来特别指明提交的方式，如果传给`form_for`的是一个存在的实例对象那么默认的提交方式是`patch`，比如（edit）,如果传来的对象不存在，那么默认的提交方式是`post` 比如（new）

- `:authenticity_token`-在表单中使用的真实性令牌，如果你需要自定义，或者根本不需要令牌时，可以通过设置 config.action_view.embed_authenticity_token_in_remote_forms = false 来省略嵌入的真实性令牌。当您对表单进行片段缓存时，这很有用。 远程表单从元标记中获取真实性标记，因此除非您支持没有 JavaScript 的浏览器，否则不需要嵌入。

- `:remote`- 如果设置为`true`,表单将会以 ajax 的方式提交

- `:enforce_utf8`- 如果设置`false`，将不会生成 name 属性为`utf-8`的标签

- `:html` - 设置表单的 html 属性，如`class` `style` ...

### 带模型的表单

在上面的例子中，我们发现 form_for 可以接收符号和字符串，同时还可以接受模型对象本身，例如:

```erb
<%= form_for @post do |f| %>
  ...
<% end %>
```

在 controller 中，我们可以通过`params[:post][...]`来获得参数，我们还可以改变这个参数名字，如：

```erb
<%= form_for(@person, as: :client) do |f| %>
  ...
<% end %>
```

此时就是`params[:client][...]`

另外，表单里面的初始值是根据所给的 modle 中获取的，不管他是不是一个实例变量,如： 有一个本地变量 post

```erb
<%= form_for(post) do |f| %>
  ...
<% end %>
```

### action url 的书写方式

就像上面的例子中，我们没有显示的指出表单应该提交到何处，但是， `form_for`已经自动帮我们设置了`url`,这个默认的设置规则是有 `config/routes.rb`中理由决定的。
有以下几个例子：

-

```erb
<%= form_for @post do |f| %>
  ...
<% end %>
```

等同于

```erb
<%= form_for @post, as: :post, url: post_path(@post), method: :patch, html: { class: "edit_post", id: "edit_post_45" } do |f| %>
  ...
<% end %>
```

- 创建一个新的 modled 对象

```erb
<%= form_for(Post.new) do |f| %>
  ...
<% end %>
```

等同于

```erb
<%= form_for @post, as: :post, url: posts_path, html: { class: "new_post", id: "new_post" } do |f| %>
  ...
<% end %>
```

- 我们也可以指定`url`

```erb
<%= form_for(@post, url: super_posts_path) do |f| %>
  ...
<% end %>
```

- 或者指定返回的格式：

```erb
<%= form_for(@post, format: :json) do |f| %>
  ...
<% end %>
```

- 对命令空间的路由，像 `admin_post_url`:

```erb
<%= form_for([:admin, @post]) do |f| %>
 ...
<% end %>
```

- 如果您的资源已定义关联，则您希望在给定路由设置正确的情况下向文档添加评论:

```erb
<%= form_for([@document, @comment]) do |f| %>
 ...
<% end %>
```
