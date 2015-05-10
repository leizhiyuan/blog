title: 《Spring揭秘》读书笔记-第二章IoC的基本概念
tags:
  - java
  - spring
  - 开发
id: 3686
categories:
  - 学习笔记
date: 2014-07-01 18:54:17
---

<span style="font-family: 宋体; font-size: 10pt;">理念就是让别人为你服务，中文名控制反转，也叫依赖注入DI</span>

<pre class="lang:default decode:true">public class FXNewsProvider

{

  private IFXNewsListener newsListener;

  private IFXNewsPersister newPersistener;

  public void getAndPersistNews()

{

String[] newsIds = newsListener.getAvailableNewsIds();

if(ArrayUtils.isEmpty(newsIds))

{
   return;
} 

for(String newsId : newsIds)

{ FXNewsBean newsBean = newsListener.getNewsByPK(newsId); 

newPersistener.persistNews(newsBean);

newsListener.postProcessIfNecessary(newsId);

}

}

}</pre>

 <span style="font-family: 宋体; font-size: 10pt;">假设这个类用来处理新闻，</span>

<span style="font-family: Courier; font-size: 8pt;">IFXNewsListener </span><span style="font-family: 宋体; font-size: 8pt;">用来获取新闻，</span>

<span style="font-family: Courier; font-size: 8pt;">IFXNewsPersister  </span> <span style="font-family: 宋体; font-size: 8pt;">用来把获取的新闻持久化</span>

<span style="font-family: 宋体; font-size: 10pt;">当我们需要获取不同的新闻源，比如道琼斯的新闻时，我们会写一个</span> <span style="font-family: Courier; font-size: 8pt;">DowJonesNewsListener</span> <span style="font-family: 宋体; font-size: 8pt;">类和</span> <span style="font-family: Courier; font-size: 8pt;">DowJonesNewsPersister</span> <span style="font-family: 宋体; font-size: 8pt;">类，然后实例化</span>

<pre class="lang:default decode:true">public FXNewsProvider()

{

  newsListener = new DowJonesNewsListener();

  newPersistener = new DowJonesNewsPersister();

}</pre>
&nbsp;

<span style="font-family: SimSun; font-size: 9pt;">如果我们依赖于某个类或服务，最简单而有效的方式就是直接</span>

<span style="font-family: SimSun; font-size: 9pt;">在类的构造函数中新建相应的依赖类。</span>

<span style="font-family: 宋体; font-size: 10pt;">注意看，</span> <span style="font-family: SimSun; font-size: 9pt;">，我们都是自己主动地去获</span>

<span style="font-family: SimSun; font-size: 9pt;">取依赖的对象！</span>

<span style="font-family: SimSun; font-size: 9pt;">可是回头想想，我们自己每次用到什么依赖对象都要主动地去获取，这是否真的必要？我们最终</span>

<span style="font-family: SimSun; font-size: 9pt;">所要做的，其实就是直接调用依赖对象所提供的某项服务而已</span>

<span style="font-family: SimSun; font-size: 9pt;">能不能我们用的时候自动送过来呢？</span>

<span style="font-family: SimSun; font-size: 9pt;">。现在是用什么，让别人直接送过来就成。所以，简单点儿说，</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC</span> <span style="font-family: SimSun; font-size: 9pt;">的理念就是，</span>

<span style="font-family: SimSun; font-size: 9pt;">让别人为你服务！在图</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">2-1</span> <span style="font-family: SimSun; font-size: 9pt;">中，也就是让</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC Service Provider</span> <span style="font-family: SimSun; font-size: 9pt;">来为你服务！</span>

<<<<<<< HEAD
![]({{BASE_PATH}}/images/45ddcfa8929fe85603c18852a59afac715fa4c7f.bmp)
=======
![](/images/45ddcfa8929fe85603c18852a59afac715fa4c7f.bmp)
>>>>>>> 换电脑之后重新备份

<span style="font-family: 宋体; font-size: 10pt;">这里，被注入对象就是</span> <span style="font-family: Courier; font-size: 8pt;">FXNewsProvider</span> <span style="font-family: 宋体; font-size: 8pt;">，被依赖于对象</span> <span style="font-family: Courier; font-size: 8pt;">IFXNewsListener</span> <span style="font-family: 宋体; font-size: 8pt;">和</span> <span style="font-family: Courier; font-size: 8pt;">IFXNewsPersister  </span>

<span style="font-family: SimSun; font-size: 9pt;">通常情况下，被注入对象会直接依赖于被依赖对象。但是，在</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC</span> <span style="font-family: SimSun; font-size: 9pt;">的场景中，二者之间通过</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC Service</span>

<span style="font-family: 'Times New Roman'; font-size: 9pt;">Provider</span> <span style="font-family: SimSun; font-size: 9pt;">来打交道，所有的被注入对象和依赖对象现在由</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC Service Provider</span> <span style="font-family: SimSun; font-size: 9pt;">统一管理。被注入对象需要</span>

