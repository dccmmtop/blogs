## 配置翻译文本
在`config/locales`文件夹下，新建翻译文本`zh-CN.yml`,这里的文件名根据自己的需要随便命名，最好见名知意
文件内容参考如下
```yml
zh-CN:
  menu:
    home: '首页'
    team: '团队'
    projects: '项目'
    careers: '招聘'
    blog: '博客'
```
与之对应新建翻译文本`en.yml`
文件内容参考如下
```yml
en:
  menu:
    home: 'About'
    team: 'Team'
    projects: 'Projects'
    careers: 'Careers'
    blog: 'Blog'
```
## 在console中测试
#### 查看可用的语言
`I18n.available_locales`
#### 设置当前语言
`I18n.locale = 'en'`
#### 测试翻译
`I18n.t('menu.home')`
## 在view中使用
```erb
<%= t("menu.home")%>
```
## 设置网站的语言环境
#### view
在helper中编写生成语言选择的下拉列表框的辅助方法，方便在view中使用，尽量不要把ruby语言部分过多的写在html中
```ruby
  def language(language_category)
    case language_category
    when :'en'
      'English'
    when :'zh-CN'
      '简体中文'
    end
  end

  def language_tag
    request_url = request.url
    I18n.available_locales.map do |la|
      content_tag(:li,content_tag(:a ,language(la),href:"#{request_url}?&locale=#{la.to_s}",data: {remote: true},class:'language'))
      # <li><a class='language' href= "xxxx?&local=en" data-remote='true'>English</a></li>
    end.join.html_safe
  end
```
注意`content_tag`的用法
当选择语言时，需要异步提交到后台设置语言环境，然后重新加载本页面
#### controller
每个页面都需要检查一下当前的语言环境，所以在每个页面跳转之前都到先设置正确的语言，因此在`ApplicationController`中如下
```ruby
  before_action :set_language

  def set_language
    session[:locale] = params[:locale] if params[:locale]
    I18n.locale = session[:locale] || I18n.default_locale
  end
```
#### 刷新当前页面
由于是异步提交到后台中，所以页面不会刷新。对语言选择的超链接添加`ajax:success`监听，当成功设置语言环境后，使用前端的方法刷新本页面
```javascript
function set_language(){
  $(".language").on('ajax:success', function(event, data, status, xhr) {
    location.reload();
  });
```




