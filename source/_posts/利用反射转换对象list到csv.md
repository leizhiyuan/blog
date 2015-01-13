title: 利用反射转换对象list到csv
tags:
  - C＃
  - 大学
  - 学习
  - 设计
  - 软件
id: 2248
categories:
  - 学习笔记
date: 2013-02-02 07:39:11
---

扒自一工程。。可以学习一下.net中反射的简单用法
<pre class="lang:default decode:true">      /// &lt;summary&gt;
        /// Take object List as input and export to csv which will be prompt save as dialog
        /// &lt;/summary&gt;
        /// &lt;typeparam name="T"&gt; Type of object&lt;/typeparam&gt;
        /// &lt;param name="listToExport"&gt; Object list to export&lt;/param&gt;
        /// &lt;param name="seperateChar"&gt; character to use as scv separator&lt;/param&gt;
        public static string ExportListToCSV&lt;T&gt;( List&lt;T&gt; listToExport, string seperateChar)
        {
            Int32 success = 0;
            StringBuilder export = new StringBuilder();
            try
            {
                string seperator = "" ;
                StringBuilder builder = new StringBuilder();

                //获取表头的
                PropertyInfo[] fieldInfo = listToExport[0].GetType().GetProperties();
                foreach (PropertyInfo col in fieldInfo)
                {
                    if (!col.PropertyType.FullName.Equals("System.Data.EntityKey") &amp;&amp; !col.PropertyType.FullName.Equals("System.Data.EntityState" ))
                    {
                        builder.Append(seperator).Append(col.Name);
                        seperator = seperateChar;
                    }
                }
                export.AppendLine(builder.ToString());
                foreach (T dataItem in listToExport)
                {
                    PropertyInfo[] allProperties = dataItem.GetType().GetProperties();
                    seperator = "";
                    StringBuilder builderTmp = new StringBuilder();
                    //真正求数据域的
                    foreach (PropertyInfo thisProperty in allProperties)
                    {
                        if (!thisProperty.PropertyType.FullName.Equals("System.Data.EntityKey") &amp;&amp; !thisProperty.PropertyType.FullName.Equals("System.Data.EntityState" ))
                        {
                            object value = thisProperty.GetValue(dataItem, null);
                            String propetyValue = (value == null ? String.Empty : value.ToString());
                            builderTmp.Append(seperator).Append(propetyValue);
                            seperator = seperateChar;
                        }
                    }
                    ++success;
                    export.AppendLine(builderTmp.ToString());
                }
            }
            catch (Exception ex)
            {
                throw ex;
            }
            return export.ToString();
        }</pre>
<pre class="lang:default decode:true">if (!thisProperty.PropertyType.FullName.Equals("System.Data.EntityKey") &amp;&amp; !thisProperty.PropertyType.FullName.Equals("System.Data.EntityState"))</pre>
&nbsp;

增加这个只是两个预定义的实体框架内的属性。

使用PropertyInfo 可以获得名字。数据类型。声明类型，反射类型，读写属性等等。也可以设置或获取属性值。