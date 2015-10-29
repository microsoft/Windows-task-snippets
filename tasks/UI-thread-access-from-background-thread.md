# Update the UI thread from a background thread

Enables code running on a background thread to schedule work that will run on the UI thread.

Code running on a background thread cannot manipulate UI elements directly without raising 
a cross-thread exception. Instead, you must run UI code using a dispatcher. This task snippet
encapsulates the typical dispatcher usage in Universal Windows Platform (UWP) apps. 

```C#
using System;
using System.Threading.Tasks;
using Windows.ApplicationModel.Core;
using Windows.UI.Core;

public static async Task CallOnUiThreadAsync(DispatchedHandler handler) => 
    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
        CoreDispatcherPriority.Normal, handler);
```

## Usage

```C#
private async void NetworkInformation_NetworkStatusChanged(object sender)
{
    await CallOnUiThreadAsync(() =>
    {
        // Update the UI to reflect the current network status. 
    });
}
```

## See also

[CoreDispatcher class](https://msdn.microsoft.com/library/windows/apps/windows.ui.core.coredispatcher.aspx)  
[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
