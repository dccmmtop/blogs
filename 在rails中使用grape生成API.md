---
tags: rails,api
date: 2018-08-08 11:29:47
---

### 添加 gem

```ruby
gem "grape"
```

### 添加 api 文件目录

手动新建 api 目录，如图:
![http://ogbkru1bq.bkt.clouddn.com/选区_114.png]

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

### 测试

```shell
curl -X GET -H 'Accedd:xxxxx' http://xxxxx/api/topcis/ping
```
