---
layout: post
title:  "XAML Data Binding"
categories: wpf xaml c#
---

I'm late to WPF development. I have learned a few tips from my recent experience with a WPF project. Most of my knowledge of WPF, XAML and C# are from Googling. Here, I simply want to summarize some of the tricks I've learned, for furture reference and etc.

The first trick I'd like to talk about is Data Binding. Data Binding looks really easy in XAML, isn't it? Suppose that you want to bind a variable 'Status' to a TextBlock. Through this way, you hope that by changing the value of 'Status' in your C# code, the change will be reflected in the UI as well.

```xaml
XAML

<TextBlock x:Name="UsernameBox"
           Text="{Binding Path=Username, Mode=OneWay}"/>
```

```c#
C#

public string Username { get; set; }
```

I'm sorry it will disappoint you! Once after the page has been loaded, the text UI doesn't change at all no matter what changes you make to the Username variable.

What's missing here is to send a property change event.

```c#
C#

public event PropertyChangedEventHandler PropertyChanged;

private void NotifyPropertyChanged(string propertyName)
{
    if (this.PropertyChanged != null &&
        !string.IsNullOrEmpty(propertyName))
    {
        this.PropertyChanged(this, new PropertyChangedEventArgs(propertyName));
    }
}

private string username;
public string Username
{
    get { return username;}
    set
    {
        if (username != value)
        {
            username = value;
            NotifyPropertyChanged("Username");
        }
    }
}
```

Only by sending a property changed event, you are able to notify the UI to update accordingly.

Here is a blog which talks more detalis and some improvements of notifying property change:

[WPF and Notifying Property Change](http://www.daedtech.com/wpf-and-notifying-property-change/)

Cheers!