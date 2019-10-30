---
tags: [图片处理,magick]
date: 2018-08-15 10:34:48
---

imagemagick 文档：<http://www.imagemagick.org/script/command-line-options.php>

convert 功能强大，用来批处理图片的放大、缩小、裁剪、旋转、合并、水印、拼接、格式转换等都非常方便，特别适合后台的图片处理。

### 1，获取图片信息

```shell
$identify image.png



image.png PNG 559x559 559x559+0+0 8-bit sRGB 467KB 0.000u 0:00.008
```

如果只需要获取宽高：

```shell
$identify -format "%wx%h" image.png
```

### 2，放大，缩小 -resize

```shell
$convert image.png -resize 200x200 resize.png
```

也可以按照比例（缩小一半）：

```shell
$convert image.png -resize 50% resize.png
```

还可以多次缩放（先缩小一半，再放大一倍，效果就是变模糊了）：

```shell
$convert image.png -resize 50%  -resize 200%  resize.png
```

### 3，放大，缩小 -sample

与 resize 的区别在于-sample 只进行了采样，没有进行插值，所以用来生成缩略图最合适

```powershell
$convert image.png -sample 50% sample.png
```

这个处理的效果就是马赛克：

```shell
$convert image.png -sample 10% -sample 1000% sample.png
```

### 4，裁剪 -crop

从（50，50）位置开始，裁剪一个 100X100 大小的图片：

```shell
$convert image.png -crop 100x100+50+50 crop.png
```

如果不指定位置，则按照这个大小分隔出小图片，这个命令生成 crop-0.png，crop-1.png，crop-2.png……：

```powershell
$convert image.png -crop 100x100 crop.png
```

可以指定裁剪位置的相对位置 -gravity：

```shell
$convert image.png -gravity northeast -crop 100x100+0+0 crop.png
```

-gravity 即指定坐标原点，有 northwest：左上角，north：上边中间，northeast：右上角，east：右边中间……

### 5，旋转 -rotate

```shell
$convert image.png -rotate 45 rotate.png
```

默认的背景为白色，我们可以指定背景色：

```shell
$convert image.png -backround black -rotate 45 rotate.png



$convert image.png -background #000000 -rotate 45 rotate.png
```

还可以指定为透明背景色：

```shell
$convert image.png -background rgba(0,0,0,0) -rotate 45 rotate.png
```

### 6，合并

合并指的是将一张图片覆盖到一个背景图片上：

```shell
$convert image.png -compose over overlay.png -composite newimage.png
```

-compose 指定覆盖操作的类型，其中 over 为安全覆盖，另外还有 xor、in、out、atop 等等

覆盖的位置可以通过-gravity 指定：

```shell
$convert image.png -gravity southeast -compose over overlay.png -composite newimage.png
```

这是将图片覆盖到底图的右下角。

### 7，更改图片的 alpha 通道

分两步：

```shell
$convert image.png -define png:format=png32  image32.png



$convert image32.png -channel alpha -fx "0.5" imagealpha.png
```

这个命令首先将 image.png 的格式改为 png32（确保有 alpha 通道），然后更改 alpha 通道置为 0.5，也就是半透明，值的范围为 0 到 1.0

可以使用将一张透明图片覆盖到原图上的方式做水印图片：

```shell
$convert image.png -gravity center -compose over overlay.png -composite newimage.png



$convert image.png -gravity southeast -compose over overlay.png -composite newimage.png
```

### 8，拼接

横向拼接（+append），下对齐（-gravity south）：

```shell
$convert image1.png image2.png image3.png -gravity south +append result.png
```

纵向拼接（-append），右对齐（-gravity east）：

```shell
$convert image1.png image2.png image3.png -gravity east -append result.png
```

### 9，格式转换

```shell
$convert image.png image.jpg



$convert image.png -define png:format=png32 newimage.png
```

### 10，文字注释

```shell
$convert image.png -draw "text 0,20 'some text'" newimage.png
```

从文件 text.txt 中读取文字，指定颜色，字体，大小，位置：

```shell
$convert source.jpg -font xxx.ttf -fill red -pointsize 48 -annotate +50+50 @text.txt result.jpg
```

### 11，去掉边框

```shell
$convert image.png -trim -fuzz 10% newimage.png
```
