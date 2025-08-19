Eine Page erbt IMMER von BasePage und schaut zumindest so aus
<base:BasePage x:Class="Sample.MainPage"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      xmlns:base="using:UnoBaseFramework.Presentation"
      Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <!--PUT CONTENT HERE-->
</base:BasePage>