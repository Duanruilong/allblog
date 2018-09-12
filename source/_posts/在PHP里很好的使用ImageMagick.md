---
title: 在PHP里很好的使用ImageMagick
date: 2018-09-05 00:08:37
tags: [PHP,编程,后端]
description: 
copyright: true
categories: 后端
top: 
---

最近的PHP项目中，需要用到画图和图片拼接效果，这里是一些开发过程里用到的一些点还有就是一些踩过的坑。

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

