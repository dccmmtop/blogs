## rails部署之后图片404问题
当在html.erb中添加一张图片时，可以用`<%= image_tag%>`
辅助函数，但是如果我们要在scss文件中给某个divt添加北京图片时，该怎样设置图片的url呢？
```scss
  .div#name{
    background-image:url(image-path("1.png"));
  }
```
需要使用`image-path`辅助函数，如果直接写图片名称，再开发环境下没有问题，但是部署到线上就会404
还有另外一种方式
```erb
 <img  class="img-circle" src="<%=asset_path('user.png')%>" width="100px;"
 height='100px' style="margin: 20px;">
```

