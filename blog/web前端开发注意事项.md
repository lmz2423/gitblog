<!--
author: 滇西之王
head: http://q.qlogo.cn/qqapp/100229475/C96DA226D9D07DECADAA54A78E9FEDF9/100
date: @2015-12-27
title: web前端开发注意事项
tags: css
category: css
status: publish
summary: 文中主要提到web前端开发中，碰到的一些问题，如怎么使用rem 怎么更好的设置重置样式，怎么利用rem进行自适应布局。
-->
###怪异模式
由于现在xp，win7的使用率比较高，虽然在web前端界，大家都不怎么去考虑IE6的兼容性，但是IE8在现在还是要考虑的。[怪异模式](https://zh.wikipedia.org/zh/%E6%80%AA%E5%BC%82%E6%A8%A1%E5%BC%8F)是用来维持对较旧网页的兼容性的一种处理。这在IE8中是需要考虑的。如果有人在IE8中浏览网站时单击了“兼容性视图”按钮，IE8就会进入IE7模式，显示出的网页将看不到IE8原本可以显示的css2.1的设置。**但我们可以通过一个指令来让浏览器忽略这种行为**
``` vbscript-html
 <meta http-equiv="X-UA-Compatible" content ="IE=edge" />
```
将这条指令放在网页的`<head>`中（最好把它放在`<title>`标签下方）。标签中的“IE=edge”部分将会命令IE8之后的版本也要它们的标准模式来显示这些网页。
### 重置样式的处理
由于不同浏览器自带的样式各不一样，为了覆盖浏览器的自带样式，web工作者设置了一套重置样式。目前使用的最多是Eric Meyer的[reset.css](http://meyerweb.com/eric/tools/css/reset/) 。但我们不能一味粘贴复制，而是根据自己的项目需求来修改这份样式。对于在网页中没有使用到到的标签，我们完全没有在重置样式添加。如果我们的网页的只使用了`h1` `h2`,那我们没有重置样式中添加`h3`,`h4`,`h5`,`h6`的样式。很多人都会用到如下的一段代码
```css
html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed, 
figure, figcaption, footer, header, hgroup, 
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
	margin: 0;
	padding: 0;
	border: 0;
	font-size: 100%;
	font: inherit;
	vertical-align: baseline;
}
```
但是像`div` 默认的**margin padding** 就是 **0**  这对于它来说就是多此一举。随着**css3**的广泛应用。为了重置在iphone系统，winphone系统的样式。现在又推出了[normalize.css](https://necolas.github.io/normalize.css/)样式。如在ios系统中
对于**button, input[type="button"] ,input[type="reset"],input[type="submit"]**,ios系统会展示成自己特有的方式。为了覆盖ios特有的方式。
我们在在重置样式表添加
``` css
button,
html input[type="button"],
input[type="reset"],
input[type="submit"] {
  -webkit-appearance: button; 
  cursor: pointer; 
}
```
其他的设置可以参考normalize.ss。重置样式的设置,记住两字即可**精简**。
###使用rem还是em，rem在自适应中的用途
  在书写css的时候，我们经常用到`px`,`em` ,`rem`。 `px`的单位是绝对单位。而`em`和`rem`是相对单位。
 那`em`和`rem`之间又有什么区别呢？`em`是相对于父级元素的**font-size** ,譬如`div`标签中包含一个`p`标签。如果该`div`标签设置的font-size 为12px ,如果我们设置`p`标签的font-size为2em ,那p元素的font-size大小默认为24px. 而高度，宽度，也可以使用em来设置。如上述的`p`标签的weight设置为2em,那p元素高度就是24px，再加上媒体查询。我们就可以进行进行自适应布局。在rem出现之前，em是一种不错的方法，但是有了rem之后。我们有更好的方法对屏幕进行自适应设置.
 
 **rem**是相对于根元素`html`一个比较单位。所有标签如果采用了rem,那么它的参考对象就是 html的font-size的值。

 **rem**单位的浏览器支持情况如下。
 ![rem支持情况](http://www.alonehero.com/blog/img/rem.png)
 如果font-size设置为`10px`  那么如果子标签设置了`font-size:1.5rem ` 那子标签的字体大小就为15px。
 我们在320和360 以及414px（iphone6 plus 和iphone6s plus）的屏幕上的显示的按钮的大小。如果按照px的单位来设置，那么在360 以及414px的屏幕上就会看起来很少。如果采用rem 加上媒体查询就可以避免这种问题。
 
 
```css
html{
  font-size:20px;
}
/*针对android的手机*/
@media screen and (width:360px) {
 html{
 font-size:21.8333px;
 }
}
/*针对iphone6*/
@media screen and (width:375px) {
 html{
 font-size:23.4375px;
 }
}
/**针对iphone6s plus **/
@media screen and (width:414px) {
 html{
  font-size:25.875px;
 }
}

/*针对小米note手机 ,公司的用户使用这种机型的比较多*/
@media screen and (width:393px) {
	html{
	font-size:21.83333px;
	}
}
```
有些同学就有疑问了，为什么我们把根元素的font-size设置为**20px**，而不是**10px** 。10px的设置更好计算。原因在于
当我们设置成10px的时候。如果我们的子元素的高设置成为1rem的话，子元素的高为12px，而不是10px 。但是如果我们把根元素的font-size 设置成为**20px**的话，那么如果子元素的高设置成为0.5rem ,子元素高就为10px。原因就在如此。

### ios把数字转成电话的bug
在网页中如果我们的文本出现诸如**41400-123**的文本，在ios系统会显示为电话号码。颜色会变成蓝色，点击的时候，还可以拨打，这并不是我们想要的。

我们可以在`meta`标签中添加
```html
    <meta content="telephone=no" name="format-detection"/>
```
来解决此类问题。

###对于双核浏览器的处理
现在国内的浏览器厂商，如**360,搜狗，猎豹**在国内的PC端有很多的用户群。由于该浏览器既有webkit内核,也有兼容旧网页的IE内容。但是我们的网页，如果用上述浏览器打开，还是希望浏览器使用webkit内核来渲染我们的网页。对此360提倡添加一个meta标签。
```html
<meta name="renderer" content ="webkit" />
```
至于各家浏览器支持的怎么样，大家可以试一试。
