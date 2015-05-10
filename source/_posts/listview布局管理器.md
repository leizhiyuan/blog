title: ListView布局管理器
tags:
  - C＃
  - WPF
  - 大学
  - 学习
  - 编程
  - 翻译
  - 软件
id: 1745
categories:
  - 我的翻译
date: 2012-09-29 10:14:54
---

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/3908a54221845d3b8f0b2ad7c3c0b559dae7a979.jpg "listview")](http://leaverimage.b0.upaiyun.com/27575_o.jpg)
=======
[![](/images/3908a54221845d3b8f0b2ad7c3c0b559dae7a979.jpg "listview")](http://leaverimage.b0.upaiyun.com/27575_o.jpg)
>>>>>>> 换电脑之后重新备份

### 介绍

使用ListViewLayoutManager  可以控制ListView/GridView列的布局
1.	固定列宽：有着固定列宽的列
2.	范围列宽：有着最小最大宽度的列
3.	比例列宽：成比例的列宽

范围列宽可以限制列的宽度，也包括填充列的剩余可视区域。

据我们了解的Html中的表格和Grid空间。比例列以一个百分比来定义列宽，以下几个因素共同确定了比例列的宽度。

1.	垂直ListView滚动条的可视与否
2.	ListView控件宽度的改变
3.	非比例列宽度的改变

本程序支持通过XAML或是后台代码来控制ListView。如果通过XAML来控制。则允许ListViewLayoutManager 被附加到一个存在的ListView控件上。

ConverterGridColumn 类通过接口IValueConverter 提供了对象绑定。使用ImageGridViewColumn 类则允许通过DataTemplate（数据模板）将列显示成图片等。

在 [User Setting Applied](http://www.codeproject.com/Articles/25829/User-Settings-Applied)中，我展示了如何固定ListViewlieder顺序和大小

### XAML中ListView/GridView布局

固定列
下面的例子展示了通过XAML使用固定列宽控制列

<pre class="lang:c# decode:true " >&lt;ListView
    Name="MyListView"
    ctrl:ListViewLayoutManager.Enabled="true"&gt;

    &lt;ListView.View&gt;
      &lt;GridView&gt;
        &lt;GridViewColumn
          DisplayMemberBinding="{Binding Path=Name}"

          ctrl:FixedColumn.Width="100"
          Header="Name" /&gt;

        &lt;GridViewColumn
          DisplayMemberBinding="{Binding Path=City}"
          ctrl:FixedColumn.Width="300"

          Header="City" /&gt;
      &lt;/GridView&gt;

    &lt;/ListView.View&gt;
  &lt;/ListView&gt;
</pre> 

设置附加到ListView控件上的ListViewLayoutManager 的Enabled属性为True。然后FixedColumn.Width 就会阻止鼠标拖动改变列的宽度。

比例列

下面的例子展示了使用XAML通过比例来控制列
<pre class="lang:c# decode:true " ><ListView
    Name="MyListView"
    ctrl:ListViewLayoutManager.Enabled="true">
    <ListView.View>

      <GridView>

        <GridViewColumn
          DisplayMemberBinding="{Binding Path=Name}"
          ctrl:ProportionalColumn.Width="1"

          Header="Name" />

        <GridViewColumn
          DisplayMemberBinding="{Binding Path=City}"

          ctrl:ProportionalColumn.Width="3"
          Header="City" />
      </GridView>

    </ListView.View>

  </ListView></pre>

对比Grid控件的RowDefinition.Width 属性，ProportionalColumn.Width会计算百分比。简单来说，就是上面的例子中Name列会占到总宽度的25%，而City列占到75%。
与固定列相似。鼠标将不能改变列的宽度。

范围列

下面的例子展示了使用XAML通过最小/最小宽度来控制列
<pre class="lang:c# decode:true " >
<ListView
    Name="MyListView"
    ctrl:ListViewLayoutManager.Enabled="true">
    <ListView.View>

      <GridView>

        <GridViewColumn
          DisplayMemberBinding="{Binding Path=Name}"
          ctrl:RangeColumn.MinWidth="100"
          Width="150"

          Header="Name" />

        <GridViewColumn
          DisplayMemberBinding="{Binding Path=City}"
          ctrl:RangeColumn.MaxWidth="200"
          Width="150"

          Header="City" />

        <GridViewColumn
          DisplayMemberBinding="{Binding Path=Country}"
          Width="100"
          ctrl:RangeColumn.MinWidth="50"

          ctrl:RangeColumn.MaxWidth="150"
          Header="Country" />

        <GridViewColumn
          DisplayMemberBinding="{Binding Path=State}"
          Width="100"

          ctrl:RangeColumn.MinWidth="100"
          ctrl:RangeColumn.IsFillColumn="true"
          Header="Country" />

      </GridView>

    </ListView.View>
  </ListView></pre>

第一个范围列的IsFillColumn 属性被设置为True，因此将会自动改变大小来填满剩余的空间，而如果ListView包含一个比例列的话，范围列将不会填充

通过鼠标可以拖动范围列的宽度。鼠标指针会有一些提示。。

组合使用

在真实的世界里。组合使用很普遍。他们的顺序可以多种多样。
<pre class="lang:c# decode:true " >
<ListView
    Name="MyListView"
    ctrl:ListViewLayoutManager.Enabled="true">

    <ListView.View>
      <GridView>

        <GridViewColumn
          DisplayMemberBinding="{Binding Path=State}"
          ctrl:FixedColumn.Width="25"
          Header="Name" />

        <GridViewColumn
          DisplayMemberBinding="{Binding Path=Name}"

          Width="150"
          ctrl:RangeColumn.MinWidth="100"
          ctrl:RangeColumn.MaxWidth="200"

          Header="City" />

        <GridViewColumn
          DisplayMemberBinding="{Binding Path=City}"
          ctrl:ProportionalColumn.Width="1"
          Header="Zip" />

        <GridViewColumn
          DisplayMemberBinding="{Binding Path=Country}"

          ctrl:ProportionalColumn.Width="2"
          Header="Country" />
      </GridView>

    </ListView.View>
  </ListView></pre>

### 使用后台代码控制ListView/GridView布局

<pre class="lang:c# decode:true " >
ListView listView = new ListView();
 new ListViewLayoutManager( listView ); // attach the layout manager

 GridView gridView = new GridView();
 gridView.Columns.Add( FixedColumn.ApplyWidth( new MyGridViewColumn( "State" ), 25 ) );
 gridView.Columns.Add( RangeColumn.ApplyWidth( new MyGridViewColumn( "Name" ), 100,
     150, 200 ) ); // 100...200
 gridView.Columns.Add( ProportionalColumn.ApplyWidth( new MyGridViewColumn( "City" ),
     1 ) ); // 33%
 gridView.Columns.Add( ProportionalColumn.ApplyWidth( new MyGridViewColumn(
     "Country" ), 2 ) ); // 66%

 listView.View = gridView;
</pre>

### 定制列的效果

类ConverterGridColumn 作为一个基类，用来绑定列到独立的对象。
<pre class="lang:c# decode:true " >// ------------------------------------------------------------------------
  public class Customer
  {
    // ----------------------------------------------------------------------
    public Customer()
    {
    } // Customer

    // ----------------------------------------------------------------------
    public string FirstName
    {
      get { return this.firstName; }
      set { this.firstName = value; }
    } // FirstName

    // ----------------------------------------------------------------------
    public string LastName
    {
      get { return this.lastName; }
      set { this.lastName = value; }
    } // LastName

    // ----------------------------------------------------------------------
    // members
    private string firstName;
    private string lastName;

  } // class Customer

  // ------------------------------------------------------------------------
  public class CustomerFullNameColumn : ConverterGridViewColumn
  {
    // ----------------------------------------------------------------------
    public CustomerGridViewColumn() :
      base( typeof( Customer ) )
    {
    } // CustomerGridViewColumn

    // ----------------------------------------------------------------------
    protected override object ConvertValue( object value )
    {
      Customer customer = value as Customer;
      return string.Concat( customer.FirstName, " ", customer.LastName );
    } // ConvertValue

  } // class CustomerFullNameColumn</pre>

### 列以图片展示

ImageGridColumn 作为一个基类，用来绑定列到图片
<pre class="lang:c# decode:true " >// ------------------------------------------------------------------------
  public class Customer
  {
    // ----------------------------------------------------------------------
    public Customer()
    {
    } // Customer

    // ----------------------------------------------------------------------
    public bool IsActive
    {
      get { return this.isActive; }
      set { this.isActive = value; }
    } // IsActive

    // ----------------------------------------------------------------------
    // members
    private bool isActive;

  } // class Customer

  // ------------------------------------------------------------------------
  public class CustomerActiveColumn : ImageGridViewColumn
  {
    // ----------------------------------------------------------------------
    public CustomerActiveColumn()
    {
    } // CustomerActiveColumn

    // ----------------------------------------------------------------------
    protected override ImageSource GetImageSource( object value )
    {
      Customer customer = value as Customer;
      if ( customer != null )
      {
        return new BitmapImage( new Uri( customer.IsActive ? "Active.png" :
            "Inactive.png" ) );
      }
      return null;
    } // GetImageSource

  } // class CustomerActiveColumn</pre>

### 有意思的地方

作为ListView控件的核心部件- ListViewLayoutManager  有以下的功能
阻止改变固定列和比例列的宽度
强制了范围列的范围
随着ListView控件的改变而更新列布局
随着某一列的改变而更新布局

为了正确的接收到请求的信息，分析ListView控件的可视树是很有必要的,Thumb对象提供了列宽改变的事件。而为了正确的展示鼠标指针，PreviewMouseMove 和PreviewMouseLeftButtonDown 事件都会被处理

当Viewport控件大小改变（ScrollChangedEventArgs.ViewportWidthChange）的时候，会触发ScrollViewer 控件的ScrollChanged 事件
通过使用DependencyPropertyDescriptor  可以追踪 GridViewColumn的宽度改变。并发出通知。

为了可以集成进已有的系统，列数据被放置在附加属性里。使用DependencyProperty.ReadLocalValue()方法会检测当前的属性是否在同一个对象里。

类ConverterGridViewColumn  同时使用一个简单的绑定。展示数据转换（IValueConverter接口）

类ImageGridViewColumn 在数据模板中使用FrameworkElementFactory 来动态嵌入图片，缺省情况下，在ListView/GridView控件中的图片会自动拉伸（Image.Stretch属性），因为数据模板中的图片是动态创建的。模板元素的值需要使用绑定来制定
<pre class="lang:c# decode:true " >// ----------------------------------------------------------------------
 protected ImageGridViewColumn( Stretch imageStretch )
 {
   FrameworkElementFactory imageElement = new FrameworkElementFactory( typeof( Image ) );

   // image stretching
   Binding imageStretchBinding = new Binding();
   imageStretchBinding.Source = imageStretch;
   imageElement.SetBinding( Image.StretchProperty, imageStretchBinding );

   DataTemplate template = new DataTemplate();
   template.VisualTree = imageElement;
   CellTemplate = template;
 } // ImageGridViewColumn</pre>

### 许可

本文及所有代码和文件在CPOL下授权

### Demo下载

[ListView LayOut](http://pan.baidu.com/share/link?shareid=64579&uk=1493685990)

原文地址：[ListView-Layout-Manager](http://www.codeproject.com/Articles/25058/ListView-Layout-Manager)
著作权声明：本文由[http://leaver.me](http://leaver.me) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！