<span style="font-family: SimSun; font-size: 9pt;">什么，直接跟</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC Service Provider</span> <span style="font-family: SimSun; font-size: 9pt;">招呼一声，后者就会把相应的被依赖对象注入到被注入对象中，从而</span>

<span style="font-family: SimSun; font-size: 9pt;">达到</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC Service Provider</span> <span style="font-family: SimSun; font-size: 9pt;">为被注入对象服务的目的。</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC Service Provider</span> <span style="font-family: SimSun; font-size: 9pt;">在这里就是通常的</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC</span> <span style="font-family: SimSun; font-size: 9pt;">容器所充</span>

<span style="font-family: SimSun; font-size: 9pt;">当的角色。从被注入对象的角度看，与之前直接寻求依赖对象相比，依赖对象的取得方式发生了反转，</span>

<span style="font-family: SimSun; font-size: 9pt;">控制也从被注入对象转到了</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC Service Provider</span> <span style="font-family: SimSun; font-size: 9pt;">那里</span>

<span style="font-family: SimSun; font-size: 9pt;">这里我们想知道的是？怎么跟</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC Service Provider</span> <span style="font-family: SimSun; font-size: 9pt;">打招呼</span>

<span style="font-family: SimSun; font-size: 9pt;">在</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC</span> <span style="font-family: SimSun; font-size: 9pt;">模式中，被注入对象又是通过哪些方式来通知</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC Service Provider</span> <span style="font-family: SimSun; font-size: 9pt;">为其提供适当服务的呢？三种打招呼的方式</span>

<span style="font-family: SimSun; font-size: 9pt;">，即构造方法注入（</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">constructor</span>

<span style="font-family: 'Times New Roman'; font-size: 9pt;">injection</span> <span style="font-family: SimSun; font-size: 9pt;">）、</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">setter</span> <span style="font-family: SimSun; font-size: 9pt;">方法注入（</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">setter injection</span> <span style="font-family: SimSun; font-size: 9pt;">）以及接口注入（</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">interface injection</span> <span style="font-family: SimSun; font-size: 9pt;">）。</span>

<span style="font-family: SimSun; font-size: 9pt;">构造方法注入：</span>

<pre class="lang:default decode:true">public FXNewsProvider(IFXNewsListener newsListner,IFXNewsPersister newsPersister) 

{

   this.newsListener = newsListner;

   this.newPersistener = newsPersister;
 }</pre>
&nbsp;

&nbsp;

<span style="font-family: 'Times New Roman'; font-size: 9pt;">IoC Service Provider</span> <span style="font-family: SimSun; font-size: 9pt;">会检查被注入对象的构造方法，取得它所需要的依赖对象列表，进而为其注</span>

<span style="font-family: SimSun; font-size: 9pt;">入相应的对象。</span>

<span style="font-family: SimSun; font-size: 9pt;">setter方法注入</span>

<pre class="lang:default decode:true">public class FXNewsProvider

{

  private IFXNewsListener newsListener;

  private IFXNewsPersister newPersistener;

  public IFXNewsListener getNewsListener() {

  return newsListener;

}

public void setNewsListener(IFXNewsListener newsListener) {

  this.newsListener = newsListener;

}

public IFXNewsPersister getNewPersistener() {

  return newPersistener;

}

public void setNewPersistener(IFXNewsPersister newPersistener) {

  this.newPersistener = newPersistener;

}

}</pre>
&nbsp;

&nbsp;

<span style="font-family: SimSun; font-size: 9pt;">外界就可以通过调用</span> <span style="font-family: Courier; font-size: 9pt;">setNewsListener</span> <span style="font-family: SimSun; font-size: 9pt;">和</span> <span style="font-family: Courier; font-size: 9pt;">setNewPersistener</span> <span style="font-family: SimSun; font-size: 9pt;">方法为</span> <span style="font-family: Courier; font-size: 9pt;">FXNewsProvider</span> <span style="font-family: SimSun; font-size: 9pt;">对</span>

<span style="font-family: SimSun; font-size: 9pt;">象注入所依赖的对象了。</span>

<span style="font-family: 宋体; font-size: 10pt;">接口注入，这个基本不用了。</span>

<span style="font-family: SimHei; font-size: 9pt;">接口注入</span> <span style="font-family: SimSun; font-size: 9pt;">。从注入方式的使用上来说，接口注入是现在不甚提倡的一种方式，基本处于"退</span>

<span style="font-family: SimSun; font-size: 9pt;">役状态"。因为它强制被注入对象实现不必要的接口，带有侵入性。而构造方法注入和</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">setter</span>

<span style="font-family: SimSun; font-size: 9pt;">方法注入则不需要如此。</span>

