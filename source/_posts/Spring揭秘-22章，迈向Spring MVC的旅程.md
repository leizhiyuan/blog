title: Spring揭秘-22章，迈向Spring MVC的旅程
tags:
  - java
  - spring
id: 3705
categories:
  - 学习笔记
date: 2014-07-13 11:25:39
---

Spring揭秘-22章，迈向Spring MVC的旅程<div>
</div><div>Servlet导致数据访问逻辑和业务处理逻辑和对应的视图渲染逻辑相互混杂。之后，JSP出现，通过将输出渲染以模板的形式抽取到jsp后缀的模板文件中，jsp开始繁盛，同时要注意，Servlet处理web请求的时候，要在web.xml中，注册相应的请求url和具体的Serlet的映射关系。于是，jsp有开始混合着写代码</div><div>
</div><div>于是，我们引入了JavaBean,来封装相关业务逻辑，经过一次升级后，</div><div>
</div><div>![](/images/e6af173baae0c480b8dddc398ce8dd3078b260e4.jpg)
</div><div>这个模型就比较清楚了。但是和MVC还是有点差别</div><div>一个典型的mvc模式应该是这样的</div><div>![](/images/4bcce41a42b7cc021f4b726da1c3533cc5371a21.jpg)
</div><div>
</div><div>控制器处理请求，模型封装逻辑和状态，视图给用户，</div><div>
</div><div>Structs以请求/响应框架为基础。</div><div>
</div><div>Spring MVC属于请求渠道的WEb框架。框架引入Front Controller做分发之后，就更加好管理了。</div><div>
</div><div>![](/images/131d593fd9a36cfc8180a4b86495208a356ae563.jpg)
</div>

<div>[来自为知笔记(Wiz)](http://www.wiz.cn/i/e0140d75 "来自为知笔记(Wiz)")</div>