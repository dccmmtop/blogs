## on()：适用于当前及未来的元素（比如由脚本创建的新元素)
  ```js
  $(selector).on(event,childSelector,data,function,map)
  ```
#### example
```js
$("#searchMoveVideoResult").on("click","ul li",function(){  
    $(this).css("border","5px solid #000");  
    }); 
```

