---
layout: post
title:  "TextBox and PasswordBox with Placeholder"
categories: "wpf xaml placeholder"
---

In HTML5, **&lt;input&gt;** element has a **placeholder** attribute, which is for specifying the placeholder text. However, in XAML, neither TextBox nor PasswordBox has the off-the-shelf placeholder attribute. To achieve a similar visual effect, a simple and common way is to layer another TextBox on top of the existing one. When nothing is inputed, display the placeholder TextBox; otherwise, hide it.

Here is an example:

![Cascading Selector](/assets/2017-03-10-text-placeholder.png "placeholder") ![Cascading Selector](/assets/2017-03-10-text-placeholder-input.png "placeholder")

The codes are like below:

```xaml
<StackPanel>
    <Grid Width="300" Margin="0, 20, 0, 0" FocusManager.FocusedElement="{Binding ElementName=UsernameBox}">
        <Grid.RowDefinitions>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <TextBox Grid.Row="0"
                 x:Name="UsernameBox"
                 Text="{Binding Path=Username,
                                Mode=TwoWay,
                                UpdateSourceTrigger=PropertyChanged}"
                 Background="Transparent"
                 BorderBrush="Transparent"
                 BorderThickness="0"
                 Foreground="Black"
                 Panel.ZIndex="2"
                 CaretBrush="DimGray"
                 FontSize="12"
                 FocusManager.IsFocusScope="True"/>

        <TextBox Grid.Row="0"
                 Text="Username"
                 Background="Transparent"
                 BorderBrush="DimGray"
                 BorderThickness="0 0 0 1"
                 FontSize="12"
                 FocusManager.IsFocusScope="False">
            <TextBox.Style>
                <Style TargetType="{x:Type TextBox}">
                    <Style.Triggers>
                        <DataTrigger Binding="{Binding Path=Username,
                                                       Mode=OneWay,
                                                       Converter={StaticResource Empty2BoolConverter}}"
                                                       Value="False">
                            <Setter Property="Foreground" Value="Transparent"/>
                        </DataTrigger>
                        <DataTrigger Binding="{Binding Path=Username,
                                                       Mode=OneWay,
                                                       Converter={StaticResource Empty2BoolConverter}}"
                                                       Value="True">
                            <Setter Property="Foreground" Value="DimGray"/>
                        </DataTrigger>
                    </Style.Triggers>
                </Style>
            </TextBox.Style>
        </TextBox>
    </Grid>

    <Grid Width="300" Margin="0, 20, 0, 0" FocusManager.FocusedElement="{Binding ElementName=UserPasswordBox}">
        <Grid.RowDefinitions>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <PasswordBox Grid.Row="0"
                     x:Name="UserPasswordBox"
                     Background="Transparent"
                     BorderBrush="Transparent"
                     BorderThickness="0"
                     Foreground="Black"
                     Panel.ZIndex="2"
                     CaretBrush="DimGray"
                     FontSize="12"
                     FocusManager.IsFocusScope="True"
                     PasswordChanged="OnPasswordChange"/>

        <TextBox Grid.Row="0"
                 Text="Password"
                 Background="Transparent"
                 BorderBrush="DimGray"
                 BorderThickness="0 0 0 1"
                 FontSize="12"
                 FocusManager.IsFocusScope="False">
            <TextBox.Style>
                <Style TargetType="{x:Type TextBox}">
                    <Style.Triggers>
                        <DataTrigger Binding="{Binding Path=Password,
                                                       Mode=OneWay,
                                                       Converter={StaticResource Empty2BoolConverter}}"
                                                       Value="False">
                            <Setter Property="Foreground" Value="Transparent"/>
                        </DataTrigger>
                        <DataTrigger Binding="{Binding Path=Password,
                                                       Mode=OneWay,
                                                       Converter={StaticResource Empty2BoolConverter}}"
                                                       Value="True">
                            <Setter Property="Foreground" Value="DimGray"/>
                        </DataTrigger>
                    </Style.Triggers>
                </Style>
            </TextBox.Style>
        </TextBox>
    </Grid>
</StackPanel>
```

There are a few things to pay attention to in the above codes. First, the FocusManager.FocusedElement of the parent Grid container should be set to the input TextBox. Otherwise, when tabbing between the TextBoxes, the focus behavior is incorrect. Second, the string to boolean value converter is defined in the resource dictionary. The converter class is like below:

```cs
public class IsEmptyConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return string.IsNullOrEmpty((string)value);
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        throw new InvalidOperationException("IsEmptyConverter can only be used OneWay.");
    }
}
```

Enjoy!