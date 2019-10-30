---
tags: [rails,yield,content_for]
date: 2018-08-12 15:43:39
---

### layout/application.html.erb

```erb
<!DOCTYPE html>
<html>
<head>
  <!-- 默认值 -->
  <title><%= content_for(:title) ? (yield :title) : "MillionBlock" %></title>
  <%= csrf_meta_tags %>
  <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
  <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
</head>

<body>
  <div id="mainContent">
    <%= render 'shared/header' %>
    <div class="container">
      <%=notice_message%>
    </div>
    <%= yield :buy_block %>
    <div class="container">
      <div class="row">
        <%= yield %>
      </div>
    </div>
    <%= render 'shared/footer' %>
  </div>
</body>
</html>
```
**注意如何使 `title` 带有默认值**

### 应用

```erb
<% content_for :title, "MillionBlock is a very interesting website, let's take a look." %>
<div class="share center-block">
  <div class="text">
    <p>If your friend uses your recommended code to buy a grid, your assets will increase.Scan two-dimensional code, share with friends</p>
  </div>
</div>
```
