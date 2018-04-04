## Simple_form

#### 安裝

`gem 'simple_form'`

`bundle install`

`rails generate simple_form:install --bootstrap`

基本使用方式

只要输入f.input，simple_form會自動根據資料庫中該欄位的資料型態，判斷此input顯示的格式

```erb
<%= simple_form_for @post do |f| %>
  <div class="form-group"> 
  <%= f.input :username, label: 'Your username please', error: 'Username is mandatory, please specify one' %>
  <%= f.input :password, hint: 'No special characters.' %>  <%= f.input :email, placeholder: 'user@domain.com' %> 
  <%= f.input :remember_me, inline_label: 'Yes, remember me' %>
  </div>
  <%= f.submit "Submit", class: "btn btn-primary", data: { disable_with: "Submiting..." } %>
<% end %>
```

若有錯誤，也可使用as手動調整欄位格式

```erb
<%= simple_form_for @user do |f| %> 
  <%= f.input :username %>
  <%= f.input :password %>
  <%= f.input :description, as: :text %>
  <%= f.input :accepts,     as: :radio_buttons %>
  <%= f.button :submit %>
<% end %>
```



#### 排版

為了排版，平常寫在後面的參數，也可以分開填寫

例如

```erb
<%= simple_form_for @user do |f| %>
  <%= f.input :username, label: 'username', hint: 'No special characters, please!', error: 'username', id: 'user_name_error', full_error: 'token'  %>
  <%= f.submit 'Save' %>
<% end %>
```

可調整為：

```erb
<%= simple_form_for @user do |f| %> 
  <%= f.label :username %>
  <%= f.input_field :username %> 
  <%= f.hint 'No special characters, please!' %> 
  <%= f.error :username, id: 'user_name_error' %> 
  <%= f.full_error :token %>
  <%= f.submit 'Save' %>
<% end %>
```

#### 疑難雜症

關閉label

`label: false `

備註hint

`hint: '文字'`