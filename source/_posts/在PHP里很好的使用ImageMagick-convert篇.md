---
title: 在PHP里很好的使用ImageMagick---convert篇
date: 2018-09-18 00:15:54
tags: [PHP,编程,后端]
description: 
copyright: true
categories: 后端
top: 
---

ImageMagick 包括一组命令行工具来操作图片，之前的ImageMagick实现base64图片的逻辑在服务器端可能会造成溢出的事故，所以在接下的文章里会介绍另外一种好的实现方式就是----convert命令。使用命令格式的形式去生成图片和大小的裁剪数据格式的转换。

![PHP](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/php.jpg)

<!-- more -->

# 基本命令与格式

## 1、基本命令

* `magick:` 创建、编辑图像，转换图像格式，以及调整图像大小、模糊、裁切、除去杂点、抖动 ( dither )、绘图、翻转、合并、重新采样等。
* `convert:` 等同于 magick 命令。
* `identify:` 输出一个或多个图像文件的格式和特征信息，如分辨率、大小、尺寸、色彩空间等。
* `mogrify:` 与 magick 功能一样，不过不需要指定输出文件，自动覆盖原始图像文件。
* `composite:` 将一个图片或多个图片组合成新图片。
* `montage:` 组合多个独立的图像来创建合成图像。每个图像都可以用边框，透明度等特性进行装饰。

* `compare:` 从数学和视觉角度比较源图像与重建图像之间的差异。
* `display: `在任何 X server 上显示一个图像或图像序列。
* `animate:` 在任何 X server 上显示图像序列。
* `import:` 保存 X server 上的任何可见窗口并把它作为图像文件输出。可以捕捉单个窗口，整个屏幕或屏幕的任意矩形部分。
* `conjure: `解释并执行 MSL ( Magick Scripting Language ) 写的脚本。
* `stream:` 一个轻量级工具，用于将图像或部分图像的一个或多个像素组件流式传输到存储设备。在处理大图像或原始像素组件时很有用。


## 2、命令格式

基本命令的使用，遵循 Unix 风格的标准格式，例如  `command [options] input_image output_image`

> 将一张宽高 300x300 的图片 goods.png 转换成 200x200 的goods.jpg，可以这样用

```h
convert -resize 200x200 goods.png goods.jpg

```




# 开始绘制生成图片

## 一些前期参数

### 1、入参

```h

$item_img='https://img.alicdn.com/bao/uploaded/i1/1750208593/TB1rgM3hhtnkeRjSZSgXXXAuXXa_!!0-item_pic.jpg';
$item_title='测试字体';
$shop_title='测试店铺';
$shop_img='http://q.aiyongbao.com/item/web/images/qap_img/mobile/userAvatar.png';
$qr_img='https://img.alicdn.com/tfscom/TB1uJDStYZnBKNjSZFKwu3GOVXa.png';
$numid_share='0000000';
$qr_title='长按识别二维码';

// 对宝贝标题裁剪
$drawtitone=mb_substr($item_title,0,13, 'utf-8');
$drawtittow=mb_substr($item_title,13,13, 'utf-8');
$drawtitthree=mb_substr($item_title,26,13, 'utf-8');
$time_date = time(); // 时间戳
$workDir = '/data/tmp/'.$numid_share.'_con'.$time_date; // 目标路径---->指定一个文件夹保存生成图片过程里的图片

```

### 2、先下载素材文件

我们这里需要一个拼接图片的链接，所以先下载到之前的创建的文件夹下，因为这些素材图片只是为了最后的拼接，最后可以完全删除。
```h

$item_img_down = $workDir.'/itemimage.jpg';
$shop_img_down = $workDir.'/shopimage.jpg';
$qr_img_down = $workDir.'/qrimage.jpg';
// 下载宝贝主图
\NetworkUtils::curlDownload($item_img, $item_img_down);
// 下载店铺图片
\NetworkUtils::curlDownload($shop_img, $shop_img_down);
// 下载二维码
\NetworkUtils::curlDownload($qr_img, $qr_img_down);

```

`\NetworkUtils::curlDownload($item_img, $item_img_down);` 下载`$item_img`保存在目标路径文件夹下的`$workDir.'/itemimage.jpg'`路径。


