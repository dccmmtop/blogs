---
tags: [ruby,随机数]
date: 2018-08-12 10:23:46
---

### shuffle

```ruby
(("0".."9").to_a + ("A".."Z").to_a).shuffle[0..6].to_a.join
```

> shuffle: 随机排列，中文名称是洗牌

### sample

```ruby
(("0".."9").to_a +  ("A".."Z").to_a).sample(6).join
```

### \*

```ruby
[*'0'..'9',*'A'..'Z'].sample(6).join
```

\*的意思是将范围展开
