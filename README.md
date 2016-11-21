

>在看CSS选择符的时候注意到一个特别有意思的选择符--:target，随后在[http://benschwarz.github.io/gallery-css/](http://benschwarz.github.io/gallery-css/)发现利用该选择符可以做出如此漂亮的gallery，干净利落无需js代码(强迫症及完美主义必备)，网站上有一套讲解教程，但是是付费的。今天把我实现的过程以及某些细节分享出来，供学习使用。

我这里有一个在线预览[点击这里](http://wyuhao.com/demo/targetGallery/)

## html模板

```html
<div class="gallery">
	<div id="gallery-1" class="control-operator"></div>
	<div id="gallery-2" class="control-operator"></div>
	<div id="gallery-3" class="control-operator"></div>
	
	<figure class="item">
	  <img src="./slide1.jpg" />
	</figure>
	
	<figure class="item">
	  <img src="./slide2.jpg" />
	</figure>
	
	<figure class="item">
	  <img src="slide3.jpg" />
	</figure>
	
	<div class="controls">
	  <a href="#gallery-1" class="control-button">•</a>
	  <a href="#gallery-2" class="control-button">•</a>
	  <a href="#gallery-3" class="control-button">•</a>
	</div>
</div>
```

制作画册的图片以及描述信息等将放在figure标签中；用于点击的切换不同slide的按钮以一个圆点的形式放在``<div class="controls"></div>``中，``<a>``标签中以一个圆点作为按钮；div.control-operator并不显示，作控制用，后面会解释。

这里以3张图为例，添加slide只需要将以上的html代码对应扩展就可以了。

## 第一步基本布局设置

首先做一些全局设置，去掉body，figure标签的默认边距:

    body,figure {
        margin: 0;
    }

设置img标签自动放缩:

    img {
        max-width: 100%;
        height: auto;
    }

接下来设置div.gallery及几个相关子元素的定位:

    .gallery {
        position: relative;    
    }

	.gallery > .item {
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		text-align: center;
	}

    /* 设置包含<a>按钮的div块 */
	.gallery > .controls {
		position: absolute;
		bottom: 0;
		width: 100%;
		text-align: center;
	}

    /* <a>按钮 */
	.gallery .control-button {
		display: inline-block;
		margin: 0 .02em;
		font-size: 3em;
		text-align: center;
		text-decoration: none;
	}

	.gallery > .item:first-of-type {
		position: static;
	}

    .gallery > .control-operator {
        display: none;      
    }

设置div.gallery为relative定位是为了让其子元素的absolute位置基于它自己。设置完以上属性，此时应该只能看到一张居中的图片，其余图片按顺序在下层被遮挡。

#### 为什么要设置第一个figure.item定位为static

绝对定位意味着该元素将不复存在于正常的文档流中，如果div.gallery所有的子元素都绝对定位，div.gallery元素将无法被撑开，其高度将为0，设置其第一个figure.item子元素为static元素，意在撑开div.gallery，这样便于div.gallery中其他元素的显示，同时避免div.gallery后面的标签元素和div.gallery重叠。

## 第二步利用:target选择符控制图像的显示

为了让figure.item元素在没有被选中的时候消失，我们将其opacity属性设置为0，同时将第一个figure.item默认显示，修改添加以下代码:

	.gallery > .item {
		opacity: 0;
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		text-align: center;
	}
	
	.gallery > .item:first-of-type {
		position: static;
		opacity: 1;
	}

将除了第一个figure.item的其他figure元素的opacity属性全部设为0，这样进入网页(未经过任何用户操作)时将看到第一幅图片。

#### 精彩的部分

下面是非常经典的部分：当通过.contorl-button按钮将某对应id的``<div class="control-operator"></div>``选择为:target的时候，通过opacity属性设定某一figure.item显示，其余不显示:

	.gallery > .control-operator:target ~ .item {
		opacity: 0;
	}
	
	.gallery > .control-operator:nth-of-type(1):target ~ .item:nth-of-type(1) {
		opacity: 1;
	}
	.gallery > .control-operator:nth-of-type(2):target ~ .item:nth-of-type(2) {
		opacity: 1;
	}
	.gallery > .control-operator:nth-of-type(3):target ~ .item:nth-of-type(3) {
		opacity: 1;
	}

以上代码在某一.control-operator:target被选中时，将除了和自己序号相同的figure.item的opacity属性设为0，相同序号设为1。至此，点击网页中的``<a class="control-button">•</a>``按钮已经可以实现'翻页'效果了！代码十分简单，效果却非常好。

**特别注意，如果你需要扩展为大于3张图片的时候，以上代码也需要对应扩展。**

## 为什么要设置``<div id="" class="control-operator"></div>``

既然是通过:target设置figure.item元素的opacity属性，为什么不干脆让``<a href="" class="control-button">•</a>``直接去控制``<figure>``元素好了，为什么一定要设置一个并不显示的``<div id="" class="control-operator"></div>``标签呢？html模板设置成下面这样子岂不是更简洁？

```html
  <div class="gallery">
    <figure class="item" id="gallery-1">
      <img src="./slide1.jpg" />
    </figure>

    <figure class="item" id="gallery-2">
      <img src="./slide2.jpg" />
    </figure>

    <figure class="item" id="gallery-3">
      <img src="slide3.jpg" />
    </figure>

    <div class="controls">
      <a href="#gallery-1" class="control-button">•</a>
      <a href="#gallery-2" class="control-button">•</a>
      <a href="#gallery-3" class="control-button">•</a>
    </div>
  </div>
```

答案是不可以的，我们知道对于页内链接，如果有个链接指向index.html#gallery-1，在单击该链接后Web浏览器会自动跳转到该标签所在的位置，通常会向上或者向下滚动到该标签上部恰好出现在用户视野中。如果使用上面"简洁"版的代码，将会出现页面跳动的问题，每次在画册翻页的时候浏览器会滚动页面使figure.item上部刚好对齐在浏览器上部，带来不好的用户体验。不显示的``<div id="" class="control-operator"></div>``恰恰是为了解决这个问题而生的，十分巧妙。

## 让画面过渡更加自然

gallery的功能已经实现了，为了让画面过渡更加自然，我们使用css的transition实现精细的过渡效果。

#### 添加``<a>``控制按钮的样式

首先不妨添加一下``<a href="" class="control-button">•</a>``的样式：

	/* a 按钮的样式 */
	.gallery .control-button {
	  color: #ccc;
	  color: rgba(255, 255, 255, 0.4);
	}

	.gallery .control-button:first-of-type {
	  color: #whtie;
	  color: rgba(255, 255, 255, 0.8);
	}
	
	.gallery .control-button:hover {
	  color: white;
	  color: rgba(255, 255, 255, 0.8);
	}

默认设置第一个.control-button的颜色为rgba(255, 255, 255, .8)--被选定时更深的颜色。设置color: #ccc以及color: white是为了兼容不支持CSS3属性rgba的浏览器(ie8及以下)。

接下来在通过``<a href="" class="control-button">•</a>``选定某一:target的时候，将该``<a>``突出显示:

	.gallery .control-operator:target ~ .controls .control-button {
	  color: #ccc;
	  color: rgba(255, 255, 255, 0.4);
	}

    /* ：target情况下的hover */
    .gallery .control-operator:target ~ .contorls .control-button:hover {
      color: white;
      color: rgba(255, 255, 255, 0.8);
    }

	.gallery .control-operator:nth-of-type(1):target ~ .controls .control-button:nth-of-type(1),
	.gallery .control-operator:nth-of-type(2):target ~ .controls .control-button:nth-of-type(2),
	.gallery .control-operator:nth-of-type(3):target ~ .controls .control-button:nth-of-type(3) {
	  color: white;
	  color: rgba(255, 255, 255, 0.8);
	}

**别忘了设置在：target情况下a按钮的hover样式 **

接下来利用transition属性实现渐变的过渡效果，在.gallery .control-button中添加以下代码:

	.gallery .control-button {
		display: inline-block;
		margin: 0 .02em;
		font-size: 3em;
		text-align: center;
		text-decoration: none;
		-webkit-transition: color .1s;
		-o-transition: color .1s;
		transition: color .1s;
	}

#### 添加figure.item过渡效果

同理，在.gallery > .item中添加以下代码:

	.gallery > .item {
		opacity: 0;
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		text-align: center;
		-webkit-transition: opacity .5s;
		-o-transition: opacity .5s;
		transition: opacity .5s;
	}

这样在切换图片的时候会有0.5s的渐变，给人舒服的过渡。

## 其他

CSS3已经支持动画animation属性，你当然可以设置动画让它自动滚动播放，要特别注意动画播放的时机。

代码预览如下:

html 

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Demo</title>
  <link rel="stylesheet" href="./gallery.css">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
</head>
<body>
  <div class="gallery">
    <div id="gallery-1" class="control-operator"></div>
    <div id="gallery-2" class="control-operator"></div>
    <div id="gallery-3" class="control-operator"></div>

    <figure class="item">
      <figcaption>Blue</figcaption>
      <img src="./slide1.jpg" />
    </figure>

    <figure class="item">
      <figcaption>Road</figcaption>
      <img src="./slide2.jpg" />
    </figure>

    <figure class="item">
      <figcaption>Snow</figcaption>
      <img src="slide3.jpg" />
    </figure>

    <div class="controls">
      <a href="#gallery-1" class="control-button">•</a>
      <a href="#gallery-2" class="control-button">•</a>
      <a href="#gallery-3" class="control-button">•</a>
    </div>
  </div>

</body>
</html>
```

css 

```css
body, figure {
	margin: 0;
}

img {
	max-width: 100%;
	height: auto;
}

figcaption {
	display: block;
	font-weight: bold;
	padding: 1em 0;
}

.gallery {
	position: relative;
}

.gallery > .item {
	opacity: 0;
	position: absolute;
	left: 0;
	top: 0;
	width: 100%;
	text-align: center;
	-webkit-transition: opacity .5s;
	-o-transition: opacity .5s;
	transition: opacity .5s;
}

.gallery > .item:first-of-type {
	position: static;
	opacity: 1;
}

.gallery > .controls {
	position: absolute;
	bottom: 0;
	width: 100%;
	text-align: center;
}

.gallery > .control-operator {
	display: none;
}

.gallery .control-button {
	display: inline-block;
	margin: 0 .02em;
	font-size: 3em;
	text-align: center;
	text-decoration: none;
	-webkit-transition: color .1s;
	-o-transition: color .1s;
	transition: color .1s;
}

.gallery > .control-operator:target ~ .item {
	opacity: 0;
}

.gallery > .control-operator:nth-of-type(1):target ~ .item:nth-of-type(1) {
	opacity: 1;
}
.gallery > .control-operator:nth-of-type(2):target ~ .item:nth-of-type(2) {
	opacity: 1;
}
.gallery > .control-operator:nth-of-type(3):target ~ .item:nth-of-type(3) {
	opacity: 1;
}

/* a 按钮的样式 */
.gallery .control-button {
  color: #ccc;
  color: rgba(255, 255, 255, 0.4);
}

.gallery .control-button:first-of-type {
  color: white;
  color: rgba(255, 255, 255, 0.8);
}

.gallery .control-button:hover {
  color: white;
  color: rgba(255, 255, 255, 0.8);
}

.gallery .control-operator:target ~ .controls .control-button {
  color: #ccc;
  color: rgba(255, 255, 255, 0.4);
}

.gallery .control-operator:target ~ .controls .control-button:hover {
  color: white;
  color: rgba(255, 255, 255, .8);
}

/* 被选中时 设置<a>.</a>颜色为 rgba(255,255,255,.8) */
.gallery .control-operator:nth-of-type(1):target ~ .controls .control-button:nth-of-type(1),
.gallery .control-operator:nth-of-type(2):target ~ .controls .control-button:nth-of-type(2),
.gallery .control-operator:nth-of-type(3):target ~ .controls .control-button:nth-of-type(3) {
  color: white;
  color: rgba(255, 255, 255, 0.8);
}
```

** 如果觉得对你有帮助就给一颗star吧~ **






