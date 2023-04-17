# 发展史

**CSS1.0** :19912月W3 了第一个有关样式的标准CSS1.0。这个版本中，已经包含了的相关font的相关属性、颜色与背景的相关属

性、文字的相关属性、box的相关属性等。

**CSS2.0** DIV（块）+ CSS, HTML 和CSS结构分离 ，网页变得简单 SEO 1985年5月，CSS2.0正式推出。这个版本推荐的是内容和表

现效果分离的方式，并开始使用样式表结构。

**CSS2.1** 浮动、定位 2004年2月，CSS2.1正式推出。它在CSS2.0的基础上略微做了改动，删除了许多不被浏览器支持的属性。

**CSS3.0** 圆角、阴影、动画…







# 盒子模型

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210706201351414.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzU0ODk3ODcz,size_16,color_FFFFFF,t_70)



margin ：外边距

padding ：内边距

border ：边框

盒子的计算方式 ：margin + border +padding + 内容宽度

![img](https://img-blog.csdnimg.cn/20210706201322400.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzU0ODk3ODcz,size_16,color_FFFFFF,t_70)



## 边框

- 盒子边框

  - 设置边框样式（border-style）

    none：没有边框即忽略所有边框的宽度（默认值）

    solid：边框为单实线(最为常用的)

    dashed：边框为虚线

    dotted：边框为点线

    double：边框为双实线

- border-width 边框宽度

- **外边距的妙用 ：居中元素** margin: 0 auto; （上下为0 左右居中）

- margin属性用于设置外边距。 设置外边距会在元素之间创建“空白”， 这段空白通常不能放置其他内容。

- margin参数（padding相同）： 四个的话：是上、右、下、左 顺时针

   三个a、b、c 是 上a 左右b 下c

   两个 上下 左右

- padding不会出现负值，margin是可以出现负值的

## 圆角边框

- border-radius: 左上角 右上角 右下角 左下角;



## 阴影

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <style>

        div{
            width: 1200px;
            margin: 0 auto;
        }
        img{
            border-radius: 91.5px;
            box-shadow: 10px 10px 100px yellow;
        }

    </style>

</head>
<body>
<div>
    <img src="images/abc.png"/>
</div>

</body>
</html>
```





# 选择器

## 基本选择器

标签选择器

**类选择器** class

**id选择器**

三种选择器优先级：

- 不遵循就近原则 ，是固定的

   **id选择器>类选择器>标签选择器**

## 层次选择器

**后代选择器**

**子选择器** 

**相邻兄弟选择器**（弟弟选择器） 同辈向下最近的一个

**通用选择器**：当前选中元素的向下的所有兄弟元素



## 属性选择器

```
属性名 . 属性名  =  属性值（正则）
=  绝对等于
*=  包含这个元素
^=  以这个开头
$=  以这个结尾
```



# *浮动

## 标准文档流

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210321004748316.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01lbW9yeV9zaGFyZQ==,size_16,color_FFFFFF,t_70#pic_center)

- 块级元素：独占一行
  - h1-h6 p div 列表
- 行内元素：不独占一行
  - span a img strong
- 行内元素可以被包含在块级元素里面，反之不可以





## display

- 这个也是实现行内元素排列的一种方式，但很多情况下我们都是用float



## float

- float：左右浮动
- 选择器 {float:属性值;}

| 属性值 | 描述                 |
| ------ | -------------------- |
| left   | 元素向左浮动         |
| right  | 元素向右浮动         |
| none   | 元素不浮动（默认值） |





## 解决父级边框塌陷的问题

- clear

```both
/* 
clear right:  右侧不允许有浮动元素
clear left： 左侧不允许有浮动元素
clear both； 两侧侧不允许有浮动元素
clear none；
*/
123456
```

解决方案：

增加父集元素的高度

```css
.father{
    border: 1px solid red;
    height：  800px;
}
1234
```

增加一个空的div标签，清除浮动

```css
<div class="clear"></div>

.clear{
    clear: both;
    margin: 0;
    padding: 0;
}
1234567
```

overflow

```css
在父级元素中增加一个  overflow:hidden
```

父类添加一个伪类：after

```css
#father:after{
	content:'';
	dispaly:block;
	clear:both;
}
12345
```

小结：

1. 浮动元素后面增加空div（简单，代码中尽量避免空div）
2. 设置父元素的高度（简单，元素假设有了固定的高度，就会被限制）
3. overflow（简单，下拉的一些场景避免使用）
4. 父类添加一个伪类：after（推荐，写法稍微复杂一些，但是没有副作用，推荐使用）



## 对比

- display

方向不可控

- float

浮动起来的话会脱离标准文档流，需要解决父级边框塌陷的问题





# *定位



## 相对定位

相对定位： position ：relative

相对与原来的位置进行指定的偏移，相对定位的话，它仍然在标准文档流中，原来的位置会被保留



## 绝对定位

绝对定位：基于xxx进行定位。上下左右

1. 没有父级元素的前题下，会相对与浏览器定位
2. 假设父级元素存在定位，我们通常会相对于父级元素进行偏移
3. 在父级元素范围内移动

相对于父级或浏览器的位置进行指定的偏移，绝对定位的话，它在标准文档流中，原来的位置不会被保留。



## 固定定位

位置固定不变的
