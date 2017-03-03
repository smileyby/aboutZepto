Zepto 使用中的一些注意点
======================

#### 从哪里下载Zepto

> 这个问题看起来很蠢，从官网下载不就行了嘛！可是你有没有发现下载链接上面邮航小字呢？
> 
> There are more modules; a list of all modules is available in the README.
> 
> 在这个[README](https://github.com/madrobby/zepto#readme)里面你会惊奇的发现，Zepto源码中有14个模块，而官网提供的标准版里面只有7个模块！而且居然不包含对移动端开发非常重要的touch模块（提供对触摸时间的支持）！所以我的建议是，不要葱官网下载，而是从Github下载了源码之后自己Build一个版本，这样你可以自行挑选适合的模块，比我我挑选的模块是这几个：
> * polyfill, zepto,detct,event,ajax,form,fx这7个就是标准版包含的模块
> * fx_methods 有了这个模块之后，.show().hide()等几个方法才能支持动画，比如.show('fast')
> * data提供对 .data()方法的完整支持，像jQuery一样的内存对象存储
> * assets 移除img元素后做一些特殊处理，用来清理内存
> * selector 更多的选择器的支持，后面会提到
> * touch 对触摸时间的支持，比如tap时间

#### 不要用click事件，用tap代替

> 这个估计已经广为人知，因为click事件有200-300ms的延迟，为了更快的相应，最好用Zepto提供的tap事件
>
> 不相信的话，可以用下面的代码测试一下

```js
	
	var t1,t2;
	$('#id').tap(function () {
	    t1 = Date.now();
	});
	$('#id').click(function () {
	    t2 = Date.now();
	    alert(t2 - t1);
	});

``` 

#### Zepto对css选择器的支持

> 郑重提醒， :text :checkbox :first 等等再jQuery里面很常用的选择器，Zepto不支持！
> 
> 原因很简单，jQuery通过自己编写的sizzle引擎来支持CSS选择器，而Zepto是直接通过浏览器提供的document.querySelectorAll 接口
> 
> 这个接口也只支持标准的CSS选择器，而上面提到的那些属于jQuery选择器扩展，所以仔细看看这个网页，注意一下这些选择器。
> 
> 当然也有好详细，就是上面的selector模块，如果有这个模块，能够支持部分的Jquery选择器扩展列举如下：
> * :visible :hidden
> * :selected :checked
> * :parent
> * :first :last :eq
> * :contain :has

#### 元素的尺寸计算

> 首先Zepto没有 .innerHeight() .outerWidth()等四个方法，其次，他的 .height() .width()方法也不完善，对于display: none; 的元素，计算出的高宽都是0
> 
> 而这在jQuery中是没有问题的，一次jQuery针对这种元素，会先设置其css样式为 position:absolute,visibility: hidden, display: block
> 
> 计算完高宽之后再回复，参见[ https://github.com/jquery/jquery/blob/master/src/css.js#L460]( https://github.com/jquery/jquery/blob/master/src/css.js#L460) 如果遇到这种情况，可以参考jQuery写一个类似的方法

####　.show()的动画效果

> 如果没有fx_methods模块的话，.show()方法不支持动画的，不过有了这个模块的话，动画的支持还是有点小问题，比如：

```html

	<div style="background:black;opacity:0.7;display:none">
	    test
	</div>

```

> 如果你调用$('div').show('fast'),那么动画完成后你看到的不会是一个半透明的元素，而是全黑不透明的因为Zepto的.show()动画实现的很简单，没有高度的变化，而是将透明度从0逐渐变成1，所以元素上原来设置的透明度就被替代了。这种情况下，可用.fadeIn()方法来替代show()

#### 结语

> 看到这里相信你已经了解为什么我说Zepto远未成熟，目前他其实还仅仅处于能用，远未达到jQuery好用的地步。