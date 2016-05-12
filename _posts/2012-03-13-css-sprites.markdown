---
layout: post
title:  "CSS Sprites"
categories: css
---

While I was working on the navigation bar, I came across this AaHa... trick. In the beginning, I thought it was crazy that icons and small images are combined into a large picture. A minute later, I realized that there must be some reason because so many websites have applied this technique.

![Cascading Selector](/assets/2012-03-13-css-sprites-2.png "css sprites")

Not surprisingly, it turns out to be a must-to-know CSS technique called CSS Sprites. The basic idea is by combining multiple images into one, it's able to reduce the number of HTTP requests. One HTTP request is able to fetch all of the icons and small images required to display the web page. And performance research has indicated that "reducing the number of HTTP requests has the biggest impact on reducing response time and is often the easiest performance improvement to make". 

How to implement sprites with CSS? Generally speaking, firstly, we have to merge the icons into one big sprite image via Photoshop or any other kind of image processing tool. Next, we set the sprite image as the background image. Finally, we could display any part of the image via shifting the background position.

The top picture in this post is a demo I did applying CSS sprites.  I merged a series of apple products into one image (the icons are downloaded from [Icon Archive](http://www.iconarchive.com/)). Each icon is of the size 64px * 64px. All the icons are aligned side by side, without gaps between them. Therefore, the whole sprite image is of the size 256px * 64px.

![Cascading Selector](/assets/2012-03-13-css-sprites-1.png "css sprites")

The content consisting the navigation bar is an unordered list:

```html
<ul>
    <li class="corner-left clear-border-right">
        <a class="ipad" href="#" title="iPad"></a>
    </li>
    <li class="clear-border-right">
        <a class="iphone" href="#" title="iPhone"></a>
    </li>
    <li class="clear-border-right">
        <a class="ipod" href="#" title="iPod"></a>
    </li>
    <li class="corner-right">
        <a class="macbook" href="#" title="MacBook"></a>
    </li>
</ul>
```

To align the unordered list horizontally, the tip is to set the list element's display as inline-block. Basically speaking, inline-block is a way to make elements inline, yet preserving their block capabilities such as setting width and height, top and bottom margins and paddings etc. Thus, list elements are displayed horizontally. 

```css
li {
    display: inline-block;
}
```

However, since the list elements are treated as inline elements, there is always some space between two list elements even if the margin of li is set to zero. To remove these annoying gaps, one method is to set the margin to a negative value, say -2px. 

![Cascading Selector](/assets/2012-03-13-css-sprites-3.png "css sprites")

Next, I set the sprite image as the background image of the anchor element itself:

```css
.ipad, .iphone, .ipod, .macbook {
    display: block;
    width: 64px;
    height: 64px;
    background: url("../img/sprite.png") no-repeat;
}
```

Each anchor element displays only a part of the whole background. For example, the second anchor element displays the iPhone icon. To do that, merely shift the background position with negative X coordinates -64px. The shift is calculated in this way: originally, the background image is positioned with its (0, 0) aligned to the top-left of the container. Since the iPhone icon starts at (64px, 0) on the image, we have to shift the image leftward of 64px to align the start of iPhone icon to the top-left of the container. Therefore, the background is shifted with X coordinates -64px. 

```css
.iphone {
    background-position: -64px 0;
}
```

Here, I want to take some time to showcase the inset shadow effect. See, I have dropped some shadow inside the top of the boxes (only on the top side).

![Cascading Selector](/assets/2012-03-13-css-sprites-4.png "css sprites")

This effect is done by setting the box shadow to inset and the spread radius to a negative value. Feel free take a try.

```css
box-shadow: inset 0 5px 5px -2px #CDCDCD;
```

**Reference**

http://css-tricks.com/css-sprites/
http://www.alistapart.com/articles/sprites
http://www.mezzoblue.com/tests/revised-image-replacement/
https://developer.mozilla.org/en/CSS/background-position