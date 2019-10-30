---
tags: [rails,健壮参数]
---

```ruby
def user_params
  params.require(:user).permit(:nickname, :email, :address)
end
```
