---
layout: post
title:  "XAML Using Sprite Image"
categories: "wpf xaml sprite"
---

CSS sprite is commonly used in web development. I have talked about it in my blog [CSS Sprite](http://yuezhizizhang.github.io/css/2012/03/13/css-sprites.html). How to use a sprite image in WPF?

Easy! First, define an ImageBrush in your resource dictionary:

```xaml
<ImageBrush x:Key="Logo" ImageSource="/Resources/sprite.png"
            Stretch="None" ViewboxUnits="Absolute" Viewbox="1238,602,254,140"/>
```

Viewbox property specifies the icon's relative position on the sprite and its size. It is **"left top width height"**. In the above example, the icon is located 1238px to the left, and 602px to the top of the sprite image. The icon's size is 254px (width) * 140px (height). Stretch="None" means no stretching.

To draw the icon on the window, we simply apply the style to a Canvas.

```xaml
<Canvas Width="254" Height="140" Background="{StaticResource Logo}"/>
```