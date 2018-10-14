---
title: wordpress 实现404个性跳转页面
tags:
  - wordpress
  - 笔记
id: 415
categories:
  - 学习笔记
date: 2012-04-28 09:53:51
---

这个就不多说了，直接贴代码，使用方法就是把这些代码全部拷贝进你的404.php这个文件。后台点击外观-编辑-选择404.php，开始编辑，

完成后然后就可以自己测试下效果了，你也可以先访问[http://leaver.me/test404](http://leaver.me/test404)查看效果
<pre lang="xml" escaped="true">
&lt;title&gt;404&lt;/title&gt;
&lt;style type="text/css"&gt;
body{
    margin:0;
    padding:0;
    font:14px/1.6 Arial,Sans-serif;
    background:#fff url(img/body.png) repeat-x;
}
a:link,a:visited{
   color:#007ab7;
   text-decoration:none;
}
h1{
   position:relative;
   z-index:2;
   width:540px;
   height:0;
   margin:110px auto 15px;
   padding:230px 0 0;
   overflow:hidden;
   xxxxborder:1px solid;
   background-image: url(http://leaverimage.b0.upaiyun.com/20346_o.jpg);
   background-repeat: no-repeat;
}
h2{
   position:absolute;
   top:17px;
   left:187px;
   margin:0;
   font-size:0;
   text-indent:-999px;
   -moz-user-select:none;
   -webkit-user-select:none;
   user-select:none;
   cursor:default;
   width: 534px;
}
h2 em{
   display:block;
   font:italic bold 200px/120px "Times New Roman",Times,Serif;
   text-indent:0;
   letter-spacing:-5px;
   color:rgba(216,226,244,0.3);
}
.link a{margin-right:1em;}
.link,.texts{
   width:540px;
   margin:0 auto 15px;
   color:#505050;
}
.texts{line-height:2;}
.texts dd{margin:0;padding:0 0 0 15px;}
.texts ul{margin:0;padding:0;}
.portal{
   color:#505050;
   text-align:center;
   white-space:nowrap;
   word-spacing:0.45em;
}
.portal a:link,.portal a:visited{
   color:#505050;
   word-spacing:0;
}
.portal a:hover,.portal a:active{color:#007ab7;}
.portal span{
   display:inline-block;
   height:38px;
   line-height:35px;
   background:url(img/portal.png) repeat-x;
}
.portal span span{
   padding:0 0 0 20px;
   background:url(img/portal.png) no-repeat 0 -40px;
}
.portal span span span{padding:0 20px 0 0;background-position:100% -80px;}
.STYLE1 {
   font-family: Arial, Helvetica, sans-serif;
   font-size: 65px;
}
&lt;/style&gt;
&lt;!--[if lte IE 8]&gt;
&lt;style type="text/css"&gt;
h2 em{color:#e4ebf8;}
&lt;/style&gt;
&lt;![endif]--&gt;

&lt;script type="text/javascript"&gt;
var i = 5;
var intervalid;
intervalid = setInterval("fun()", 1000);
function fun() {
  if (i == 0) {
   window.location.href = "&lt;?php bloginfo('url'); ?&gt;";
   clearInterval(intervalid);
}
document.getElementById("secondsDisplay").innerHTML = i;
i--;
}
&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;h1&gt;&lt;/h1&gt;
&lt;h2&gt;&lt;em&gt;&lt;span class="STYLE1"&gt;404 Error&amp;nbsp;&amp;nbsp; &lt;/span&gt;&lt;/em&gt;: 您所查找的页面不存在,可能已被删除或您输错了网址!&lt;/h2&gt;
&lt;p class="link"&gt;
   &lt;a href="/"&gt;&amp;#9666;返回首页&lt;/a&gt;
   &lt;a href="javascript:history.go(-1);"&gt;&amp;#9666;返回上一页&lt;/a&gt;
&lt;/p&gt;
&lt;dl class="texts"&gt;
  &lt;dt&gt;我正在联系火星总部查找您所需要的页面.请返回等待信息..&lt;/dt&gt;
  &lt;dd&gt;
&lt;ul&gt;
  &lt;li&gt;不要返回吗?&lt;/li&gt;
  &lt;li&gt;确定不要返回吗?&lt;/li&gt;
  &lt;li&gt;真的真的确定不要返回吗?&lt;/li&gt;
  &lt;li&gt;系统在 &lt;span id="secondsDisplay" style="font-size:20px;color:red;"&gt;5&lt;/span&gt; 秒钟之后带你返回地球。&lt;/li&gt;
&lt;/ul&gt;
&lt;/dd&gt;
&lt;/dl&gt;
&lt;/body&gt;</pre>