### 3、指定字体和颜色

```h
$fontFamily = '/usr/share/fonts/chinese/msyh.ttf';
$fontColor = '#333333';
$fontColor_shoptitle = '#999999';
$fontColor_qrtitlt = '#666666';
```

### 4、创建一个底图
创建一个`750x1046`白色的底图，我们可以在上面绘制文字

```h
// 创建一个底图(并且加上一些文字)

$back_img=$workDir.'/bg.jpg'; //要生成的图片的路径---->可以在服务器端看到
$back_cmds="convert 'xc:[750x1046!]' -background white -font {$fontFamily} -draw 'text 580,994 \"{$qr_title}\"' -fill \"{$fontColor_qrtitlt}\" 
            -pointsize 20 -draw 'text 114,990 \"{$shop_title}\"' -font {$fontFamily} -pointsize 22 -fill '#999999' {$workDir}/bg.jpg";
$result_back_cmds = \SystemCommon::runningCmd($back_cmds);  

```
> 这里需要注意一下文字的绘制命令顺序，否则会出现没有生效的问题。
> `-font {$fontFamily} -draw 'text 580,994 \"{$qr_title}\"' -fill \"{$fontColor_qrtitlt}\" -pointsize 20`

解释一下：
* `xc:[100x40!]`: 设置画布大小的一种简写方式，方括号里写入画布宽高，注意要加 !
* `-fill 'rgba(0, 0, 0, 0)'`：设置了文本的填充颜色
* `text 114,990`: 对文字进行定位
* `-font`：指定字体
* `-pointsize`：指定文本的字体大小
* `-draw`：绘图选项，text 声明绘制文本， 0,0 声明文本距离图片左上角的偏移值，绘制文本的格式为 text x,y string，当然还可以绘制其他类型，诸如圆 ( circle )、折线 ( polyline )
  

继续添加文字：

```h
$back_img_shoptitle=$workDir.'/btitle.jpg'; //要生成的图片的路径
$back_cmds_shoptitle="convert -draw 'text 40,820 \"{$drawtitone}\"' -font {$fontFamily} -pointsize 32 -fill \"{$fontColor}\" -draw 'text 40,866 \"{$drawtittow}\"' 
                    -font {$fontFamily} -pointsize 32 -fill \"{$fontColor}\" -draw 'text 40,912 \"{$drawtitthree}\"' -font {$fontFamily} -pointsize 32 -fill \"{$fontColor}\"  
                    {$back_img} {$workDir}/btitle.jpg";
$result_back_cmds_shoptitle = \SystemCommon::runningCmd($back_cmds_shoptitle);  

```
效果：

![convert](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/php/php_convert_6.png)


### 5、拼接图片
现在就可以在之前得到的图片上拼接所需要的图片了

```h
 // 在底图上合并商品主图
$back_img_itemimg='/data/tmp/' . md5(microtime(true)).'.jpg';  //要生成的图片的路径
$geometryX = "+" . 0; // 定位
$geometryY = "+" . 0; // 定位
$customPicPos = "northwest";
$back_cmds_itemimg = "convert {$back_img_shoptitle} {$workDir}/item_img_mag.jpg -gravity {$customPicPos} -geometry {$geometryX}{$geometryY} 
                    -compose over -composite -antialias -set colorspace sRGB -colorspace sRGB {$workDir}/result.jpg";
$result_back_cmds_itemimg = \SystemCommon::runningCmd($back_cmds_itemimg); 

```
解释一下：
* `-geometry`: 设置文本在图片里的排列方式 ( 类似 CSS 里的 align-items + justify-content )，center 表示水平垂直都居中，其他值还可以是：NorthWest, North, NorthEast, West, East, SouthWest, South, SouthEast，不记大小写
* `composite`: 将一个图片或多个图片组合成新图片。

这里是把`{$workDir}/item_img_mag.jpg`路径的图片绘制到之前绘制文字的图片上`$back_img_shoptitle`，最终得到的图片保存路径是在`{$workDir}/result.jpg`

让我们继续操作图片

