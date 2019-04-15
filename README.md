## 移动端ios下$('body')点击无效的解决方法

今天遇到一个坑，嵌入到游戏中的h5页面出现按钮点击失效问题

正常是这样的

```
$('body').click(function(e) {
   var target = $(e.target);
   if (!target.is('#more') && !target.is('.btns *')) {
       $('.btns').removeClass('open');
   $('#more').show();
   }
});
```

但是你会发现正常的pc和安卓显示中可以实现，但是在ios真机中显示有问题，因为IOS浏览器的window、document、body并不接受click事件，按钮和链接才接受click事件;

度娘了一下有人说给body加cursor：pointer;然并卵;

后面实践发现下面的写法会解决

```
$('body>*').bind('click', function(e) {
...
});
```

或者

```
$('btn').bind('click',function(e){ //点击按钮显示
    $('#more').show();
    $('body').one('click',function(){ //这里给body执行一次点击后删除事件
        $('#more').hide();
    })
    e.stopPropagation(); //这里阻止一下冒泡，因为按钮也在body里面，不让body检测到点击按钮
})
```

这样应该挺好懂吧，可以试试


## 解决：初步解决方案是给body或者html添加`overflow：hidden`样式，当然height要设置成100%

```
$("#showProup").click(function() {
      $("#mask").show(0,function(){
           $("body").css('overflow','hidden');
      });
  })
  $("#close").click(function() {
      $("#mask").hide(0,function(){
          $("body").css('overflow','scroll');
      });
  })
}
```

移动端问题比较明显，所以可以`touchmove`代替

```
$(document).on("touchmove",function(e) {
  e.preventDefault(); 
})
```

这种方法在移动端是可以解决滑动的问题，但有种情景就显得有点尴尬了，移动端屏幕本来就小，如果弹窗的内容过多也需要滑动（比如很长的活动规则），因为弹窗出现的时候已经禁止了滑动事件，此时禁止滑动之前先做一下判断---

```
$(document).on("touchmove",function(e) {
   if(e.target.className.indexOf("shadeBox") >= 0) {
        e.preventDefault();      
    } 
})
```

以上为想到的初步方案，如有更好可替换

解决：用户点击一个链接，会出现一个边框或者半透明灰色遮罩, 不同生产商定义出来额效果不一样，可设置-webkit-tap-highlight-color的alpha值为0去除部分机器自带的效果

```
a,button,input,textarea{
  -webkit-tap-highlight-color: rgba(0,0,0,0;)
  -webkit-user-modify:read-write-plaintext-only;
}
```
