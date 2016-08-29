# Creating custom keyboard shortcuts

Creates a keyboard shortcut that changes the text displayed on a button. It reads "Goodbye" if you press Ctrl+G, or "Hello!" if you press Ctrl+H (and by default).

##XAML
```xml
<Grid KeyDown="Grid_KeyDown">
    <Button x:Name="btn" HorizontalAlignment="Center" VerticalAlignment="Center" Content="Hello!"/>
</Grid>
```

##C# #
```CSharp
private bool isCtrlKeyPressed;

public MainPage()
{
    this.InitializeComponent();

    // Set the input focus to ensure that keyboard events are raised.
    this.Loaded += delegate { this.Focus(FocusState.Programmatic); };
}

private void Grid_KeyUp(object sender, KeyRoutedEventArgs e)
{
    if (e.Key == VirtualKey.Control) isCtrlKeyPressed = false;
}

private void Grid_KeyDown(object sender, KeyRoutedEventArgs e)
{
    if (e.Key == VirtualKey.Control) isCtrlKeyPressed = true;
    else if (isCtrlKeyPressed)
    {
        switch (e.Key)
        {
            case VirtualKey.G: btn.Content = "Goodbye"; break;
            case VirtualKey.H: btn.Content = "Hello!"; break;
        }
    }
}
```

## See also

[Keyboard interactions](https://msdn.microsoft.com/en-us/windows/uwp/input-and-devices/keyboard-interactions)  
[VirtualKey enumeration](https://msdn.microsoft.com/library/windows/apps/windows.system.virtualkey.aspx)
