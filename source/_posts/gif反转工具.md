title: gif反转工具
tags:
  - C＃
  - 原创
  - 学习
  - 编程
  - 软件
id: 938
categories:
  - 学习笔记
date: 2012-06-15 17:33:29
---

首先看下效果图：
[![]({{BASE_PATH}}/images/b38ddf634b3f63f7f6e7f6016218ba55907c67b5.jpg "gif反转工具")](http://leaverimage.b0.upaiyun.com/23424_o.jpg)
然后是两张gif的对比
[![]({{BASE_PATH}}/images/3b7f81c3127dc8ebb3eb51d13a29d45751861ea7.gif "旧图")](http://leaverimage.b0.upaiyun.com/23425_o.gif)   //原本图是正着走的
[![]({{BASE_PATH}}/images/576d8c2f7990984cd6927c59d24684f2ae94fcb4.gif "新图")](http://leaverimage.b0.upaiyun.com/23426_o.gif)   //处理后是倒着走的

gif是动态的嘛。然后我昨天和一个朋友聊天的时候发了一串相同的gif图，然后看着千篇一律的东西。我想能不能写个程序实现gif的初始状态不同呢。什么意思呢。我们知道，gif是由帧构成的，我想实现的功能是比如一个gif共有十帧，那么我写出来的程序能够生成10个gif文件，分别对应不同的初始状态来进行循环。后来一想，gif帧太多的话，比较慢，而且也不实用，于是决定简化一下，只做一个反转工具，比如一个gif是从左到右播放的，通过这个成功可以生成一个相同的gif图，不过是倒着播放的。

思路很简单，就是先把gif分解成很多帧，然后对帧进行合并，合并帧之前把帧的位置反转一下就可以了。因为我自己对图像处理的知识不懂，只想到了思路，所以这些功能都要找些资料，然后修改，测试。

分割帧的代码如下
<pre class="lang:c# decode:true" title="分割gif">        //解码gif图片
        public List&lt;string&gt; GetFrames(string pPath, string pSavedPath)
        {
            Image gif = Image.FromFile(pPath);
            FrameDimension fd = new FrameDimension(gif.FrameDimensionsList[0]);

            //获取帧数(gif图片可能包含多帧，其它格式图片一般仅一帧)
            int count = gif.GetFrameCount(fd);
            List&lt;string&gt; gifList=new List&lt;string&gt;();
            //以Jpeg格式保存各帧

            for (int i = 0; i &lt; count; i++)
              {
                  gif.SelectActiveFrame(fd, i);
                  gif.Save(pSavedPath + "\\frame_" + i + ".png", ImageFormat.Png);
                  gifList.Add(pSavedPath + "\\frame_" + i + ".png");
              }
            return gifList;
         }</pre>
可以看到，返回了一个包含所有生成的帧地址的list列表。然后就是使用gifList作为参数来合并了。
<pre class="lang:c# decode:true" title="合并gif">//获取系统临时目录存放解码后的png图片
                string temppath = System.Environment.GetEnvironmentVariable("TEMP"); 

                List&lt;string&gt; gifList = GetFrames(tBoxFile.Text, temppath);
                gifList.Reverse();
                String outputFilePath = "new.gif";
                AnimatedGifEncoder ae = new AnimatedGifEncoder();
                ae.Start(outputFilePath);
                ae.SetDelay(100);    // 延迟间隔
                ae.SetRepeat(0);  //-1:不循环,0:总是循环 播放  
                for (int i = 0, count = gifList.Count; i &lt; count; i++)
                {
                    ae.AddFrame(Image.FromFile(gifList[i]));
                }
                ae.Finish();
                MessageBox.Show("成功!新文件已保存在同目录");</pre>
这里面使用了AnimatedGifEncoder这个类，这是Gif.Components.dll动态连接库里的类（此库开源，文末给出地址），是我在codeProject上找到的。首先我把gifList反转，然后合并保存到同目录。中间生成的帧为了方便我保存到了temp目录。

本来这个库里是分割gif的功能的。但是我实际测试后发现效果非常差，图片黑条泛滥，根本没法看。所以还是使用上面那段代码，相关代码我依然保存在工程里，有兴趣可以自己测试。

明天四级考试，求人品。。

项目源码:[gif反转工具](http://pan.baidu.com/share/link?shareid=125360&amp;uk=1493685990)

参考：
[C#图片处理：获取GIF 动画图片中的各个帧](http://www.cnblogs.com/top5/archive/2011/03/19/1988595.html)
[NGif, Animated GIF Encoder for .NET](http://www.codeproject.com/Articles/11505/NGif-Animated-GIF-Encoder-for-NET)