### 注意请求头的设置
键必须使用字符串的形式，**而不能使用符号**
```ruby
headers = {
"FC-ACCESS-KEY" => "xxxx",
"FC-ACCESS-SIGNATURE" => "xxxx",
"FC-ACCESS-TIMESTAMP" => "xxx",
'Content-Type'=> 'application/json;charset=UTF-8'
}
params = {"name" => 'dcdc'}
HTTParty.get(full_url,:body => params.to_json, :headers => headers) # body 代表请求体，传递参数
HTTParty.post(full_url,:body => params.to_json, :headers => headers)
```

