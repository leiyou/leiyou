title: Mongodb ruby driver
date: 2015-08-23 16:26:46
tags: [Mongodb, ruby]
---
版本说明：ruby 1.9.3，mongodb 2.4.6

#### 准备工作
ruby安装mongo驱动
``` bash
gem install mongo
```
连接/创建数据库
``` ruby
client = Mongo::Client.new([127.0.0.1:27107], :database => 'mydb')
```
删除数据库
``` ruby
client.database.drop
```
#### CRUD操作
##### 创建集合

``` ruby
artists = client[:artists]
```
创建固定集合
``` ruby
artists = client[:artists, :capped => true, :size => 1024]
```
删除集合
```ruby
artists.drop()
```
##### insert——插入

插入单个文档
``` ruby
result = client[:artists].insert_one({:name => 'Mongo'})
```
<!-- more -->
插入多个文档
``` ruby
result = client[:artists].insert_many([
	{
		:name => 'NoSQL',
		:name => 'SQL'
	}
])
puts result.n #=> 输出文档个数
```
##### query——查询
查询name取值为SQL的文档，并输出其id值
``` ruby
client[:artists].find(:name => 'SQL').each do |doc|
	puts doc['_id']
end
```
查询可以配合skip()、limit()函数控制查询方式及返回的结果
如跳过前10个文档开始查询，返回的查询结果限制在10条内
``` ruby
doc = client[:artists].find(:name => 'SQL').skip(10).limit(10)
```
返回与查询匹配的文档数
``` ruby
puts client[:artists].find(:name => 'SQL').count
```
查询某个字段出现过的值
``` ruby
client[:artists].find.distinct(:name)
```

##### update——更新
更新单个文档
``` ruby
result = client[:artists].find(:name => 'SQL').update_one("$in" => {:age => 20})
puts result.n #=> return 1 更新了一个文档
```

更新多个文档
``` ruby
result = client[:artists].find(:name => 'SQL').update_many("$in" => {:age => 20})
puts result.n #=> return n 更新了所有与查询匹配文档
```
查找并更新单个文档
``` ruby
result = client[:artists].find(:name => 'SQL').find_one_and_replace(:language => 'mongo')
#=> 更新后，文档中只有_id、language两个字段
```

``` ruby
result = client[:artists].find(:name => 'SQL').find_one_and_replace('$set' => {:name =>'Jose'})
#=> 更新后，文档中原有字段不变，新插入一个name字段
```
``` ruby
artists.find(:name => 'José James').find_one_and_delete 
#=> 删除一个文档
```

##### remove——删除
删除单个文档
``` ruby
result = artists.find(:name => 'SQL').delete_one
result.n #=>return 1
```
删除多个文档
``` ruby
result = artists.find(:name => 'Björk').delete_many
result.n #=> return n
```
#### Index——索引
创建单个索引
``` ruby
index = client[:artists].indexes.create_one({ :name => 1 }, :unique => true)

```
##### 创建多个索引
``` ruby
index = client[:artists].indexes.create_many(
[
	{ 
		:key => {:name => 1} , :unique => true
	},
	{ 
		:key => {:ages => 1}
	},
]
```
##### 删除索引
``` ruby
client[:artists].indexes.drop_one('name_1') #=> 删除名称为 name_1的索引
client[:artists].indexes.drop_all #=>  删除集合中所有索引
```
##### 遍历索引
``` ruby
client[:artists].indexes.each do |index|
  puts index
end
```