---
tags: js,Date
---

`new Date(dateString)`由于 dateString 不规范，会引起`Invalid Date`错误
dateString 有一下类型

```js
new Date('month dd,yyyy hh:mm:ss');
new Date('month dd,yyyy');
new Date(yyyy, mth, dd, hh, mm, ss);
new Date(yyyy, mth, dd);
new Date(ms);
```

### 解决方式如下：

dateString = "2019-01-23 15:12:12"

```js
export default class Utils {
  static toDate = dateString => {
    time = dateString.replace(/-|\s/g, ':').split(/:/);
    time = new Date(time[0], time[1] - 1, time[2], time[3], time[4], time[5]);
    return time;
  };
}
```
