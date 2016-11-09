<!---
  category: Threading
  language: cs
-->

# Update the UI after a delay

Performs the specified action after the specified number of milliseconds. 

```C#
using System;
using System.Threading.Tasks;
using Windows.ApplicationModel.Core;

public static void DoAfterDelay(int millisecondsDelay, Action action)
{
    var withoutAwait = CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
        Windows.UI.Core.CoreDispatcherPriority.Normal,
        async () => { await Task.Delay(millisecondsDelay); action(); });
}
```

Although DoAfterDelay uses async in its implementation, it is not an async method. This is because
callers do not need to await any results. Therefore, it does not await its RunAsync call and does not 
return a Task. (The withoutAwait variable is present only to suppress the warning about not using "await".) 
DoAfterDelay could be an "async void" method, but this is not recommended except with event handlers 
because callers expect to be able to await async methods.  

## Usage

One scenario where this method is useful is to hide a temporary message after displaying it briefly. 

```C#
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

// Normally declared in XAML.
TextBlock AlertMessage { get; } = new TextBlock { Text = "Alert!" };

public void ShowAlertForFiveSeconds()
{
    AlertMessage.Visibility = Visibility.Visible;
    DoAfterDelay(5000, () => AlertMessage.Visibility = Visibility.Collapsed);
}
``` 

## See also

[CoreDispatcher.RunAsync method](https://msdn.microsoft.com/library/windows/apps/windows.ui.core.coredispatcher.runasync.aspx)  

[Task.Delay method](https://msdn.microsoft.com/library/hh139096.aspx)  
[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  

This method is used by the [RSS Reader](https://github.com/Microsoft/Windows-appsample-rssreader/blob/master/RssReader/ViewModels/MainViewModel.cs#L257-L266) 
sample to hide a temporary message.
