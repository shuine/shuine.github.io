---
layout: post
title: history详解及实例应用
categories: 前端
description: history知识介绍，在H5中前进后退应用，同时用一个详细的代码实例加以说明
keywords: history, state, 异步上传
---

DOM中的window对象通过window.history方法提供了对浏览器历史记录的读取，让你可以在用户的访问记录中前进和后退。

## 一，history简介

History 对象包含用户（在浏览器窗口中）访问过的 URL。

History 对象是 window 对象的一部分，可通过 window.history 属性对其进行访问。

注意： 没有应用于History对象的公开标准，不过所有浏览器都支持该对象。

### History 对象属性

| 属性     | 说明                                       |
| ------ | ---------------------------------------- |
| length | 返回历史列表中的网址数，通过检查浏览器历史记录的length属性来找到历史记录堆栈中的页面总数 |

### History 对象方法

使用back(),forward(),和go()方法可以在用户的历史记录中前进、后退、移动到历史记录中特定的位置

| 方法        | 说明                                       |
| --------- | ---------------------------------------- |
| back()    | 加载 history 列表中的前一个 URL，像用户点击了浏览器工具栏上的返回键一样 |
| forward() | 加载 history 列表中的下一个 URL                   |
| go()      | 加载 history 列表中的某个具体页面，使用go()方法从session历史中载入特定的页面，注意:IE支持向go()方法传URL参数 |

### History 添加修改实体方法

HTML5引入history.pushState()和history.replaceState()方法，添加和修改history实体。同时，这些方法会和window.onpostate事件一起工作。

