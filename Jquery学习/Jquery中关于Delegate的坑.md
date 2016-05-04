# Jquery中关于Delegate的坑
[toc]
---

之前一直都是看别人的技术博客，学习到了很多的东西。今天突然有点突发奇想，开始自己写点东西，一方面是帮助自己总结下所遇到的问题，另一方面也是为了帮助由需要的人。——写在前面的话

---

最近在工作学习中对jQuery这个库使用的比较多，在使用过程中也遇到过不少的问题，其中有一个就是关于jQuery中Delegate的用法.

先看一个demo

```html
<div class="wrap">
    <div class="foobar">
    foobar
    </div>
</div>
<button class="button"> control</button>
```


```javascript
var button = $(".button");
var foobar  = $(".foobar");
var htmltext = '<div class="foobar"> foobar</div>';
button.click(function() {
	$(".wrap").append(htmltext);
});
foobar.click(function(){
	alert("foobar");
});

```
代码实现的功能很简单，而且也很常用。按钮点击后能生成foobar元素，foobar元素也有一个简单的alert事件。

但是在运行过程中，我们发现，新生成的foorbar，并不能触发点击事件。只有最初始的第一个foorbar能够触发出alert。

这是因为在页面渲染完成之后，jQuery就为我们的class="foobar"的元素添加了点击的事件，后面通过按钮点击而生成的foobar却无法获得点击事件。这也就是为什么我们需要使用delegate的原因了，**为这些新生成的dom元素来添加事件**。

接下来我们修改一下代码

```javascript
var button = $(".button");
var foobar  = $(".foobar");
var htmltext = '<div class="foobar"> foobar</div>';
button.click(function() {
	$(".wrap").append(htmltext);
});

$(document).delegate(foobar,"click",function(){
    alert("foobar");
});

```
我们在$(document)中为foobar元素绑定了click事件，但是问题来了。程序并没有向我们想象中的方向去执行。在我们无论点击页面任何位置时，alert("foobar")都执行了一次。

这当然不是我们想要的结果。

```javascript
$(document).delegate(foobar,"click",function(){
    console.log($(this));
    alert("foobar");
});
```
我们在alert前插入一行console.log($(this))来打印下我们们点击事件所响应的元素。结果发现输出的确实document这个元素，而不是我们预期的foobar元素。这也是为什么无论我们点击什么地方，总会执行alert语句。

很快，我们就发现了问题的原因。

$(document).delegate(**foobar**,"click",function......

这里的foobar是使用了缓存的。而我们的缓存元素的代码是在页面初始化的时候执行了一次，也就是说，后面所有新生成元素，是无法绑定到事件的，从而将整个click的事件绑定到了document上面了。

既然找到了问题，那么就很好解决了。只要将foobar替换成$(".foobar")就可以了。

```javascript
$(document).delegate(foobar,"click",function(){
    console.log($(this)index());
    alert("foobar");
});
```
> 具体点击的哪个元素，也能通过索引正确的打印出来了

大家都喜欢用新建变量来缓存一个jquery元素，在绝大多数情况下，确实能提高代码的效率，但是也有可能出现不知名的的错误，所以说，写代码不是一根筋写到底的事情，还是要多思考下，哪些地方能用哪些方法，哪些地方不能用，不然掉坑里了都不觉得。




