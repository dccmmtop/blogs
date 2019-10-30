---
tags: [rails,api,grape]
date: 2018-08-08 11:29:47
---

### 添加 gem

```ruby
gem "grape"
gem 'grape-swagger'
# 生成 UI
gem 'grape-swagger-rails'
```

### 添加 api 文件目录

手动新建 api 目录，如图:

![](http://ogbkru1bq.bkt.clouddn.com/选区_114.png)

### 加载 api 内文件

在`config/application.rb`内是 api 目录被加载

```ruby
config.paths.add File.join('app', 'api'), glob: File.join('**', '*.rb')
config.autoload_paths += Dir[Rails.root.join('app')]
```

### 设置路由，添加 application_api.rb

设置路由,访问`xxx/api/xxx`时会首先执行`ApplicationApi`内容

```ruby
mount Api::ApplicationApi => '/api'

# api 文档的路由
mount GrapeSwaggerRails::Engine => '/apidoc'
```

`application_api.rb`文件内容如下

```ruby
module Api
  class ApplicationApi < Grape::API
  # before_action
    before do
      error!("error") unless validates
    end

    helpers do
      def validates
      # 身份验证
        request.headers['xxxx'] == ENV['xxxxx']
      end
    end
    # 要挂载的api
    mount Api::TopicsApi => '/topics' #xxxx/api/topics/

    # 配置api文档
    # 首页显示内容
    GrapeSwaggerRails.options.url = '/api/swagger_doc'
    # 动态设置 base_url
    GrapeSwaggerRails.options.before_action do
      GrapeSwaggerRails.options.app_url = request.protocol + request.host_with_port
    end
    # api 列表显示方式
    GrapeSwaggerRails.options.doc_expansion = 'list'
    # 是否隐藏 api_key的输入框
    GrapeSwaggerRails.options.hide_api_key_input = true
    # 生成api 文档
    add_swagger_documentation
  end
end
```

### 添加 api 文件

`TopicsApi` 内容如下

```ruby
module Api
  class TopicsApi < Grape::API
    format :json

    desc "create"
    params do
      # 若上传文件，type:File
      requires :tag, type: String, desc: "tag"
      requires :title, type: String, desc: "title"
      requires :body, type: String, desc: "body"
    end
    # xxxxx/api/topics/create
    post 'create' do
      title = params[:title].to_s
      tag = params[:tag].to_s
      body = params[:body].to_s
      if title && body
        Topic.create(title:title,tag:tag,body:body)
        {status:0, message: "success",data:{}}
      else
        {status:1, message: "title or body should not null"}
      end
    end

    desc "delete"
    params do
      requires :title, type: String, desc: "title"
    end
    delete '/delete' do
      @topic = Topic.find_by_title(params[:title])
      if ! @topic
        return {status:2, message: "not found #{params[:title]}"}
      end
      @topic.delete
      return {status:0}
    end

    get :ping do
      { data: "pong" }
    end

  end
end
```

### api 文档 UI

![](http://ogbkru1bq.bkt.clouddn.com/1535269780.png)

### 测试

```shell
curl -X GET -H 'Access:xxxxx' http://xxxxx/api/topcis/ping
```
