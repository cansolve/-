## 移动端ios下$('body')点击无效的解决方法

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
