<!---
  category: Threading
  language: cs
-->

# Await a UI task sent from a background thread

Enables code running on a background thread to await a task that must run on the UI thread.  

Normally, you can update your UI from a background thread 
by calling `Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => /* update the UI */)`. 
However, this merely schedules the work on the UI thread and returns immediately, 
even if the task is to await user input in a popup box. 
It also does not provide a way for the task to return a result to the caller.

RunTaskAsync provides an alternative that uses TaskCompletionSource in combination with RunAsync to return a Task 
that you can await from your background thread, thereby pausing execution until the UI task completes. 

Because RunTaskAsync is an extension method, you call it as if it were a method on Dispatcher:
`var result = await Dispatcher.RunTaskAsync(async () => { ...; return value; });`

```C#
using System;
using System.Threading.Tasks;
using Windows.UI.Core;

public static class DispatcherTaskExtensions
{
    public static async Task<T> RunTaskAsync<T>(this CoreDispatcher dispatcher, 
        Func<Task<T>> func, CoreDispatcherPriority priority = CoreDispatcherPriority.Normal)
    {
        var taskCompletionSource = new TaskCompletionSource<T>();
        await dispatcher.RunAsync(priority, async () =>
        {
            try
            {
                taskCompletionSource.SetResult(await func());
            }
            catch (Exception ex)
            {
                taskCompletionSource.SetException(ex);
            }
        });
        return await taskCompletionSource.Task;
    }

    // There is no TaskCompletionSource<void> so we use a bool that we throw away.
    public static async Task RunTaskAsync(this CoreDispatcher dispatcher,
        Func<Task> func, CoreDispatcherPriority priority = CoreDispatcherPriority.Normal) => 
        await RunTaskAsync(dispatcher, async () => { await func(); return false; }, priority);
}
```

## Usage

```C#
public static async Task MyMethod()
{
    System.Diagnostics.Debug.WriteLine("MyMethod() is running");
    var buttonLabel = await CoreWindow.GetForCurrentThread().Dispatcher.RunTaskAsync(async () =>
    {
        var popup = new Windows.UI.Popups.MessageDialog("Alert",
            "MyMethod() is paused. Choose a button to continue running MyMethod()");
        popup.Commands.Add(new Windows.UI.Popups.UICommand("Button 1"));
        popup.Commands.Add(new Windows.UI.Popups.UICommand("Button 2"));
        popup.CancelCommandIndex = 0;
        var command = await popup.ShowAsync();
        
        // The value returned by the lambda expression will be the RunTaskAsync return value.
        return command.Label;
    });
    System.Diagnostics.Debug.WriteLine($"MyMethod() is running again. User clicked {buttonLabel}");
}
```

One situation where RunTaskAsync is handy is when you want to render a software bitmap to the screen 
from a non-ui thread, which requires using the dispatcher to run async code on the UI thread.    

```C#
using System.Threading.Tasks;
using Windows.Graphics.Imaging;
using Windows.UI.Core;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media.Imaging;

public static async Task RenderToImageElement(
    Image imageElement, SoftwareBitmap softwareBitmap)
{
    // Changes to imageElement must happen on the UI thread.
    await imageElement.Dispatcher.RunTaskAsync(async () =>
    {
        if (softwareBitmap != null)
        {
            var imageSource = new SoftwareBitmapSource();
            await imageSource.SetBitmapAsync(softwareBitmap);
            imageElement.Source = imageSource;
        }
        else
        {
            // Clear the image element.
            imageElement.Source = null;
        }
    });
}
```

## See also

[CoreDispatcher class](https://msdn.microsoft.com/library/windows/apps/windows.ui.core.coredispatcher.aspx)  
[CoreDispatcher.RunAsync method](https://msdn.microsoft.com/library/windows/apps/windows.ui.core.coredispatcher.runasync.aspx)  
[TaskCompletionSource<TResult> class](https://msdn.microsoft.com/library/dd449174.aspx)  
[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
[Extension methods](https://msdn.microsoft.com/en-us/library/bb383977.aspx)  
[Interpolated strings](https://msdn.microsoft.com/library/dn961160.aspx) (strings with a $ prefix)  

The [UWP Community Toolkit](http://docs.uwpcommunitytoolkit.com/en/master/) includes an extended version of this task snippet in the form of a DispatcherHelper class 
([source](https://github.com/Microsoft/UWPCommunityToolkit/blob/master/Microsoft.Toolkit.Uwp/Helpers/DispatcherHelper.cs), 
[docs](http://docs.uwpcommunitytoolkit.com/en/master/helpers/DispatcherHelper/)).  
