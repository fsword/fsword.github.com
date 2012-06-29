---
layout: post
title: "active record错误汇总"
date: 2012-05-04 14:25
comments: true
categories: active_record
---
这个帖子专门记录一些使用active record时遇到的错误  
1. 错误描述：directive找不到自己的template  
错误代码  
```ruby  
class Directive
  belongs_to :template, :class_name => 'DirectiveTemplate'
  ...
end

create_table "directives", :force => true do |t|
  ...
  t.integer  "directive_template_id"
  ...
end
```
解释：这段代码本身并没有错误，问题是建立directives表的时候给字段命名为 directive_template_id。事实上，active record使用关联对象的外键，是根据field名称来进行的，它和设置的class_name，所以ar会根据directive对象的template_id字段查找关联的template  
解决方法：更改数据库字段名，或者在模型代码中指定foreign_key  

2. 错误描述：db:seed_fu 创建模型遇到表结构错误  
解释：初步试验发现，如果执行 `rake db:create db:migrate db:seed_fu` 会遇到这个问题，而将seed_fu单独执行则是正常的，考虑到发生错误的表在migrate阶段有过结构变更，估计是数据库事务导致alter语句没有提交，从而使得seed_fu失败
