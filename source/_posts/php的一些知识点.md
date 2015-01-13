title: php的一些知识点
tags:
  - php
id: 228
categories:
  - 学习笔记
date: 2012-04-17 17:37:18
---

一.php中单双引号的区别

1.  "" 双引号里面的字段会经过编译器解释，然后再当作HTML代码输出。
2.  '' 单引号里面的不进行解释，直接输出。
从字面意思上就可以看出，单引号比双引号要快了。单引号支持\'和\\的转义，但其他一些转义字符就必须是在双引号里了。
例如：
<pre lang="php">
$name='my name is bystander';
echo $name //结果是:my name is bystander
echo '$name' //结果是:$name
echo "$name" //结果是:my name is bystander
</pre>
二.require和include的区别
　　在于，出现错误时，require是error,也就是说脚本会停止执行，而include是warning。也就说说代码会继续执行，另外，无论require的位置如何。即使是放在一个if代码块里面。他也会将指定文件包含进来。。即使该if部分不执行。顺带说下include 和include_once。其实类似于c里面的ifdef。。就是只包含一次。不重复包含。

三。php类
php类的构造函数命名为_construct.析构函数为_destruct，需要调用父类的构造函数时，使用parent::_construct()来调用

四。pear包
PEAR是"PHP Extension and Application Repository"的缩写，也就是一个PHP扩展和应用的管理工具，
具体可参考：[http://www.berlinix.com/php_pear.html](http://www.berlinix.com/php_pear.html)

五。PDO
PDO(PHP Data Objects)扩展为PHP访问数据库定义了一个轻量级的、一致性的接口，它提供了一个数据访问抽象层，这样，无论使用什么数据库，都可以通过一致的函数执行查询和获取数据。PDO随PHP5.1发行，在PHP5.0的PECL扩展中也可以使用。其实就是一个访问数据库的一个类，连数据库的时候实例一个。调用方法就这样。前提是这个装好了。
linux下具体可以参考：[LINUX下手动安装PDO_MYSQL](http://www.pkphp.com/2008/04/24/linux%E4%B8%8B%E6%89%8B%E5%8A%A8%E5%AE%89%E8%A3%85pdo_mysql/)
window下和例子（例子也可以在linux下使用）参考此文：[PHP5中PDO的简单使用](http://blog.csdn.net/heiyeshuwu/article/details/1355970)

六。Zend

*   准确地讲 Zend 框架究竟是什么呢？Zend 框架具有以下特征：
*   是基于 PHP 建立的。
*   是面向对象的。
*   使用 MVC 范例。
*   具有开放源码贡献者。
*   有贡献者负责保证他们的代码不是他人的知识产权。

通过建立 MVC 模式，Zend 框架的目标是使编程生活更加轻松，这不仅体现在通用领域，而且对您始终想要做的具体的事情也是如此，比如访问数据库或输出 PDF 文件。

具体可参考：[理解 Zend 框架，第 1 部分: 基础](https://www.ibm.com/developerworks/cn/opensource/os-php-zend1/)

七。GD库
　　百科上的解释：　GD库，是php处理图形的扩展库，GD库提供了一系列用来处理图片的API，使用GD库可以处理图片，或者生成图片。 在网站上GD库通常用来生成缩略图，<mark>或者用来对图片加水印，或者用来生成汉字验证码，或者对网站数据生成报表等</mark>。在PHP处理图像，可使用GD库，
　　如何检测Lamp是否已经有了GD库呢。将下面的代码<pre lang="php"><?php phpinfo(); ?> </pre>
保存为phpinfo.php，然后传到服务器的网站目录下，在浏览器访问这个文件，如: localhost/phpinfo.php，然后找到一行为GD Support，后面如果是enabled，那就说明系统已经有了GD库
　　使用的例子可以参见官方一个例子，在指定的图片上添加文字。
例子参见：[http://www.php.net/manual/en/image.examples-png.php](http://www.php.net/manual/en/image.examples-png.php)

八。Smarty引擎
　　Smarty主要就是分离了前台和后台的实现。使得多人合作开发程序更加方便。因为前后台在代码上基本完全分离，一般实现就是一个模板文件，以tpl结尾，一个php文件。在tpl文件里面用变量来指示内容。而在php文件中将内容传递过去。来完成页面显示。
参考：[smarty安装及初级使用](http://blog.csdn.net/phphot/article/details/2190534)只看第一部分的例子即可。