title: FreeAnony-代理采集设置工具
tags:
  - C＃
  - 原创
  - 大学
  - 开发
  - 软件
id: 961
categories:
  - 学习笔记
date: 2012-06-17 20:10:05
---

这个名字确实有点不太好。。因为刚开始我想到这个工具是在今天早上看到一个别人的代理工具的时候，突然想做的，没有好好规化，结果后来代码越写越多。。不过收获很大。

先看界面，因为是简单实现一下，所以就不要吐槽界面了。。[![]({{BASE_PATH}}/images/10c75978f904af3fc0eb8e9c41cf2a47d4d2545f.jpg "FreeAnony")](http://leaverimage.b0.upaiyun.com/23525_o.jpg)

刚开始想的思路就是先去一个经常发布代理IP的网页去采集。然后解析成一条条的信息。然后显示出来。中途遇到几个问题，一个就是在做代理验证的时候，没有用多线程，导致直接界面无响应。另一个就是DataGridView控件要实现对一个数组的绑定，需要的一个实体对象。
<pre class="lang:c# decode:true">public IPEntity[] GetIpInfo(string url)
        {

            //获得网页源码
            string content = Get_Http(url);
            //定位代理ip位置
            int start = content.IndexOf("&lt;/strong&gt;&lt;/u&gt;&lt;/a&gt;&lt;BR&gt;");
            int end = content.LastIndexOf("&lt;BR&gt;&lt;SCRIPT type=text/javascript&gt;");
            //提取并去除一些冗余代码
            string subContent = content.Substring(start, end - start).Substring(21);
            subContent = subContent.Replace("&amp;nbsp; dn28.com", "");
            //通过br标签分隔代理列表为数组
            string[] sArray = Regex.Split(subContent, "&lt;br&gt;", RegexOptions.IgnoreCase);
            IPEntity[] list = new IPEntity[sArray.Length];
            int j = 0;
            foreach (string i in sArray)
            {
                int addrpos = i.IndexOf(":");
                string ipaddress = i.Substring(0, addrpos);
                int portpos = i.IndexOf("@");
                string ipport = i.Substring(addrpos + 1, portpos - addrpos - 1);
                int typepos = i.IndexOf(";");
                string iptype = i.Substring(portpos + 1, typepos - portpos - 1);
                string ipcountry = i.Substring(typepos + 1);
                list[j++] = new IPEntity(ipaddress, ipport, iptype, ipcountry);

            }
            return list;

        }</pre>
这部分就是先得到网页源码，然后通过IndexOf和LastIndexOf定位到ip组的位置。这是通过分析源码来确定参数的。最后通过
标签分割成字符数组。数组元素类似于112.25.12.37:80@HTTP;江苏省 移动，然后就是继续分割成一部分，构造了一个IPEntity的数组。在按钮中绑定到DataGridView中即可
IPEntity类的定义如下：这里面设置了一个status属性，用于后面代理验证的时候进行标记。
<pre class="lang:c# decode:true">public  class IPEntity
        {
            string _Address;
            string _Port;
            string _Type;
            string _Country;
            string _Status;

            public string Address
            {
                get { return _Address; }
                set { _Address = value; }
            }

            public string Port
            {
                get { return _Port; }
                set { _Port = value; }
            }
            public string Status
            {
                get { return _Status; }
                set { _Status = value; }
            }
            public string Type
            {
                get { return _Type; }
                set { _Type = value; }

            }

            public string Country
            {
                get { return _Country; }
                set { _Country = value; }
            }

            public IPEntity(string Address, string Port,string Type,string Country)
            {
                this._Address = Address;
                this._Port = Port;
                this._Type = Type;
                this._Country = Country;
            }
        }</pre>
然后是验证ip可用性的部分。这部分主要的代码是验证DataGridView中的ip地址可用性
<pre class="lang:c# decode:true">public void CheckIpArray()
        {
            pBarCheck.Maximum = dgvIp.Rows.Count;
            pBarCheck.Step = 1;
            for (int i = 0; i &lt; dgvIp.Rows.Count; i++)
            {
                string ipAddress = dgvIp["Address", i].Value.ToString();
                int ipPort = Int32.Parse(dgvIp["Port", i].Value.ToString());
                string sip = ipAddress + ":" + ipPort;
                if (checkProxyIP(sip, 5000))
                    dgvIp["Status", i].Value = "正常";
                else
                    dgvIp["Status", i].Value = "错误";
                pBarCheck.Value += pBarCheck.Step;
                Thread.Sleep(1000);
            }
            MessageBox.Show("验证完成,点击表中最左侧选择一行.");
        }</pre>
但是实际运行后就知道，会出现界面无响应。因为长时间的网络访问。为此，在按钮的单击事件中，使用简单的线程后台来操作
<pre class="lang:c# decode:true">private void btnCheck_Click(object sender, EventArgs e)
        {
            if (dgvIp.ColumnCount == 0)
                MessageBox.Show("请先搜索可用代理");
            else
            {
                Thread thread = new Thread(CheckIpArray);
                thread.IsBackground = true;
                thread.Start();
            }
        }</pre>
然后其实就没什么了。至于后面的设置iE代理不过就是对注册表的操作了
<pre class="lang:c# decode:true"> private void btnSet_Click(object sender, EventArgs e)
        {
            if (dgvIp.SelectedRows.Count &lt; 1)
                MessageBox.Show("请先选中一行可用的代理");
            else
            {
                RegistryKey rk = Registry.CurrentUser.OpenSubKey(@"Software\Microsoft\Windows\CurrentVersion\Internet Settings", true);
                string address = dgvIp.SelectedRows[0].Cells[0].Value.ToString();
                string port = dgvIp.SelectedRows[0].Cells[1].Value.ToString();
                //设置代理可用 
                rk.SetValue("ProxyEnable",1);
                //设置代理IP和端口 
                rk.CreateSubKey("ProxyServer");
                rk.SetValue("ProxyServer", address + ":" + port);
                rk.Close();
                MessageBox.Show("成功！！！");
            }
        }</pre>
不好的地方希望大家指正。因为只是一个简单的原型。完成我想的功能。所以不必过分苛求。关键的代码就是上面的了。
项目源码下载：[FreeAnony](http://pan.baidu.com/share/link?shareid=172009&amp;uk=1493685990)
参考：
[http://blog.csdn.net/cisso/article/details/4467188](http://blog.csdn.net/cisso/article/details/4467188)

[http://www.blogjava.net/aoneany/articles/281344.html](http://www.blogjava.net/aoneany/articles/281344.html)

[http://www.cnblogs.com/server-you/archive/2006/09/20/509342.html](http://www.cnblogs.com/server-you/archive/2006/09/20/509342.html)

[http://topic.csdn.net/u/20090514/01/82749a36-ffef-4da5-a607-260dccbad84b.html](http://topic.csdn.net/u/20090514/01/82749a36-ffef-4da5-a607-260dccbad84b.html)

[http://www.haogongju.net/art/1485280](http://www.haogongju.net/art/1485280)

[http://developer.51cto.com/art/200903/115200.htm](http://developer.51cto.com/art/200903/115200.htm)

[http://qianshao.blog.51cto.com/935360/201782](http://qianshao.blog.51cto.com/935360/201782)