---
tags: [rails,validates]
---

```ruby
validates :address,  presence: true, uniqueness: true
validates :email, presence: true, length: { maximum: 255 },
VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
format: { with: VALID_EMAIL_REGEX }
```
