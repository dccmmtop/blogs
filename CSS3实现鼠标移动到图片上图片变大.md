## CSS3实现鼠标移动到图片上图片变大

### [转载](https://blog.csdn.net/u014175572/article/details/51535768)

###### SS3实现鼠标移动到图片上图片变大（缓慢变大，有过渡效果，放大的过程是有动画过渡的，这个过渡的时间可以自定义）

CSS3的transform:scale()可以实现按比例放大或者缩小功能。

CSS3的transition允许CSS的属性值在一定的时间区间内平滑地过渡。这种效果可以在鼠标单击、获得焦点、被点击或对元素任何改变中触发，并圆滑地以动画效果改变CSS的属性值。

效果如下图所示：

1、当未鼠标未放到图片上的效果：

![img](https://img-blog.csdn.net/20160530100006074)

2、当鼠标放到图片上后(放大的过程是有动画过渡的，这个过渡的时间可以自定义)：

![img](https://img-blog.csdn.net/20160530100059753)

代码如下：

```html
<!DOCTYPE html>  
<html>  
    <head>  
        <meta charset="UTF-8">  
        <title></title>  
        <style type="text/css">  
            div{  
                width: 300px;  
                height: 300px;  
                border: #000 solid 1px;  
                margin: 50px auto;  
                overflow: hidden;  
            }  
            div img{  
                cursor: pointer;  
                transition: all 0.6s;  
            }  
            div img:hover{  
                transform: scale(1.4);  
            }  
        </style>  
    </head>  
    <body>  
        <div>  
            <img src="img/focus.png" />  
        </div>  
    </body>  
</html>  
```

其中：

transition: all 0.6s;表示所有的属性变化在0.6s的时间段内完成。

transform: scale(1.4);表示在鼠标放到图片上的时候图片按比例放大1.4倍。

 