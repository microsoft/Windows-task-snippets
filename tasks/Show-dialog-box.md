<!---
  category: ControlsLayoutAndText
  language: cs
-->

# Show a dialog box

These methods provide convenient ways to show a dialog box, 
and can be called from the UI thread or from a background thread:

* [Simple dialog](#simple-dialog)  
* [Action dialog](#action-dialog)   

## Simple dialog

Shows a simple dialog box displaying a message, an optional title, 
and a "Close" button for the user to dismiss it. 

```C#
using System;
using System.Threading.Tasks;
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
using Windows.UI.Popups;

public static async Task ShowDialogAsync(string contents, string title = null)
{
    var dialog = title == null ?
        new MessageDialog(contents) { CancelCommandIndex = 0 } :
        new MessageDialog(contents, title) { CancelCommandIndex = 0 };
    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
        CoreDispatcherPriority.Normal, async () => await dialog.ShowAsync());
}
```

## Action dialog

Shows a dialog box with a message, an optional title, and two buttons for "OK" and 
"Cancel". 

This method takes a callback action that is invoked when the user presses OK. You
can also provide your own strings for the title and the two buttons.  

```C#
using System;
using System.Threading.Tasks;
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
using Windows.UI.Popups;

public static async Task ShowActionDialogAsync(string contents, Action callback,
    string title = null, string okButtonText = "OK", string cancelButtonText = "Cancel")
{
    var dialog = title == null ?
        new MessageDialog(contents) { CancelCommandIndex = 1 } :
        new MessageDialog(contents, title) { CancelCommandIndex = 1 };
    dialog.Commands.Add(new UICommand(okButtonText, command => callback()));
    dialog.Commands.Add(new UICommand(cancelButtonText));
    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
        CoreDispatcherPriority.Normal, async () => await dialog.ShowAsync());
}
```

### Usage

```C#
await ShowActionDialogAsync("Do you really want to delete all items?", 
    () => { /* delete all items */ }, "Warning");
```

## See also

[MessageDialog class](https://msdn.microsoft.com/library/windows/apps/br208674)  
[UICommand class](https://msdn.microsoft.com/library/windows/apps/windows.ui.popups.uicommand.aspx)  
[CoreDispatcher.RunAsync method](https://msdn.microsoft.com/library/windows/apps/windows.ui.core.coredispatcher.runasync.aspx)  
[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
[?: operator](https://msdn.microsoft.com/library/ty67wk28.aspx)  
