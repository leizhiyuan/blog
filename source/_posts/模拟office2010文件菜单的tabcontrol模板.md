title: 模拟Office2010文件菜单的TabControl模板
tags:
  - C＃
  - WPF
  - 大学
  - 总结
  - 翻译
  - 软件
id: 2084
categories:
  - 我的翻译
date: 2012-10-17 10:00:24
---

[![]({{BASE_PATH}}/images/2ad13c7f6efeae2f1463e231f11d164126743c47.png "1")](http://leaverimage.b0.upaiyun.com/28185_o.png)

这是Office2010中的文件菜单点开后的效果。本文我将以强大的WPF来实现类似的效果。希望你能有所收获。而不是只拷贝/粘贴代码而已。

&nbsp;

开始之前。先把TabControl找个地方放着。
<pre class="lang:default decode:true">&lt;Window x:Class="TestClient.MainWindow"

        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"

        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"

        Title="MainWindow" Height="350" Width="525"&gt;

    &lt;TabControl Name="tabSteps"&gt;

        &lt;TabItem Header="Info" IsSelected="True"&gt;

            &lt;TextBlock&gt;Info content&lt;/TextBlock&gt;

        &lt;/TabItem&gt;

        &lt;TabItem Header="Recent"&gt;

            &lt;TextBlock&gt;Recent content tab&lt;/TextBlock&gt;

        &lt;/TabItem&gt;

        &lt;TabItem Header="New"&gt;

            &lt;TextBlock&gt;New content tab&lt;/TextBlock&gt;

        &lt;/TabItem&gt;

        &lt;TabItem Header="Print"&gt;

            &lt;TextBlock&gt;Print content tab&lt;/TextBlock&gt;

        &lt;/TabItem&gt;

        &lt;TabItem Header="Save &amp;amp; Send"&gt;

            &lt;TextBlock&gt;Save &amp;amp; send content tab&lt;/TextBlock&gt;

        &lt;/TabItem&gt;

        &lt;TabItem Header="Help"&gt;

            &lt;TextBlock&gt;Help tab&lt;/TextBlock&gt;

        &lt;/TabItem&gt;

    &lt;/TabControl&gt;

&lt;/Window&gt;</pre>
&nbsp;

然后会大概是这个效果

[![]({{BASE_PATH}}/images/797a20d8d2f0489af03c4d57e307fcf0c14657be.png "2")](http://leaverimage.b0.upaiyun.com/28186_o.png)

为了改变TabControl的显示效果。我们使用模板机制，我们把模板写进一个资源字典里。这样就可以重用了。添加一个资源字典的步骤如下

右键点击工程-添加-资源字典

然后在资源字典里添加一些代码。
<pre class="lang:default decode:true">  &lt;ControlTemplate x:Key="OfficeTabControl" TargetType="{x:Type TabControl}"&gt;

        &lt;Grid&gt;

            &lt;Grid.ColumnDefinitions&gt;

                &lt;ColumnDefinition Width="160" /&gt;

                &lt;ColumnDefinition/&gt;

            &lt;/Grid.ColumnDefinitions&gt;

            &lt;Border Background="#FFE9ECEF"

                    Grid.Column="0"

                    BorderBrush="LightGray"

                    BorderThickness="1"

                    SnapsToDevicePixels="True" /&gt;

            &lt;StackPanel IsItemsHost="True"

                        Grid.Column="0"

                        Margin="0,0,-1,0"

                        SnapsToDevicePixels="True" /&gt;

            &lt;ContentPresenter

                Content="{TemplateBinding SelectedContent}"

                Grid.Column="1"

                Margin="15,0,0,0" /&gt;

        &lt;/Grid&gt;

    &lt;/ControlTemplate&gt;</pre>
&nbsp;

这样就添加了一个有一个grid元素的名为OfficeTabControl的控件模板 . Grid 被分成两列，一列是标签页，一列是页内容。左边的列包含一个灰色背景和亮灰色的边缘线，然后一个StackPanel，IsItemsHost属性被设置为true，

这样标签项被会放在这个栈面板里。第二列是ContentPresenter 这会放置标签页内容。然后让我们前面的TabControl使用新模板。设置Template 属性。
<pre class="lang:default decode:true"> &lt;Window.Resources&gt;

        &lt;ResourceDictionary&gt;

            &lt;ResourceDictionary.MergedDictionaries&gt;

                &lt;ResourceDictionary Source="OfficeTab.xaml" /&gt;

            &lt;/ResourceDictionary.MergedDictionaries&gt;

        &lt;/ResourceDictionary&gt;

    &lt;/Window.Resources&gt;

    &lt;TabControl Name="tabSteps" Template="{StaticResource OfficeTabControl}"&gt;</pre>
&nbsp;

在这之前，先把资源字典加到窗体的Reesouce里。然后再设置。然后运行软件。效果会有一些不一样。

[![]({{BASE_PATH}}/images/2ba7dae0ace3d2aba71967d816315b1b953d139b.png "3")](http://leaverimage.b0.upaiyun.com/28187_o.png)

然后要修改左侧单个标签的显示效果。通过改变模板来实现。给模板添加如下的代码

&nbsp;
<pre class="lang:default decode:true">&lt;ControlTemplate x:Key="OfficeTabControl" TargetType="{x:Type TabControl}"&gt;

    &lt;ControlTemplate.Resources&gt;

        &lt;Style TargetType="{x:Type TabItem}"&gt;

            &lt;Setter Property="Template"&gt;

                &lt;Setter.Value&gt;

                    &lt;ControlTemplate TargetType="{x:Type TabItem}"&gt;

                        &lt;Grid SnapsToDevicePixels="True"&gt;

                            &lt;ContentPresenter

                                Name="buttonText"

                                Margin="15,0,5,0"

                                TextBlock.FontFamily="Calibri"

                                TextBlock.FontSize="12pt"

                                TextBlock.Foreground="Black"

                                Content="{TemplateBinding Header}"

                                VerticalAlignment="Center"/&gt;

                        &lt;/Grid&gt;

                    &lt;/ControlTemplate&gt;

                &lt;/Setter.Value&gt;

            &lt;/Setter&gt;

        &lt;/Style&gt;

    &lt;/ControlTemplate.Resources&gt;</pre>
&nbsp;

然后再运行

[![]({{BASE_PATH}}/images/ded32740b808794d30171ba7b82b5b1c6c89a6c3.png "4")](http://leaverimage.b0.upaiyun.com/28188_o.png)

VisualState很有意思。我们可以放在Grid里。然后设置正常状态和鼠标悬停的状态。

为了添加鼠标悬停效果，我们添加两个Borders元素。一个右边缘是灰线，另一个用在背景上。亮蓝色放在上下边缘
<pre class="lang:default decode:true">&lt;Border Name="hoverShape"

        Height="40"

        Margin="0,0,1,0"

        SnapsToDevicePixels="True"

        BorderThickness="0,0,1,0"

        BorderBrush="LightGray"&gt;

    &lt;Border BorderBrush="#FFA1B7EA"

            BorderThickness="0,1"

            Background="#FFE5EEF9"

            Height="40"

            SnapsToDevicePixels="True" /&gt;

&lt;/Border&gt;</pre>
&nbsp;

之后，我们为VisualState创建故事板，一个是正常状态。会使得hoverShape的透明度为0.另一个是鼠标悬停的状态。透明度会变成1
<pre class="lang:default decode:true">&lt;Grid SnapsToDevicePixels="True"&gt;

    &lt;VisualStateManager.VisualStateGroups&gt;

        &lt;VisualStateGroup Name="CommonStates"&gt;

            &lt;VisualState Name="MouseOver"&gt;

                &lt;Storyboard&gt;

                    &lt;DoubleAnimation

                        Storyboard.TargetName="hoverShape"

                        Storyboard.TargetProperty="Opacity"

                        To="1"

                        Duration="0:0:.1"/&gt;

                &lt;/Storyboard&gt;

            &lt;/VisualState&gt;

            &lt;VisualState Name="Normal"&gt;

                &lt;Storyboard&gt;

                    &lt;DoubleAnimation

                        Storyboard.TargetName="hoverShape"

                        Storyboard.TargetProperty="Opacity"

                        To="0"

                        Duration="0:0:.1"/&gt;

                &lt;/Storyboard&gt;

            &lt;/VisualState&gt;

        &lt;/VisualStateGroup&gt;</pre>
&nbsp;

之后效果如下

[![]({{BASE_PATH}}/images/c3260420be7f836a117fde4fbf6f35cc91728bc9.png "5")](http://leaverimage.b0.upaiyun.com/28193_o.png)

然后添加选中项的样式，在hoverShape 下面再添加一个Border，名字是buttonShape。通过这个我们给被选中项的上下边缘添加2个像素的黑蓝色边缘，

在 Expression Blend中打开, 使用钢笔工具绘制个你想要的路径形状即可。然后把生成的xaml代码拷些过来。

[![]({{BASE_PATH}}/images/5caf76900ae64f75e8ca5f6985109cf145b1127f.png "6")](http://leaverimage.b0.upaiyun.com/28190_o.png)

也可以直接用我的代码。。不顾我说过要怎么做了。

最终看起来应该是这样的。
<pre class="lang:default decode:true">&lt;Border

    Name="buttonShape"

    Opacity="0"

    BorderBrush="#FF0343A6"

    BorderThickness="0,2"

    Height="40"

    SnapsToDevicePixels="True"&gt;

    &lt;Path

        Data="M214,108 L346,108 346.125,118.125 337.75,126.125

                   346.375,134 346.375,143.875 214.25,144.25 z"

        SnapsToDevicePixels="True"

        Stretch="Fill"

        Height="40"&gt;

        &lt;Path.Fill&gt;

            &lt;RadialGradientBrush GradientOrigin="0.2,0.5" RadiusX="0.8" RadiusY="0.8"&gt;

                &lt;GradientStop Color="#FF5FA3F6" Offset="0" /&gt;

                &lt;GradientStop Color="#FF0C55B9" Offset="1" /&gt;

            &lt;/RadialGradientBrush&gt;

        &lt;/Path.Fill&gt;

    &lt;/Path&gt;

&lt;/Border&gt;</pre>
&nbsp;

当你运行的时候你会发现没什么变化。我们还要定义被选中标签的VisualState

当添加如下代码的时候 添加一个VisualState组。也就是 SelectionStates ，然后给选中/为选中的状态添加行为/故事板。.这里通过修改透明度来实现一些效果
<pre class="lang:default decode:true">&lt;VisualStateGroup Name="SelectionStates"&gt;

    &lt;VisualState Name="Selected"&gt;

        &lt;Storyboard&gt;

            &lt;DoubleAnimation

                Storyboard.TargetName="buttonShape" Storyboard.TargetProperty="Opacity"

                To="1" Duration="0:0:.3"/&gt;

            &lt;DoubleAnimation

                Storyboard.TargetName="hoverShape" Storyboard.TargetProperty="Opacity"

                To="0" Duration="0:0:.1"/&gt;

            &lt;ColorAnimation

                Storyboard.TargetName="buttonText"

                Storyboard.TargetProperty=

                            "(TextBlock.Foreground).(SolidColorBrush.Color)"

                To="White" Duration="0:0:.1" /&gt;

        &lt;/Storyboard&gt;

    &lt;/VisualState&gt;

    &lt;VisualState Name="Unselected"&gt;

        &lt;Storyboard&gt;

            &lt;DoubleAnimation

                Storyboard.TargetName="buttonShape"

                Storyboard.TargetProperty="Opacity" To="0" Duration="0:0:.1"/&gt;

            &lt;DoubleAnimation

                Storyboard.TargetName="hoverShape"

                Storyboard.TargetProperty="Opacity" To="0" Duration="0:0:.1"/&gt;

        &lt;/Storyboard&gt;

    &lt;/VisualState&gt;

&lt;/VisualStateGroup&gt;</pre>
&nbsp;

现在再运行会发现效果更好了

[![]({{BASE_PATH}}/images/3b46642c7ed968464713adc3e60749fa05066b65.png "7")](http://leaverimage.b0.upaiyun.com/28191_o.png)

&nbsp;

还有个小问题，就是那个陷进入的小三角形的背景颜色依然还在。通过在下面添加一个白色的形状。，并且对故事板的选中和为选中状态改变一下透明度就可以了。
<pre class="lang:default decode:true">&lt;VisualStateGroup Name="SelectionStates"&gt;

    &lt;VisualState Name="Selected"&gt;

        &lt;Storyboard&gt;

            &lt;DoubleAnimation

                Storyboard.TargetName="buttonBackgroundShape"

                Storyboard.TargetProperty="Opacity" To="1" Duration="0"/&gt;

        &lt;/Storyboard&gt;

    &lt;/VisualState&gt;

    &lt;VisualState Name="Unselected"&gt;

        &lt;Storyboard&gt;

            &lt;DoubleAnimation

                Storyboard.TargetName="buttonBackgroundShape"

                Storyboard.TargetProperty="Opacity" To="0" Duration="0"/&gt;

        &lt;/Storyboard&gt;

    &lt;/VisualState&gt;

&lt;/VisualStateGroup&gt;</pre>
&nbsp;

最终效果如下:

[![]({{BASE_PATH}}/images/c540f5dfc7dbed9ae6def8f6d7d461a85be83f89.png "8")](http://leaverimage.b0.upaiyun.com/28192_o.png)

源代码地址：
[downloadicon href=http://pan.baidu.com/share/link?shareid=85332&amp;uk=1493685990]OfficeTab.7z[/downloadicon]

许可

本文包括源代码和文件在CPOL下授权

著作权声明：本文由[http://leaver.me](http://leaver.me/) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！