---
tags: [rails]
---

```ruby
def current_contract
  respond_to do |format|
    u = User.where(isCurrentContract: true).first
    cs = {token: u.address, auction: u.nickname, type: u.email.split('@')[0]}
    format.json { render json: cs.to_json }
  end
end
```
