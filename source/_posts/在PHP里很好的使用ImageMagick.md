---
title: 在PHP里使用ImageMagick生成base64图片
date: 2018-09-05 00:08:37
tags: [PHP,编程,后端]
description: 
copyright: true
categories: 后端
top: 
---

最近的PHP项目中，需要用到画图和图片拼接效果，这里是一些开发过程里用到的一些点还有就是一些踩过的坑。通过ImageMagick生成base64图片格式，为前端所使用。

![PHP](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/php.jpg)

<!-- more -->

> 一些需要的知识点

# PHP将图片转base64编码以及base64图片转换为图片并保存代码

## 图片转base64编码

```h

/*图片转换为 base64格式编码*/
$img = 'uploads/about.png';
$base64_img = base64EncodeImage($img);
echo '<img src="' . $base64_img . '" />';
 
function base64EncodeImage ($image_file) {
    $base64_image = '';
    $image_info = getimagesize($image_file);
    $image_data = fread(fopen($image_file, 'r'), filesize($image_file));
    $base64_image = 'data:' . $image_info['mime'] . ';base64,' . chunk_split(base64_encode($image_data));
    return $base64_image;
}
```

## base64图片转换为图片并保存

```h
/*  base64格式编码转换为图片并保存对应文件夹 */
function base64_image_content($base64_image_content,$path){
    //匹配出图片的格式
    if (preg_match('/^(data:\s*image\/(\w+);base64,)/', $base64_image_content, $result)){
        $type = $result[2];
        $new_file = $path."/".date('Ymd',time())."/";
        if(!file_exists($new_file)){
            //检查是否有该文件夹，如果没有就创建，并给予最高权限
            mkdir($new_file, 0700);
        }
        $new_file = $new_file.time().".{$type}";
        if (file_put_contents($new_file, base64_decode(str_replace($result[1], '', $base64_image_content)))){
            return '/'.$new_file;
        }else{
            return false;
        }
    }else{
        return false;
    }
}
 
echo base64_image_content($base64_img,"uploads/");
```
# base64

Base64是一种用64个字符来表示任意二进制数据的方法。
Base64的原理很简单，首先，准备一个包含64个字符的数组：

`['A', 'B', 'C', ... 'a', 'b', 'c', ... '0', '1', ... '+', '/']`
然后，对二进制数据进行处理，每3个字节一组，一共是3x8=24bit，划为4组，每组正好6个bit

如果要编码的二进制数据不是3的倍数，最后会剩下1个或2个字节怎么办？Base64用\x00字节在末尾补足后，再在编码的末尾加上1个或2个=号，表示补了多少字节，解码的时候，会自动去掉。



使用jpg图片体积要比png小
使用PHP的Imagick类进行图像的操作

# 具体操作

## (1).创建一个底图,宽750px，高1046px，白色背景，格式为jpg的图片
```h
// 初始化一个画板
        $img =new Imagick();
        $img->newImage(750,1046,'white','jpg') ; 
        
```

## (2).在底图上添加需求图片
前提是我们已经知道了需要合并的图片链接地址
```h
$item_img='https://img.alicdn.com/bao/uploaded/i1/1750208593/TB1rgM3hhtnkeRjSZSgXXXAuXXa_!!0-item_pic.jpg'

第一步：实例化图片
$imgtwo = new Imagick($item_img);

第二步：设置添加图片的大小
$imgtwo->resizeImage(750,764,Imagick::FILTER_LANCZOS,1);

关于resizeImage参数说明
    bool Imagick::resizeImage ( int $columns , int $rows , int $filter , float $blur [, bool $bestfit = false ] )

参数：
  ● columns 图片的宽度
  ● rows 图片高度
  ● filter 过滤器，用于过滤图片，有高斯filte根据情况而定
  ● blur blur=1 为虚化， blur =-1 为锐化

第三步：与底图合并
$img->compositeImage($imgtwo,$imgtwo->getImageCompose(),0,0);

使用compositeImage();
    bool Imagick::compositeImage ( Imagick $composite_object , int $composite , int $x , int $y [, int $channel = Imagick::CHANNEL_ALL ] )

参数：
  ● composite_object ：用于合并的图片的Imagick对象
  ● composite：合并操作，定义操作常量。 具体请查看 合并操作常量列表
  ● x：相对图像顶点左上位置（0,0）的横坐标
  ● y：相对图像顶点左上位置（0,0）的纵坐标
  ● channel：通过传入一个通道常量，来开启通道模式。为了支持多个通道，可以通过二进制运算的操作来合并多个通道常量。

到这里就可以得到一个合并的图片了
1、加一个header信息，可以直接在网页上查看图片
    header("Content-Type: img/png");
    echo $img;
2、可以把图片在指定目录中生成，在指定目录下生成为img.png
	$file="./img.png";
	$img->writeImage($file);


我这里是这样处理：
    header ( 'Content-type: ' . strtolower ($img->getImageFormat ()) );
    $type = strtolower($img->getImageFormat());
    $dest_img='/data/tmp/' . md5(microtime(true)).'.'.$type;    //要生成的图片的路径，随机生成图片名称


```