| 方法              | 说明                                       |
| --------------- | ---------------------------------------- |
| pushState()     | 向 history 添加当前页面的记录                      |
| [replaceState() | history.replaceState() 用起来很像pushState()，除了replaceState()是用来修改当前的history实体而不是创建一个新的。这个方法有时会很有用，当 你需要对某些用户行为作反应而更新一个state对象或者当前history实体时，可以使用它来更新state对象或者当前history实体的url。 |

pushState()有三个参数:state对象，标题(现在是被忽略，未作处理)，URL(可选)。具体细节：

·        state对象 –state对象是一个JavaScript对象，它关系到由pushState()方法创建出来的新的history实体。用以存储关于你所要插入到历史 记录的条目的相关信息。State对象可以是任何Json字符串。因为firefox会使用用户的硬盘来存取state对象，这个对象的最大存储空间为640k。如果大于这个数 值，则pushState()方法会抛出一个异常。如果确实需要更多的空间来存储，请使用本地存储。

·        title—firefox现在回忽略这个参数，虽然它可能将来会被使用上。而现在最安全的使用方式是传一个空字符串，以防止将来的修改。或者可以传一个简短的标题来表示state

·        URL—这个参数用来传递新的history实体的URL，注意浏览器将不会在调用pushState()方法后加载这个URL。但也许会过一会尝试加载这个URL。比如在用户重启了浏览器后，新的url可以不是绝对路径。如果是相对路径，那么它会相对于现有的url。新的url必须和现有的url同域，否则pushState()将抛出异常。这个参数是选填的，如果为空，则会被置为document当前的url。

某种意义上来说，调用pushState()方法很像设置了window.location = “#foo”,这两者都会创建和激活另一个关联到当前document的history实体，但pushState()另外有一些优点：

- 新的url可以是任何和当前url同域的url，相比之下，如果只设置hash，window.location会保持在同一个document。
- 如果不需要，你可以不修改url。对比而言，设置window.location = “#foo”;仅产生新的history实体，如果你当前的hash不是#foo
- 你可以将任意的数据与你的新history实体关联。使用基于hash的方法，需要将所有相关的数据编码为一个短字符串。

注意，pushState()方法不会使hashchange时间发生，即使是新旧url只是hash不同。例子：

假设http://***/foo.html页面执行了一下JS

```javascript
var stateObj = { foo: "bar" }; 
history.pushState(stateObj, "page 2", "bar.html");
```

这种方法将会使url地址栏显示http://***/bar.html，但浏览器不会加载bar.html页面，即使这个页面存在也不会加载。

### History 事件

当history实体被改变时，popstate事件将会发生。如果history实体是有pushState和replaceState方法产生的，popstate事件的state属性会包含一份来自history实体的state对象的拷贝

| 方法       | 说明                   |
| -------- | -------------------- |
| popstate | 向详见window.onpopstate |

## 二，实例及问题

描述：A页面基本信息页，包含上传按钮点击进入B页面，B页面上传操作成功会回退到A页面，需要考虑问题，1：iframe实现图片异步上传，具体参见代码，2：使用history.pushState()和popstate事件实现AJAX的前进、后退功能，3：window内iframe使用state各浏览器兼容问题。

main.html：

```html
<html>
<head>
<title></title>
</head>
<body>
<!-- A页面-->
<article node-type="basic_block">
    <a href="javascript:void(0);" node-type="pic_a" action-type="pic_a">选择图片</a>
</article>
<!-- B页面-->
<article node-type="upload_block"  class="hid">
    <span>上传图片</span>
    <form id="pic_form" node-type="pic_form" target="pic_iframe" action="http://picupload.php?" method="POST">
        <input type="file"  node-type="pic_input">
    </form>
    <iframe frameborder="0"   class="hid"  id="pic_iframe" name="pic_iframe" src="about:blank"></iframe>
</article>
<script type="application/javascript" src="main.js"></script>
</body>
</html>
```

picupload.php:

```php
//处理上传，获得上传后的img_path地址，省略
$this->display('picupload.html', array('img_path' => $img_path));
```

```html
<html>
<head>
<title></title>  
<script type="text/javascript"> 
if(window.parent){
	window.parent.addImgSuccess(img_path);
}
</script>
</head>
<body>
</body>
</html>
```

main.js：

```javascript
var org_index = 0;
//"返回"触发事件,settimeout防止有些浏览器首次加载时触犯popstate
window.addEventListener('load', function() {
     setTimeout(function() {
          window.addEventListener('popstate', function() {
            $('article').addClass('hid');
           	$('[node-type="basic_block"]').removeClass('hid');
           });
      }, 0);
});
$('[node-type="pic_a"]').tap(function(){ 
$('[node-type="basic_block"]').addClass('hid');
        $('[node-type="upload_block"]').removeClass('hid');
        var state = history.state;
        history.pushState(state, null, window.location+'#upload');
        org_index = history.length;
 });
$('[node-type="pic_input"]').change(function(){ addPic(); });
//上传控件执行触发form提交
addPic : function() {
    $("#pic_form").submit();
},
//上传头像成功
addImgSuccess : function(img_path, data) {
    //兼容不同浏览器，一些浏览器加载window对象时会push新的state,history.length会增加
    var go_index = org_index - history.length - 1;
    history.go(go_index);
},
```

页面原型：

![页面原型](/images/posts/前端/history-demo.png)

## 三，问题分析

### 1，iframe实现图片异步上传

原理：将图片上传的页面放在iframe中，这样就可以在iframe中将图片提交到服务器而不需要页面刷新，提交成功后用脚本实现回到主页面并显示上传的图片。

### 2，页面初次加载state对象各浏览器兼容问题

当页面加载时，它可能会有一个非空的state对象，这可能发生在当页面设置一个state对象(使用pushState或者replaceState)之后用户重启了浏览器。当页面重新加载，页面将收到onload事件，但不会有popstate事件。然而，如果你读取history.state属性，将在popstate事件发生后得到这个state对象，所以在"返回"触发事件，使用settimeout防止有些浏览器首次加载时触犯popstate

### 3，window内iframe使用state各浏览器兼容问题

不同浏览器对iframe中history操作不一样，如Firefox执行history.back/go(-1)是iframe内后退，而Chrome是父页面后退，我们这里是需要实现父页面回退，根据state变化动态获取需要回退的步数：

```javascript
var go_index = org_index - history.length - 1;
history.go(go_index);
```

页可以根据不同的浏览器判断，不过需要对浏览器一一了解，不推荐，如下：

```javascript
var explorer =navigator.userAgent;
var str = JSON.stringify(window.location);
//ie 
if (explorer.indexOf("MSIE") >= 0) {
   history.go(-1);
}
//firefox 
else if (explorer.indexOf("Firefox") >= 0) {
    history.go(-2);
}
//Chrome
else if (explorer.indexOf("Chrome") >= 0) {
}
//Opera
else if (explorer.indexOf("Opera") >= 0) {}
//Safari
else if (explorer.indexOf("Safari") >= 0) {} 
//Netscape
else if (explorer.indexOf("Netscape")>= 0) {} 
```