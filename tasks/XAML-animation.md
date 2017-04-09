# Create animations using XAML

Animates the specified property of a certain target. 

When started (in the code-behind) the `greetingOutput` fades out, followed by the `inputButton` dimming. This snippet is based off the ["Hello World" app for C#/XAML](https://msdn.microsoft.com/en-us/windows/uwp/get-started/create-a-hello-world-app-xaml-universal).

```xaml
<StackPanel x:Name="contentPanel">
  <StackPanel.Resources>
    <Storyboard x:Name="ButtonClicked">
      <DoubleAnimation Storyboard.TargetName="greetingOutput"
                       Storyboard.TargetProperty="Opacity"
                       From="1"
                       To="0"
                       Duration="0:0:5"/>
      <DoubleAnimation Storyboard.TargetName="inputButton"
                       Storyboard.TargetProperty="Opacity"
                       To=".5"
                       BeginTime="0:0:5"
                       Duration="0:0:2"/>
    </Storyboard>
  </StackPanel.Resources>
  ...
<StackPanel>
```

## See also

[Storyboard class](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.media.animation.storyboard)  
["Hello World" app for C#/XAML](https://msdn.microsoft.com/en-us/windows/uwp/get-started/create-a-hello-world-app-xaml-universal) 
[Channel 9's Beginner Series](http://absolutebeginner.codeplex.com/)



