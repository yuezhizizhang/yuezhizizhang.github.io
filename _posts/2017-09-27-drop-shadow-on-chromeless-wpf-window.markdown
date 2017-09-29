---
layout: post
title:  "Drop Shadow on a Chromeless WPF Window"
categories: "WPF"
---

In a WPF app, you would probably want some of your pop-up windows without a title bar and be un-resizable. To remove the title bar and make the window un-resizable, you have to set properties ***WindowStyle="None"*** and ***ResizeMode="NoResize"***.

```xaml
<Window ...
        WindowStyle="None"
        ResizeMode="NoResize">
</Window>
```

However, by setting these two properties, you are actually making the window <span style="color: red">***Chromeless***</span>. By chromeless, I mean the window has **no border style** and **no drop shadow effect**. The window looks really weird, isn't it? It's totally blended with the background.  Oh, no... I would like to keep the drop shadow effect. Fine, I'm going to add a customized drop effect to the window.

```xaml
<Window.Effect>
    <DropShadowEffect Color="Gray" BlurRadius="20" Direction="-90" RenderingBias="Quality" ShadowDepth="4"/>
</Window.Effect>
```

Perfect! I have achieved my goal. Now, the popup window has no title bar, is not resizable and has a beautiful drop shadow effect. BTW, the content inside the popup window is a DataGrid with a long list.

The WPF app works beautifully locally. However, if you run it on a remote desktop or on a virtual machine, you will notice that the vertical scrollbar of the DataGrid on the popup window is not responding. Why is it so slow?

If you dig into the problem, you will find WPF app has some rendering performance issues running on a remote desktop.

* [WPF Performance Issue](https://www.codeproject.com/Articles/784529/Solutions-for-WPF-Performance-Issue)
* [Optimize WPF App For Remote Desktop](https://blogs.msdn.microsoft.com/jgoldb/2010/02/27/optimizing-visual-studio-2010-and-wpf-applications-for-remote-desktop/)
* [DropShadowEffect Performance Issue](https://social.msdn.microsoft.com/Forums/vstudio/en-US/7d21c0a0-b638-46a3-9f5e-08dfd97228d3/dropshadoweffect-performance-issue?forum=wpf)

Gosh, the DropShadowEffect makes the window running on a remote desktop not scrollable. Hence, it's best not to add a DropShadowEffect. But I really want the popup window to be titlebar-less and un-resizable. What should I do? Haha, The following codes will solve the problem.

```xaml
<Window ...
        WindowStyle="None"
        ResizeMode="CanResize">

    <WindowChrome.WindowChrome>
        <WindowChrome ResizeBorderThickness="0" CaptionHeight="0"/>
    </WindowChrome.WindowChrome>

</Window
```

Even though the ResizeMode is set to 'CanResize', however, because we have set the ResizeBorderThickness to 0, thus, the window is not resizable at all. And by setting the CaptionHeight to 0, you disallow the user to maximize the window by clicking on the header area.

Cheers!