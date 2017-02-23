---
layout: post
title:  "WPF Busy Spinner"
categories: "wpf xaml spinner"
---

Busy spinner is a must-have in almost every application. It's easy to implement in the HTML world, simply using an animated GIF. Try this website **[LOADING.IO](http://loading.io/)**, you are able to generate a variety of spinner gifs.

However, in WPF, you are not able to display an animated GIF in an image. Images are static. What a pity! Luckily, some smart guys have solved this pain point for us. I believe nearly all of the WPF busy spinner related topics on Google will lead you to this blog **[WPF: Circular Progress Bar](https://sachabarbs.wordpress.com/2009/02/03/wpf-circular-progress-bar/)**.

My codes below are based on the above blog too, except that I've made two improvements.

* I use Viewbox as the parent element, so that the spinner could be scaled to different sizes.

* I provide the methods to show and hide the spinner. When the spinner is shown, the spinning animation is started; while when it's hidden, the animation is stopped. Animation consumes a lot of CPU. Hence, I feel the need to stop the animation before hidding the spinner. I'm not sure if an animation is rendered when the element is hidden. Probably Not!

```xaml
<Viewbox x:Class="Spinner.CircularProgressBar"
         xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
         xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
         xmlns:local="clr-namespace:Spinner"
         Height="20" Width="20" Stretch="Uniform">

    <Grid x:Name="LayoutRoot" Background="Transparent"
          HorizontalAlignment="Center"
          VerticalAlignment="Center">

        <Grid.RenderTransform>
            <ScaleTransform x:Name="SpinnerScale"
                            ScaleX="1.0" ScaleY="1.0" />
        </Grid.RenderTransform>

        <Canvas RenderTransformOrigin="0.5,0.5"
                HorizontalAlignment="Center"
                VerticalAlignment="Center"
                Width="120" Height="120"
                x:Name="LayoutCanvas">

            <Ellipse Width="21.835" Height="21.862"
                     Canvas.Left="20.1696"
                     Canvas.Top="9.76358"
                     Stretch="Fill"
                     Opacity="1.0"
                     Fill="Green"/>

            <Ellipse Width="21.835" Height="21.862"
                     Canvas.Left="2.86816"
                     Canvas.Top="29.9581" Stretch="Fill"
                     Opacity="0.9"
                     Fill="Green"/>

            <Ellipse Width="21.835" Height="21.862"
                     Canvas.Left="5.03758e-006"
                     Canvas.Top="57.9341" Stretch="Fill"
                     Opacity="0.8"
                     Fill="Green"/>

            <Ellipse Width="21.835" Height="21.862"
                     Canvas.Left="12.1203"
                     Canvas.Top="83.3163" Stretch="Fill"
                     Opacity="0.7"
                     Fill="Green"/>

            <Ellipse Width="21.835" Height="21.862"
                     Canvas.Left="36.5459"
                     Canvas.Top="98.138" Stretch="Fill"
                     Opacity="0.6"
                     Fill="Green"/>

            <Ellipse Width="21.835" Height="21.862"
                     Canvas.Left="64.6723"
                     Canvas.Top="96.8411" Stretch="Fill"
                     Opacity="0.5"
                     Fill="Green"/>

            <Ellipse Width="21.835" Height="21.862"
                     Canvas.Left="87.6176"
                     Canvas.Top="81.2783" Stretch="Fill"
                     Opacity="0.4"
                     Fill="Green"/>

            <Ellipse Width="21.835" Height="21.862"
                     Canvas.Left="98.165"
                     Canvas.Top="54.414" Stretch="Fill"
                     Opacity="0.3"
                     Fill="Green"/>

            <Ellipse Width="21.835" Height="21.862"
                     Canvas.Left="92.9838"
                     Canvas.Top="26.9938" Stretch="Fill"
                     Opacity="0.2"
                     Fill="Green"/>

            <Ellipse Width="21.835" Height="21.862"
                     Canvas.Left="47.2783"
                     Canvas.Top="0.5" Stretch="Fill"
                     Opacity="0.1"
                     Fill="Green"/>

            <Canvas.RenderTransform>
                <RotateTransform x:Name="SpinnerRotate"
                                 Angle="0" />
            </Canvas.RenderTransform>

            <Canvas.Triggers>
                <EventTrigger RoutedEvent="local:CircularProgressBar.StartSpinner">
                    <BeginStoryboard>
                        <Storyboard>
                            <DoubleAnimation Storyboard.TargetName="SpinnerRotate"
                                            Storyboard.TargetProperty="(RotateTransform.Angle)"
                                            From="0" To="360"
                                            Duration="0:0:01"
                                            RepeatBehavior="Forever" />
                        </Storyboard>
                    </BeginStoryboard>
                </EventTrigger>
                <EventTrigger RoutedEvent="local:CircularProgressBar.StopSpinner">
                    <BeginStoryboard>
                        <Storyboard>
                            <DoubleAnimation Storyboard.TargetName="SpinnerRotate"
                                             Storyboard.TargetProperty="(RotateTransform.Angle)"
                                             To="360"
                                             Duration="0:0:0" />
                        </Storyboard>
                    </BeginStoryboard>
                </EventTrigger>
            </Canvas.Triggers>
        </Canvas>
    </Grid>
</Viewbox>
```

```c#
namespace Spinner
{
    /// <summary>
    /// Interaction logic for CircularProgressBar.xaml
    /// </summary>
    public partial class CircularProgressBar : Viewbox
    {
        public static readonly RoutedEvent StartEvent =
            EventManager.RegisterRoutedEvent("StartSpinner",
                                             RoutingStrategy.Bubble,
                                             typeof(RoutedEventHandler),
                                             typeof(CircularProgressBar));
        public static readonly RoutedEvent StopEvent =
            EventManager.RegisterRoutedEvent("StopSpinner",
                                             RoutingStrategy.Bubble,
                                             typeof(RoutedEventHandler),
                                             typeof(CircularProgressBar));

        public CircularProgressBar()
        {
            InitializeComponent();
            this.DataContext = this;
        }

        public event RoutedEventHandler StartSpinner
        {
            add { AddHandler(StartEvent, value); }
            remove { RemoveHandler(StartEvent, value); }
        }

        public event RoutedEventHandler StopSpinner
        {
            add { AddHandler(StopEvent, value); }
            remove { RemoveHandler(StopEvent, value); }
        }

        public void Show()
        {
            RoutedEventArgs newEventArgs = new RoutedEventArgs(StartEvent);
            LayoutCanvas.RaiseEvent(newEventArgs);

            this.Visibility = Visibility.Visible;
        }

        public void Hide()
        {
            RoutedEventArgs newEventArgs = new RoutedEventArgs(StopEvent);
            LayoutCanvas.RaiseEvent(newEventArgs);

            this.Visibility = Visibility.Hidden;
        }
    }
}
```

Enjoy!