

#### 获取第一条、最后一条记录

```ruby
Model.first

Model.first(options)

Model.find(:first, options)

Model.last

Model.last(options)

Model.find(:last, options)
```





#### 通过id获取记录

```ruby
Model.find(1, 10, options)

Model.find([1, 10], options)

```





#### find all

```ruby
Model.all(options)
```

对一组数据进行相同操作

```ruby
User.all.each do |user|
    NewsLetter.weekly_deliver(user)
end
```

如果表记录数比较大，这种方式比较耗资源，因为它会一次载入整个表的数据。改用以下这种方式，它每次只载入**1000**行，然后逐步yield完整个表

```ruby
User.find_each do |user|
  NewsLetter.weekly_deliver(user)
end
```

#### 自定义方式，find_each接受和find同样的options

```ruby

User.find_each(:batch_size => 5000, :start => 2000) do |user|
  NewsLetter.weekly_deliver(user)
end
```

find_in_batches，和find_each相似，但它yield时传递的是model对象数组，而不是单个model对象

```ruby
Invoice.find_in_batches(:include => :invoice_lines) do |invoices|
   export.add_invoices(invoices)
end
```



### 查询条件

通过替换？来传递条件值，可避免SQL注入

```ruby
Client.first(:conditions => ["orders_count = ?", params[:orders])
```

symbol占位条件

```ruby
Client.all(:conditions => ["created_at >= :start_date AND created_at <= :end_date", {:start_date => params[:start_date], :end_date => params[:end_date] }])
```

#### 范围条件 in(集合)

```ruby
Client.all(:conditions => ["created_at IN (?)", (params[:start_date].to_date)..(params[:end_date].to_date])
```

生成sql

```sql
SELECT * FROM users WHERE (created_at IN ('2007-12-31','2008-01-01','2008-01-02','2008-01-03','2008-01-04','2008-01-05', '2008-01-06','2008-01-07','2008-01-08'))
```

如果要生成日期时间，再加上.to_time

复制代码代码如下:

`params[:start_date].to_date.to_time`，生成2007-12-01 00:00:00格式

有上数据库会在以上条件中报错，如**Mysql会报查询语句过长的错误**，此时可以改成created_at > ? AND created_at < ?的形式

#### Hash条件

```ruby
Client.all(:conditions => {:locked => true })
```

**带范围条件**

```ruby
Client.all(:conditons => {:created => (Time.now.midnight - 1.day)..Time.now.midnight})
```

生成sql

```sql
SELECT * FROM clients WHERE (clients.created_at BETWEEN '2008-12-21 00:00:00' AND '2008-12-22 00:00:00')
```

**集合条件**

```ruby
Client.all(:conditons => {:orders_count => [1,3,5])
```

生成sql

```sql
SELECT * FROM clients WHERE (clients.orders_count IN (1,3,5))
```

### 查询选项

**排序**

* 单个排序

```ruby
Client.all(:order => "created_at ASC")
```

* 多个排序

```ruby
Client.all(:order => "orders_count ASC, created_at DESC")
```

**返回指定字段**

```ruby
Client.all(:select => "viewable_by, locked")
```

* 使用函数

```ruby
Client.all(:select => "DISTINCT(name)")
```

* 限定和偏移Limit and Offset

```ruby
Client.all(:limit => 5)
```

生成sql

```sql
SELECT * FROM clients LIMIT 5
```

```ruby
Client.all(:limit => 5, :offset => 5)
```

生成sql

```sql
SELECT * FROM clients LIMIT 5, 5
```

**Group分组**

```ruby
Order.all(:group => "date(created_at)", :order => "created_at")
```

**Having**

```ruby
Order.all(:group => "date(created_at)", :having => ["created_at > ?", 1.month.ago)
```

**只读**

```ruby
client = Client.first(:readonly => true)
client.locked = false
client.save
```

>  **对只读对象进行保存将会触发ActiveRecord::ReadOnlyRecord异常**

## 

**更新时锁定记录**

* 乐观锁Optimistic Locking

为使用乐观锁，须在表里建一个lock_version的字段，每次更新记录时，ActiveRecord自动递增lock_version的值，

```ruby
c1 = Client.find(1) 
c2 = Client.find(1)
c1.name = "Michael" 
c1.save 
c2.name = "should fail" 
c2.save # Raises a ActiveRecord::StaleObjectError
```

备注：You must ensure that your database schema defaults the lock_version column to 0.

This behavior can be turned off by setting ActiveRecord::Base.lock_optimistically = false.

