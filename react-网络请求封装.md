---
tags: [react]
date: 2018-09-18 15:37:48
---

```jsx
getQueryString = params => {
  console.log(params);
  return Object.keys(params)
    .map(k => k + '=' + params[k])
    .join('&');
};

request = params => {
  var method = params.method || 'GET';
  var qs = '';
  var body;
  var headers = params.headers || {
    Accept: 'application/json',
    'Content-Type': 'application/json',
  };

  if (['GET', 'DELETE'].indexOf(method) > -1)
    qs = '?' + getQueryString(params.data);
  // POST or PUT
  else body = JSON.stringify(params.data);

  var url = params.url + qs;
  console.log(url);

  return new Promise((resolve, reject) => {
    fetch(url, {method, headers, body})
      .then(data => data.json())
      .then(responseData => {
        resolve(responseData);
      })
      .catch(error => {
        reject(error);
      });
  });
};
export default class Request {
  static get = params => request(Object.assign({method: 'GET'}, params));
  static post = params => request(Object.assign({method: 'POST'}, params));
  static put = params => request(Object.assign({method: 'PUT'}, params));
  static delete = params => request(Object.assign({method: 'DELETE'}, params));
}
```