<span style="font-family: Arial; font-size: 14pt; font-weight: bold; color: #ffffff;">8</span>

<span style="font-family: Wingdings; font-size: 7pt;">q</span> <span style="font-family: SimHei; font-size: 9pt;">构造方法注入。</span> <span style="font-family: SimSun; font-size: 9pt;">这种注入方式的优点就是，对象在构造完成之后，即已进入就绪状态，可以 </span><span style="font-family: Arial; font-size: 14pt; font-weight: bold; color: #ffffff;">9</span>

<span style="font-family: SimSun; font-size: 9pt;">马上使用。缺点就是，当依赖对象比较多的时候，构造方法的参数列表会比较长。而通过反</span>

<span style="font-family: SimSun; font-size: 9pt;">射构造对象的时候，对相同类型的参数的处理会比较困难，维护和使用上也比较麻烦。而且</span>

<span style="font-family: SimSun; font-size: 9pt;">在</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">Java</span> <span style="font-family: SimSun; font-size: 9pt;">中，构造方法无法被继承，无法设置默认值。对于非必须的依赖处理，可能需要引入多</span>

<span style="font-family: SimSun; font-size: 9pt;">个构造方法，而参数数量的变动可能造成维护上的不便。</span>

<span style="font-family: Arial; font-size: 14pt; font-weight: bold; color: #ffffff;">10</span>

<span style="font-family: Wingdings; font-size: 7pt;">q</span> <span style="font-family: Arial; font-size: 9pt;">setter</span> <span style="font-family: SimHei; font-size: 9pt;">方法注入</span> <span style="font-family: SimSun; font-size: 9pt;">。因为方法可以命名，所以</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">setter</span> <span style="font-family: SimSun; font-size: 9pt;">方法注入在描述性上要比构造方法注入好一些。 </span><span style="font-family: Arial; font-size: 14pt; font-weight: bold; color: #ffffff;">11</span>

<span style="font-family: SimSun; font-size: 9pt;">另外，</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">setter</span> <span style="font-family: SimSun; font-size: 9pt;">方法可以被继承，允许设置默认值，而且有良好的</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">IDE</span> <span style="font-family: SimSun; font-size: 9pt;">支持。缺点当然就是对象无</span>

<span style="font-family: SimSun; font-size: 9pt;">法在构造完成后马上进入就绪状态。</span> <span style="font-family: Arial; font-size: 14pt; font-weight: bold; color: #ffffff;">12</span>

<span style="font-family: SimSun; font-size: 9pt;">综上所述，构造方法注入和</span> <span style="font-family: 'Times New Roman'; font-size: 9pt;">setter</span> <span style="font-family: SimSun; font-size: 9pt;">方法注入因为其侵入性较弱，且易于理解和使用，所以是现在使</span>

<span style="font-family: SimSun; font-size: 9pt;">用最多的注入方式；而接口注入因为侵入性较强，近年来已经不流行了。</span>

<span style="font-family: 宋体; font-size: 10pt;">回到开头的代码</span>

<pre class="lang:default decode:true  ">public FXNewsProvider()

{

  newsListener = new DowJonesNewsListener();

  newPersistener = new DowJonesNewsPersister();

}</pre>
&nbsp;

&nbsp;

<span style="font-family: 宋体; font-size: 10pt;">由于这里已经写死了，所以当我们添加了一个新的新闻源，比如M4网站的时候，这个对象不能用了，我们要新写一个</span> <span style="font-family: 宋体; font-size: 8pt;">M4</span> <span style="font-family: Courier; font-size: 8pt;">NewsProvider</span> <span style="font-family: 宋体; font-size: 8pt;">的类，然后新写新闻获取类，然后重新实现。</span>

<span style="font-family: 宋体; font-size: 8pt;">而使用了IoC之后，</span>

<pre class="lang:default decode:true ">FXNewsProvider dowJonesNewsProvider =

new FXNewsProvider(new DowJonesNewsListener(),new DowJonesNewsPersister());

...

FXNewsPrivider marketWin24NewsProvider =

new FXNewsProvider(new MarketWin24NewsListener(),new DowJonesNewsPersister());</pre>
&nbsp;

&nbsp;

<span style="font-family: 宋体; font-size: 10pt;">我们只要实现一个</span> <span style="font-family: Courier; font-size: 8pt;">MarketWin24NewsListener</span> <span style="font-family: 宋体; font-size: 8pt;">雷就可以，而且</span> <span style="font-family: Courier; font-size: 8pt;">FXNewsProvider </span><span style="font-family: 宋体; font-size: 8pt;">可以重用。</span>

<span style="font-family: 宋体; font-size: 8pt;">而且使用了IoC之后，单元测试变得简单，我们只有写一个Mock</span> <span style="font-family: Courier; font-size: 8pt;">NewsPersister</span> <span style="font-family: 宋体; font-size: 8pt;">就可以来测试了。</span>