指定乐观锁字段名

```ruby
class Client < ActiveRecord::Base 
    set_locking_column :lock_client_column 
end
```

* 悲观锁Pessimistic Locking

悲观锁定由数据库直接提供

```ruby
Item.transaction do
    i = Item.first(:lock => true)
    i.name = 'Jones'
    i.save
end
```

Mysql执行返回

```sql
 SELECT * FROM items LIMIT 1 FOR UPDATE Item Update (0.4ms) UPDATE items SET updated_at = '2009-02-07 18:05:56', name = 'Jones' WHERE id = 1
```

为特定数据库加入原始的lock声明

为Mysql的锁定声明为共享模式，即锁定时仍然可读

```ruby
Item.transaction do
    i = Item.find(1, :lock => "LOCK IN SHARE MODE")
    i.increment!(:views)
end
```

### 关联表

```ruby
Client.all(:joins => "LEFT OUTER JOIN address ON addresses.client_id = clients.id')
```

生成sql

```sql
SELECT clients.* FROM clients LEFT OUTER JOIN addresses ON addresses.client_id = clients.id
```

使用Array、Hash、Named Associations关联表

有如下model

```ruby
class Category < ActiveRecord::Base
    has_many :posts
end

class Post < ActiveRecord::Base
    belongs_to :category
    has_many :comments
    has_many :tags
end

class Comments <ActiveRecord::Base
    belongs_to :post
    has_one :guest
end

class Guest < ActiveRecord::Base
    belongs_to :comment
end
```



* 关联一个关系

```ruby
Category.all :joins => :posts
```

* 关联多个关系

```ruby
Post.all :joins => [:category, :comments]
```

* 嵌套关联

```ruby
Category.all :joins => {:posts => [{:comments => :guest}, :tags]}
```

* 为关联查询结果设定条件

```ruby
time_range = (Time.now.midnight - 1.day)..Time.now.midnight
Client.all :joins => :orders, :conditions => {'orders.created_at' => time_ran
```

\#或者

```ruby
time_range = (Time.now.midnight - 1.day)..Time.now.midnight 
Client.all :joins => :orders, :conditions => {:orders => {:created_at => time_range}}
```



### 优化载入

以下代码，需要执行1 + 10次sql

```ruby
clients = Client.all(:limit => 10)
clients.each do |client|
    puts client.address.postcode
end
```

优化：

```ruby
clients = Client.all(:include => :address, :limit => 10)
clients.each do |client|
    puts client.address.postcode
end
```

一次性载入post的所有分类和评论

复制代码代码如下:

`Post.all :include => [:category, :comments]`

载入category为1的所有post和cooment及tag

`Category.find 1, :include => {:posts => [{:comments => :guest}, :tags]}`

### 动态查询

```ruby
Client.find_by_name("Ryan")

Client.find_all_by_name("Ryan")
```

**方法，没有记录时抛出ActiveRecord::RecordNotFound异常**

```ruby
Client.find_by_name!("Ryan")
```

* 查询多个字段

```ruby
Client.find_by_name_and_locked("Ryan", true)
```

* 查询不到时就创建并保存

```ruby
Client.find_or_create_by_name(params[:name])
```

* 查询不到时创建一个实例，但不保存

```ruby
Client.find_or_initialize_by_name('Ryan')
```

### find_by_sql

```ruby
Client.find_by_sql("SELECT * FROM clients INNER JOIN orders ON clients.id = orders.client_id ORDER clients.created_at desc")
```

### select_all

和find_by_sql类似，**但不会用model实例化返回记录，你会得到一个hash数组**

```ruby
Client.connection.select_all("SELECT * FROM clients WHERE id = '1'")
```

### 判断记录是否存在

复制代码代码如下:

* 通过id来查询

```ruby
Client.exists?(1)

Client.exists?(1, 2, 3)

Client.exists?([1,2,3])
```

* 通过其他条件来查询

```ruby
Client.exists?(:conditions => "first_name = 'Ryan'")
```

\#没有参数时，则：表是空的 ? false : true

Client.exists?

### 计算

* 求结果集条数

```ruby
Client.count(:conditons => "first_name = 'Ryan'")
```

* 求某个字段非空白的条数

```ruby
Client.count(:age)
```

* 平均值

```ruby
Client.average("orders_count")
```

* 求最小值

```ruby
Client.minimum("age")
```

* 求最大值

```ruby
Client.maximum("age")
```

* 求和

```ruby
Client.sum("orders_count") 
```

