---
layout: post
title:  "Checkbox Column in Datagrid"
categories: "wpf xaml datagrid checkbox"
---

Have you noticed that the DataGridCheckBoxColumn in the DataGrid requires double click to check or uncheck the checkbox? It is really a bizzare behavior. The simplest way to get around it is to apply some styles to the CheckBox. For instance, setting its alignment to center or its clickmode to press. By setting any property on the checkbox, you are able to get rid of the double click requirement.

```xaml
<DataGridCheckBoxColumn Binding="{Binding Path=Selected,
                                          Mode=TwoWay,
                                          UpdateSourceTrigger=PropertyChanged}"
                        Width="30">
    <DataGridCheckBoxColumn.ElementStyle>
        <Style TargetType="{x:Type CheckBox}">
            <Setter Property="HorizontalAlignment" Value="Center"/>
            <Setter Property="ClickMode" Value="Press"/>
        </Style>
    </DataGridCheckBoxColumn.ElementStyle>
</DataGridCheckBoxColumn>
```

In addition, you can add a select all checkbox to the header of the column. It's a convenient way for the user to choose all or unchoose all.

![Cascading Selector](/assets/2017-03-21-datagrid-checkbox-all.png "checkall")


```xaml
<DataGridCheckBoxColumn Binding="{Binding Path=Selected,
                                          Mode=TwoWay,
                                          UpdateSourceTrigger=PropertyChanged}"
                        Width="30">
    <DataGridCheckBoxColumn.HeaderTemplate>
        <DataTemplate>
            <CheckBox x:Name="CheckAll" HorizontalAlignment="Center"/>
        </DataTemplate>
    </DataGridCheckBoxColumn.HeaderTemplate>
</DataGridCheckBoxColumn>
```