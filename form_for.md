---
tags: form_for,rails
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
