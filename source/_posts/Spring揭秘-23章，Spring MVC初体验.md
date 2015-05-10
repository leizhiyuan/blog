title: Spring揭秘-23章，Spring MVC初体验
tags:
  - java
  - spring
id: 3710
categories:
  - 学习笔记
date: 2014-07-13 11:26:24
---

&nbsp;鸟瞰Spring MVC<div>与其他请求驱动的Web框架思路类似。org.springframework.web.servlet.DispatcherServlet就是Spring mvC中的Front Controller。负责处理请求，但是不针对具体的处理逻辑。而是委派给下一级的控制器，也就是<span style="font-size: 10.5pt; line-height: 1.5;">org.springframework.web.servlet.mvc.Controller</span><span style="font-size: 10.5pt; line-height: 1.5;">去执行。</span></div><div><span style="font-size: 10.5pt; line-height: 1.5;">
</span></div><div>DispatcherServlet的处理流程如下：</div><div>1.HandlerMapping</div><div>DispathcherServlet是FrontController，所以他服务于一组Web请求，需要在web.xml中配置。</div><div>DispathcherServlet需要自己处理请求和处理之前的对应关系，比如根据参数对应到不同的Controller上。为了更加方便的处理映射的匹配，引入<span style="font-size: 10.5pt; line-height: 1.5;">HandlerMapping用来获取需要处理请求的对应的Controller类。</span>
</div><div><span style="font-size: 10.5pt; line-height: 1.5;">2.Controller</span></div><div>对应于<span style="font-size: 10.5pt; line-height: 1.5;">DispathcherServlet的次级控制器，本身实现了对应某个具体Web请求的处理逻辑，当</span><span style="font-size: 10.5pt; line-height: 1.5;">HandlerMapping查到了Controller之后，</span><span style="font-size: 10.5pt; line-height: 1.5;">DispathcherServlet获得了</span><span style="font-size: 10.5pt; line-height: 1.5;">HandlerMapping的返回结果。然后调用Controller处理请求，处理完成后，返回一个ModelAndView实例，里面包含两部分内容</span></div><div>视图的逻辑名称，<span style="font-size: 10.5pt; line-height: 1.5;">DispathcherServlet根据名称决定显示哪个视图</span></div><div>模型数据，渲染过程中需要将这些模型数据并入视图的显示中</div><div>
</div><div>ViewResolver和View</div><div>我们已经来到了最后一步，要转成最终的JSP视图文件。由于模板引擎很多，Spring提供了一套基于ViewResolver和View的抽象层。</div><div>
<<<<<<< HEAD
</div><div>![]({{BASE_PATH}}/images/fc45dedeba68b31c39383776fc7a7a35eba30a5a.jpg)
=======
</div><div>![](/images/fc45dedeba68b31c39383776fc7a7a35eba30a5a.jpg)
>>>>>>> 换电脑之后重新备份
</div>

<div>[来自为知笔记(Wiz)](http://www.wiz.cn/i/e0140d75 "来自为知笔记(Wiz)")</div>