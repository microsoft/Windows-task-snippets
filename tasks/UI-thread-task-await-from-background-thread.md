# Await a UI task sent from a background thread

Enables code running on a background thread to await a task that must run on the UI thread.  

Normally, you can update your UI from a background thread 
by calling `Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => /* update the UI */)`. 
However, this merely schedules the work on the UI thread and returns immediately, 
even if the task is to await user input in a popup box.
    
This method uses TaskCompletionSource in combination with RunAsync to return a Task 
that you can await from your background thread, thereby pausing execution until the UI task completes.   

```C#
using System;
using System.Threading.Tasks;
using Windows.UI.Core;

public static Task RunAwaitableAsync(CoreDispatcher dispatcher, Func<Task> task)
{
    var completion = new TaskCompletionSource<bool>();
    var action = dispatcher.RunAsync(CoreDispatcherPriority.Normal, async () =>
    {
        try
        {
            await task.Invoke();
            completion.TrySetResult(true);
        }
        catch (Exception e)
        {
            completion.TrySetException(e);
        }
    });
    return completion.Task;
}
```

## Usage

```C#
public async Task MyMethod()
{
    System.Diagnostics.Debug.WriteLine("MyMethod() is running");
    await RunAwaitableAsync(CoreWindow.GetForCurrentThread().Dispatcher, async () =>
    {
        var popup = new Windows.UI.Popups.MessageDialog("Alert", 
            "MyMethod() is paused. Tap 'Close' to continue running MyMethod()");
        await popup.ShowAsync();
    });
    System.Diagnostics.Debug.WriteLine("MyMethod() is running again");
}
```

## See also

[CoreDispatcher class](https://msdn.microsoft.com/library/windows/apps/windows.ui.core.coredispatcher.aspx)  
[CoreDispatcher.RunAsync method](https://msdn.microsoft.com/library/windows/apps/windows.ui.core.coredispatcher.runasync.aspx)  
[TaskCompletionSource<TResult> class](https://msdn.microsoft.com/library/dd449174.aspx)  
[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