## (3).图片上拼接文字
写入文字以添加店铺文字为例，逐步完成文字的写入。
```h
    $shop_title='测试店铺'；
    // 添加店铺文字
    $drawQr = new ImagickDraw(); // 实例化ImagickDraw
    $drawQr -> setFillColor(new ImagickPixel('#999999')); // 颜色
    $drawQr -> setFontSize('24'); // 大小
    $drawQr -> setFont('../../conf/Microsoftyahei.ttf'); // 字体
    $drawQr -> setTextAlignment(Imagick::ALIGN_LEFT); // 字体方向
    // ps： Imagick::ALIGN_RIGHT 朝右边    Imagick::ALIGN_LEFT 左边   Imagick::ALIGN_CENTER 中间
    $drawQr -> setTextEncoding("utf-8"); // 字体编码
    $drawQr -> annotation(114,990,$shop_title); // 画出文字
    $img -> drawImage($drawQr);  // 画在地板上
```

详细解读：
 - 1、实例化ImagickDraw类：
    `$drawQr = new ImagickDraw(); ` 
 - 2、设置字体颜色
`$drawQr -> setFillColor(new ImagickPixel('#999999')); `
 - 3、设置字体大小
`$drawQr -> setFontSize('24'); `
 - 4、设置字体格式
`$drawQr -> setFont('../../conf/Microsoftyahei.ttf');`
 - 5、设置字体方向
`$draw->setTextAlignment(Imagick::ALIGN_RIGHT);`
> ps： Imagick::ALIGN_RIGHT 朝右边    Imagick::ALIGN_LEFT 左边   Imagick::ALIGN_CENTER 中间
 - 6、设置字体编码
`$drawQr -> setTextEncoding("utf-8");`
 - 7、画出文字
`$drawQr -> annotation(114,990,$shop_title); `
 - 8、在底图上写入字体
`$img -> drawImage($drawQr);`

写入文字这个地方的一些坑：

没有设置字体格式时，中文字会解析错误
（英文没有问题）
![PHP](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/php/php_megick_1.png)

（汉字解析失败）

![PHP](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/php/php_megick_2.png)

(设置字体格式正常显示)

![PHP](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/php/php_megick_3.png)


## (4).图片base64导出
最终得到的图片我们组要以base64的格式传递给前端，进行以下操作，把我们最后拼接的到的图片base64转换输出。
```h
    $dest_img='/data/tmp/' . md5(microtime(true)).'.'.$type; //要生成的图片的路径
    $Return = array();
    // *图片转换为 base64格式编码*
    $base64_image = '';
    $image_info = getimagesize($dest_img);
    $image_data = fread(fopen($dest_img, 'r'), filesize($dest_img));
    $base64_image = 'data:' . $image_info['mime'] . ';base64,' . chunk_split(base64_encode($image_data));
    $Return['data']=$base64_image;
    return  $Return;
```
`$base64_image`就是base64格式的图片。

需要注意的是前端得到的额base64数据里包含有`'\r\n'`回车字符，需要特殊处理才可以正确显示图片。

![PHP](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/php/php_megick_4.png)

（最后得到的合并图片）

![PHP](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/php/php_megick_5.png)

(调整拼接图片大小得到不同的图片)
![PHP](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/php/php_megick_6.png)
