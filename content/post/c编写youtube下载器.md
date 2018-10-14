---
title: "C#编写youtube下载器"
tags:
  - C＃
  - 大学
  - 编程
  - 翻译
id: 1670
categories:
  - 我的翻译
date: 2012-09-25 09:33:12
---

### 介绍

本文将会暂时如何仅仅使用C#来下载youtub视频，代码简单也容易理解，每个人都可以把它整合到自己的工程项目里。

我没有使用任何第三方的库来完成这段代码，你所要做的仅仅是把两个.cs文件整合进你的项目里。

### 使用代码

这个工程里有两个主要的类

YouTubeVideoQuality Class（youtube视频质量类）

<pre class="lang:c# decode:true " >public class YouTubeVideoQuality 
{
    /// &lt;summary&gt;
    /// Gets or Sets 文件名
    /// &lt;/summary&gt;
    public string Videotitle: { get; set; }
    /// &lt;summary&gt;
    /// Gets or Sets 文件扩展
    /// &lt;/summary&gt;
    public string Extention { get; set; }
    /// &lt;summary&gt;
    /// Gets or Sets 文件地址
    /// &lt;/summary&gt;
    public string DownloadUrl { get; set; }
    /// &lt;summary&gt;
    /// Gets or Sets 视频地址
    /// &lt;/summary&gt;
    public string VideoUrl { get; set; }
    /// &lt;summary&gt;
    /// Gets or Sets 文件大小
    /// &lt;/summary&gt;
    public Size Dimension { get; set; }

    public override string ToString()
    {
        return Extention + " File " + Dimension.Width + 
                           "x" + Dimension.Height;
    }

    public void SetQuality(string Extention, Size Dimension)
    {
        this.Extention = Extention;
        this.Dimension = Dimension;
    }
}</pre> 

YouTubeDownloader Class(youtube下载类)

<pre class="lang:c# decode:true " >public class YouTubeDownloader
{
    public static List&lt;YouTubeVideoQuality&gt; GetYouTubeVideoUrls(params string[] VideoUrls)
    {
        List&lt;YouTubeVideoQuality&gt; urls = new List&lt;YouTubeVideoQuality&gt;();
        foreach (var VideoUrl in VideoUrls)
        {
            string html = Helper.DownloadWebPage(VideoUrl);
            string title: = GetTitle(html);
            foreach (var videoLink in ExtractUrls(html))
            {
                YouTubeVideoQuality q = new YouTubeVideoQuality();
                q.VideoUrl = VideoUrl;
                q.Videotitle: = title;
                q.DownloadUrl = videoLink + "&amp;title=" + title;
                if (getQuality(q))
                    urls.Add(q);
            }
        }
        return urls;
    }

    private static string GetTitle(string RssDoc)
    {
        string str14 = Helper.GetTxtBtwn(RssDoc, "'VIDEO_TITLE': '", "'", 0);
        if (str14 == "") str14 = Helper.GetTxtBtwn(RssDoc, "\"title\" content=\"", "\"", 0);
        if (str14 == "") str14 = Helper.GetTxtBtwn(RssDoc, "&amp;title=", "&amp;", 0);
        str14 = str14.Replace(@"\", "").Replace("'", "'").Replace(
                "\"", """).Replace("&lt;", "&lt;").Replace(
                "&gt;", "&gt;").Replace("+", " ");
        return str14;
    }

    private static List&lt;string&gt; ExtractUrls(string html)
    {
        html = Uri.UnescapeDataString(Regex.Match(html, "url_encoded_fmt_stream_map=(.+?)&amp;", 
                                      RegexOptions.Singleline).Groups[1].ToString());
        MatchCollection matchs = Regex.Matches(html, 
          "url=(.+?)&amp;quality=(.+?)&amp;fallback_host=(.+?)&amp;type=(.+?)&amp;itag=(.+?),", 
          RegexOptions.Singleline);
        bool firstTry = matchs.Count &gt; 0;
        if (!firstTry)
            matchs = Regex.Matches(html, 
                     "itag=(.+?)&amp;url=(.+?)&amp;type=(.+?)&amp;fallback_host=(.+?)&amp;sig=(.+?)&amp;quality=(.+?),{0,1}", 
                     RegexOptions.Singleline);
        List&lt;string&gt; urls = new List&lt;string&gt;();
        foreach (Match match in matchs)
        {
            if (firstTry)
                urls.Add(Uri.UnescapeDataString(match.Groups[1] + ""));
            else urls.Add(Uri.UnescapeDataString(match.Groups[2] + "") + "&amp;signature=" + match.Groups[5]);
        }
        return urls;
    }

    private static bool getQuality(YouTubeVideoQuality q)
    {
        if (q.DownloadUrl.Contains("itag=5"))
            q.SetQuality("flv", new Size(320, 240));
        else if (q.DownloadUrl.Contains("itag=34"))
            q.SetQuality("flv", new Size(400, 226));
        else if (q.DownloadUrl.Contains("itag=6"))
            q.SetQuality("flv", new Size(480, 360));
        else if (q.DownloadUrl.Contains("itag=35"))
            q.SetQuality("flv", new Size(640, 380));
        else if (q.DownloadUrl.Contains("itag=18"))
            q.SetQuality("mp4", new Size(480, 360));
        else if (q.DownloadUrl.Contains("itag=22"))
            q.SetQuality("mp4", new Size(1280, 720));
        else if (q.DownloadUrl.Contains("itag=37"))
            q.SetQuality("mp4", new Size(1920, 1280));
        else if (q.DownloadUrl.Contains("itag=38"))
            q.SetQuality("mp4", new Size(4096, 72304));
        else return false;
        return true;
    }
}</pre> 

### 有趣的地方

使用这个代码，你可以根据你的网速来选择不同品质的视频来下载

许多人网速很慢他们不能在线看youtube，因为我写了这个代码帮助这些人来下载视频到他们的PC上。这样，他们就能离线看了。。

### 许可

本文。包括源代码和文件，在CPOL下授权。

原文地址：[YouTube-Downloader-Using-Csharp-NET](http://www.codeproject.com/Tips/323771/YouTube-Downloader-Using-Csharp-NET)
著作权声明：本文由[http://leaver.me](http://leaver.me) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！