```h
// 合并二维码 
$geometryXshop = "+" . 40;
$geometryYshop = "+" . 950;
$back_cmds_shopimg = "convert {$workDir}/result.jpg {$workDir}/shop_img_mag.jpg -gravity {$customPicPos} -geometry {$geometryXshop}{$geometryYshop} -compose over -composite -antialias -set colorspace sRGB -colorspace sRGB {$workDir}/result1.jpg";
$result_back_cmds_shopimg = \SystemCommon::runningCmd($back_cmds_shopimg);  

// 合并店铺图标
$geometryXqr = "+" . 520;
$geometryYqr = "+" . 784;
$back_cmds_qrimg = "convert {$workDir}/result1.jpg {$workDir}/qr_img_mag.jpg -gravity {$customPicPos} -geometry {$geometryXqr}{$geometryYqr} -compose over -composite -antialias -set colorspace sRGB -colorspace sRGB {$workDir}/result2.jpg";
$result_back_cmds_qrimg = \SystemCommon::runningCmd($back_cmds_qrimg);  

```

效果：

![convert](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/php/php_convert_7.png)

发现图片并不是我们预期的效果，拼接我们也设置了图片的大小，但是没有起作用，尝试过不同的方案后决定在拼接之前把图片统一放大为需要拼接的尺寸来操作。

### 6、放大拼接图片尺寸

需要对多拼接的图片都进行操作

```h
 // 看来需要先放大主图
$item_img_m="convert -resize '750x766!' {$item_img_down} {$workDir}/item_img_mag.jpg";
$item_img_mag = \SystemCommon::runningCmd($item_img_m);  
// 看来需要先放大二维码
$shop_img_m="convert -resize 64x64 {$shop_img_down} {$workDir}/shop_img_mag.jpg";
$shop_img_mag = \SystemCommon::runningCmd($shop_img_m);  
// 看来需要先放大店铺图标
$qr_img_m="convert -resize 200x200 {$qr_img_down} {$workDir}/qr_img_mag.jpg";
$qr_img_mag = \SystemCommon::runningCmd($qr_img_m);  

```

> -resize 延伸解读

 IamgeMagick 提供了几种符号来定义缩放

 ```h
 convert -resize '150x100!' goods.jpg thumbnail.jpg

 convert -resize '150x100>' goods.jpg thumbnail.jpg

 convert -resize '150x100<' goods.jpg thumbnail.jpg

 ```
 * `!`：不管图片宽高如何，都缩放成 150x100 这样的尺寸。
 * `>`：只有宽高均大于 150x100 的图片才缩放成该尺寸 ( 按比例取最大值 )，小于的图片不做处理。
 * `<`：与 > 功能相反

缩放对比

![convert](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/php/php_convert_1.png)


这样的结果也不是我们需要的
![convert](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/php/php_convert_4.png)

只有`convert -resize '750x766!' {$item_img_down} {$workDir}/item_img_mag.jpg`这样固定尺寸之后可以得到：


![convert](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/php/php_convert_5.png)


### 7、图片转换为 base64格式

我们最后同样的以base64格式输出给前端

```h
$stdout = trim($result_back_cmds_qrimg["stdout"]);
$Return = array();
if (empty($stdout) || strpos($result_back_cmds_qrimg["stderr"], "identify") != false) {  // runningCmd执行成功
    $dest_img = $workDir.'/result2.jpg';
    // /*图片转换为 base64格式编码*/
    $base64_image = '';
    $image_info = getimagesize($dest_img);
    $image_data = fread(fopen($dest_img, 'r'), filesize($dest_img));
    $base64_image = 'data:' . $image_info['mime'] . ';base64,' . chunk_split(base64_encode($image_data));

    $Return['data']=$base64_image;
    $Return['dest_img']=$dest_img;
    $Return['type']='success';
    $Return['msg']=1;
    return $Return;
}
$Return['data']='';
$Return['msg']=2;
return $Return;

```

`$workDir.'/result2.jpg`是我们最终的拼接图片，至于文件夹下的其他图片，可以选择删除。我们只要得到结果就好了。

（小姐姐拼接图）
![convert](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/php/php_convert_8.png)


这次`convert命令`的介绍是以解读整个方法的形式，对拼接的开始进行到结尾生成最终的图片，希望对大家有力所能及的帮助。

欢迎光顾个人[Blog](https://duanruilong.github.io/blog)


