---
tags: [ruby,算法,sort]
date: 2018-08-22 13:16:15
---

### 动画演示

![](http://ogbkru1bq.bkt.clouddn.com/merge-sort-example-300px.gif)

![](http://ogbkru1bq.bkt.clouddn.com/1534915400.png)

```ruby
def merge(x,first,mid,last)
  i = first
  q = 0
  j = mid+1
  z = []
  while(i <= mid && j <= last ) do
    if x[i] < x[j]
      z[q] = x[i]
      i += 1
    else
      z[q] = x[j]
      j += 1
    end
    q += 1
  end
  z += x[i..mid] if i <= mid
  z += x[j..last] if j <= last
  x[first..last] = z
end

def merge_sort(x,first,last)
  if(first < last)
    mid = first + (last - first) / 2
    merge_pass(x,first,mid)
    merge_pass(x,mid+1,last)
    merge(x,first,mid,last)
  end
end
x = [15, 8, 2, 10, 5, 12, 16, 4, 9, 17, 11, 14, 20, 1, 7, 18,0]
merge_pass(x,0,x.size-1)
p x
```
