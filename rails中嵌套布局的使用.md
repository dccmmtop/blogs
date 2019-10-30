---
tags: [rails]
date: 2018-10-18 08:44:49
---

### 问题

![](http://ogbkru1bq.bkt.clouddn.com/1539823637.png)

如上图，绿色是 application 布局,红色 aside 布局，蓝色是内容，如何实现？

### 方法

layouts/application 布局如下

```erb
<html>
<head>
  <title><%= @page_title or "Page Title" %></title>
  <%= stylesheet_link_tag "layout" %>
  <style><%= yield :stylesheets %></style>
</head>
<body>
  <div id="top_menu">Top menu items here</div>
  <div id="menu">Menu items here</div>
  <div id="content"><%= content_for?(:content) ? yield(:content) : yield %></div>
</body>
</html>
```

layouts/aside 布局如下

```erb
<% content_for :stylesheets do %>
  #top_menu {display: none}
  #right_menu {float: right; background-color: yellow; color: black}
<% end %>
<% content_for :content do %>
  <%= yield %>
<% end %>
<%= render template: "layouts/application" %>
```

蓝色内容：

```erb
<div>
.....
</div>
```

在控制器中：

```ruby
layout 'aside'
```

### 注意

- 布局的嵌套层级没有限制
- **要使用具名 yield**
- 在 aside 布局中使用`<%= render template: "layouts/application" %`指定父布局
