title: Spring揭秘-第四章BeanFactory笔记
tags:
  - java
  - spring
id: 3695
categories:
  - 学习笔记
date: 2014-07-13 11:20:09
---

<div></div>
<div>之前说了，IoC容器就是一个IoC Service Provider，但是容器是个啥意思？![]({{BASE_PATH}}/images/d4d1615a6c1a06895088631c37e3fca7692f0b8a.png)</div>
<div>可以看到IoC容器提供了更多的内容，</div>
<div>Spring的IoC容器又分成两种，</div>
<div>1.BeanFactory</div>
<div>。基础类型IoC容器，提供完整的IoC服务支持。如果没有特殊指定，默认采用延 迟初始化策略（lazy-load）。只有当客户端对象需要访问容器中的某个受管对象的时候，才对 该受管对象进行初始化以及依赖注入操作。所以，相对来说，容器启动初期速度较快，所需 要的资源有限。对于资源有限，并且功能要求不是很严格的场景，BeanFactory是比较合适的 IoC容器选择。</div>
<div></div>
<div>2.ApplicationContext。ApplicationContext在BeanFactory的基础上构建，是相对比较高 级的容器实现，除了拥有BeanFactory的所有支持，ApplicationContext还提供了其他高级<span style="font-size: 10.5pt; line-height: 1.5;">特性，比如事件发布、国际化信息支持等，这些会在后面详述。ApplicationContext所管理 的对象，在该类型容器启动之后，默认全部初始化并绑定完成。所以，相对于BeanFactory来 说，ApplicationContext要求更多的系统资源，同时，因为在启动时就完成所有初始化，容 器启动时间较之BeanFactory也会长一些。在那些系统资源充足，并且要求更多功能的场景中， ApplicationContext类型的容器是比较合适的选择。 </span></div>
<div> 通过 图4-2，我们可以对BeanFactory和ApplicationContext之间的关系有一个更清晰的认识。</div>
<div></div>
<div></div>
<div></div>
<div></div>
<div></div>
<div>![]({{BASE_PATH}}/images/f531ea3e11bb391453eb5f763283398c938634b2.png)</div>
<div></div>
<div></div>
<div></div>
<div></div>
<div>Bean工长，就是生成Bean的嘛，每个业务对象被看成Javabean,我们与工厂打交道就简单得多，我们只要告诉他我要什么对象，至于怎么组装，那是他的事情。</div>
<div></div>
<div></div>
<div>工厂提供了一些对外的接口，比如获取Bean，渠道Bean的状态等等。</div>
<div>以xml为例</div>
<div></div>
<div>
<div>
<pre class="lang:default decode:true ">&lt;?xml version="1.0" encoding="UTF-8"?&gt; 
&lt;!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN//EN" "http://www.springframework.org/dtd/spring-beans.dtd"&gt;  
&lt;beans&gt;  
 &lt;bean id="djNewsProvider" class="..FXNewsProvider"&gt;   
    &lt;constructor-arg index="0"&gt;    
       &lt;ref bean="djNewsListener"/&gt;   
     &lt;/constructor-arg&gt;   
   &lt;constructor-arg index="1"&gt;    
       &lt;ref bean="djNewsPersister"/&gt;   
   &lt;/constructor-arg&gt;  
 &lt;/bean&gt;    
 &lt;bean id="djNewsListener" class="..impl.DowJonesNewsListener"&gt;  
 &lt;/bean&gt;  
 &lt;bean id="djNewsPersister" class="..impl.DowJonesNewsPersister"&gt;  
 &lt;/bean&gt; 
&lt;/beans&gt;</pre>
</div>
</div>
<div>然后</div>
<pre class="lang:default decode:true">public static void main(String[] args)  {
   DefaultListableBeanFactory beanRegistry = new DefaultListableBeanFactory();     
   BeanFactory container = (BeanFactory)bindViaXMLFile(beanRegistry);  
   FXNewsProvider newsProvider = (FXNewsProvider)container.getBean("djNewsProvider"); 
   newsProvider.getAndPersistNews(); 
}  
public static BeanFactory bindViaXMLFile(BeanDefinitionRegistry registry)  
{  
XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(registry);  
reader.loadBeanDefinitions("classpath:../news-config.xml");   
return (BeanFactory)registry;  // 或者直接  
//return new XmlBeanFactory(new ClassPathResource("../news-config.xml"));
 }</pre>
<div></div>
<div>人生好像就美好</div>