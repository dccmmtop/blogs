---
tags: [rails,消息闪现]
date: 2018-08-23 12:12:47
---

rails 的消息闪现可结合`redirect_to`使用，但是，这样就会刷新页面，不适合异步请求操作。并且若页面有表单，刷新页面后，未完成的表单数据会消失，对用户不友好。可用 js 自己实现一个消息闪现,如下:

样式由 bootstrap 提供

### js

```coffee
$(document).on("turbolinks:load", ->
  listen_bank_form()
)

listen_bank_form = () ->
  $(".bank-form").on('ajax:success',(data) ->
    result = data.detail[0].result
    info = data.detail[0].message
    if(result == 0)
      # 请求失败时，弹出警告
      flash_alert(info)
    else
      flash_success(info)
  ).on("ajax:error",(data) ->
    flash_alert("unknown error")
  )

flash_alert = (info) ->
  alert = '<div class="alert alert-danger"><a class="close" data-dismiss="alert" href="#"><button type="button" class="close" data-dismiss="alert" aria-hidden="true">×</button></a>'+info+'</div>'
  $(".flash").append(alert)

flash_success = (info) ->
  alert = '<div class="alert alert-success"><a class="close" data-dismiss="alert" href="#"><button type="button" class="close" data-dismiss="alert" aria-hidden="true">×</button></a>'+info+ '</div>'
  $(".flash").append(alert)
```

### erb

```erb
<div class="flash"></div>
```

```css
.flash {
  padding: 10px;
}
```

---

### js helper

```coffee
# 警告信息显示, to 显示在那个dom前(可以用 css selector)
alert : (msg,to) ->
  $(".alert").remove()
  $(to).before("<div class='alert alert-warning'><a class='close' href='#' data-dismiss='alert'><i class='fa fa-close'></i></a>#{msg}</div>")

# 成功信息显示, to 显示在那个dom前(可以用 css selector)
notice : (msg,to) ->
  $(".alert").remove()
  $(to).before("<div class='alert alert-success'><a class='close' data-dismiss='alert' href='#'><i class='fa fa-close'></i></a>#{msg}</div>")
```
