<!---
  category: Threading
  language: cs
-->

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

public static async Task CallOnUiThreadAsync(CoreDispatcher dispatcher, DispatchedHandler handler) =>
    await dispatcher.RunAsync(CoreDispatcherPriority.Normal, handler);
    
public static async Task CallOnMainViewUiThreadAsync(DispatchedHandler handler) => 
    await CallOnUiThreadAsync(CoreApplication.MainView.CoreWindow.Dispatcher, handler);
```

## Usage

```C#
// Single-view app can assume the UI thread is the main view (since there is only one).
private async void NetworkInformation_NetworkStatusChanged(object sender)
{
    await CallOnMainViewUiThreadAsync(() =>
    {
        // Update the UI to reflect the current network status. 
    });
}

// Multi-view app should use the dispatcher for the UI thread they want to run on.
private async void NetworkInformation_NetworkStatusChanged(object sender)
{
    await CallOnUiThreadAsync(this.Dispatcher, () =>
    {
        // Update this page's UI to reflect the current network status.
    }
}
```

## See also

[CoreDispatcher class](https://msdn.microsoft.com/library/windows/apps/windows.ui.core.coredispatcher.aspx)  
[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
