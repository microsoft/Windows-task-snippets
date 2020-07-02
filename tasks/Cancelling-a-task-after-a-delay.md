<!---
  category: Tasks
  language: cs
-->

# Cancelling a task after a delay

Provides a convenient way to cancel a task if it
runs longer than a specified period of time.

```C#
using System;
using System.Threading.Tasks;
using Windows.Foundation;

public static class Helpers
{
    static async void CancelAfterHelper(IAsyncInfo info, TimeSpan delay)
    {
        await Task.Delay(delay);
        info.Cancel();
    }

    public static IAsyncAction CancelAfter(this IAsyncAction action, TimeSpan delay)
    {
        CancelAfterHelper(action, delay);
        return action;
    }

    public static IAsyncOperation<T> CancelAfter<T>(this IAsyncOperation<T> op, TimeSpan delay)
    {
        CancelAfterHelper(op, delay);
        return op;
    }
}
```

## Usage

The following usage snippet shows how to display a dialog box for up to 30 seconds.

```C#
using Windows.UI.Popups;
using Windows.UI.Xaml;

private async Task<bool> IsUserStillThereAsync()
{
    var dialog = new MessageDialog("Are you still there?")
    {
        Commands = { new UICommand("Still here") }
    };
    try
    {
        await dialog.ShowAsync().CancelAfter(TimeSpan.FromSeconds(30));
        return true; // user responded to the dialog
    }
    catch (TaskCanceledException)
    {
        return false; // user did not respond to the dialog
    }
}
```

## See also

[MessageDialog class](https://msdn.microsoft.com/library/windows/apps/br208674)  
[UICommand class](https://msdn.microsoft.com/library/windows/apps/windows.ui.popups.uicommand.aspx)  
