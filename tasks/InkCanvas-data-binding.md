<!---
  category: CustomUserInteractions Inking
  language: cs
-->

# Data-bind an InkCanvas control

Enables you to use XAML data binding with ink stroke data in an InkCanvas control.

The InkCanvas class does not expose a dependency property for its ink stroke data, so there is no built-in target for data binding. 
This snippet provides a InkStrokes attached property that you can use to bind to an InkStrokeContainer. This enables you to manipulate 
the ink stroke data programmatically (for example, to deserialize it from storage) and have the changes appear in the UI automatically. 

```C#
using Windows.UI.Input.Inking;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

public static class InkCanvasBinder
{
    public static InkStrokeContainer GetInkStrokes(DependencyObject obj) => 
        obj.GetValue(InkStrokesProperty) as InkStrokeContainer;

    public static void SetInkStrokes(DependencyObject obj, InkStrokeContainer value) => 
        obj.SetValue(InkStrokesProperty, value);

    public static DependencyProperty InkStrokesProperty = DependencyProperty.RegisterAttached(
        "InkStrokes", typeof(InkStrokeContainer), typeof(InkCanvasBinder),
        new PropertyMetadata(null, InkStrokesProperty_PropertyChanged));

    private static void InkStrokesProperty_PropertyChanged(DependencyObject d, DependencyPropertyChangedEventArgs e)
    {
        var inkCanvas = d as InkCanvas;
        if (inkCanvas != null) inkCanvas.InkPresenter.StrokeContainer = e.NewValue as InkStrokeContainer;
    }
}
```

## Usage

In this example, the InkCanvas is bound to a read-only MyInkStrokeContainer property of type InkStrokeContainer defined in the page's code-behind file. 

```xaml
<InkCanvas local:InkCanvasBinder.InkStrokes="{x:Bind AnInkStrokeContainer}" />
```

## See also

[InkCanvas class](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inkcanvas.aspx)  
[InkStrokeContainer class](https://msdn.microsoft.com/library/windows/apps/windows.ui.input.inking.inkstrokecontainer.aspx)  

[Dependency properties overview](https://msdn.microsoft.com/windows/uwp/xaml-platform/dependency-properties-overview)  
[Expression-bodied function members](http://blogs.msdn.com/b/csharpfaq/archive/2014/11/20/new-features-in-c-6.aspx) (methods and properties with the "=>" syntax)  
[x:Bind markup extension](https://msdn.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension)  
