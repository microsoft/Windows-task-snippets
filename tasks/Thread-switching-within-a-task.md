<!---
  category: Threading
  language: cs
-->

# Thread switching within a task

Enables code running in a task to continue
on another thread.

Code running on a background thread cannot manipulate UI elements directly
without raising a cross-thread exception.
Other task snippets accomplish this by
putting the desired code inside a lambda which you schedule to run on the UI thread
([snippet 1](UI-thread-access-from-background-thread.md),
[snippet 2](UI-thread-task-await-from-background-thread.md)).

This task snippet provides
an alternate coding pattern which encodes the thread switches
in the flow of the function.

```C#
using System;
using System.Runtime.CompilerServices;
using System.Threading;
using Windows.System;
using Windows.System.Threading;
using Windows.UI.Core;

struct DispatcherThreadSwitcher : INotifyCompletion
{
    internal DispatcherThreadSwitcher(CoreDispatcher dispatcher) =>
        this.dispatcher = dispatcher;
    public DispatcherThreadSwitcher GetAwaiter() => this;
    public bool IsCompleted => dispatcher.HasThreadAccess;
    public void GetResult() { }
    public void OnCompleted(Action continuation) =>
        _ = dispatcher.RunAsync(CoreDispatcherPriority.Normal,
                                () => continuation());
    CoreDispatcher dispatcher;
}

struct DispatcherQueueThreadSwitcher : INotifyCompletion
{
    internal DispatcherQueueThreadSwitcher(DispatcherQueue dispatcher) =>
        this.dispatcher = dispatcher;
    public DispatcherQueueThreadSwitcher GetAwaiter() => this;
    public bool IsCompleted => dispatcher.HasThreadAccess;
    public bool GetResult() => dispatcher.HasThreadAccess;
    public void OnCompleted(Action continuation) {
        if (!dispatcher.TryEnqueue(() => continuation()))
            continuation();
    }
    DispatcherQueue dispatcher;
}

struct ThreadPoolThreadSwitcher : INotifyCompletion
{
    public ThreadPoolThreadSwitcher GetAwaiter() => this;
    public bool IsCompleted =>
       SynchronizationContext.Current == null;
    public void GetResult() { }
    public void OnCompleted(Action continuation) =>
        _ = ThreadPool.RunAsync(_ => continuation());
}

class ThreadSwitcher
{
    static public DispatcherThreadSwitcher ResumeForegroundAsync(
        CoreDispatcher dispatcher) =>
        new DispatcherThreadSwitcher(dispatcher);
    static public DispatcherQueueThreadSwitcher ResumeForegroundAsync(
        DispatcherQueue dispatcher) =>
        new DispatcherQueueThreadSwitcher(dispatcher);
    static public ThreadPoolThreadSwitcher ResumeBackgroundAsync() =>
        new ThreadPoolThreadSwitcher();
}
```

## Usage

You can `await` the `ResumeForegroundAsync()` or
`ResumeBackgroundAsync()` methods
to switch the context of the task to a
foreground thread or background thread,
respectively.

The result of `await ThreadSwitcher.ResumeForegroundAsync(DispatcherQueue)` is a `bool`
that represents whether the thread switch was successful.
The other `ThreadSwitcher` methods do not provide a result.

```C#
private async Task OnStatusChanged()
{
    // Switch to the UI thread to interact with UI elements
    await ThreadSwitcher.ResumeForegroundAsync(this.Dispatcher);

    var isEnabled = EnabledCheckBox.IsChecked.Value;
    var message = MessageTextBlock.Text;

    // Go back to background thread for expensive processing
    await ThreadSwitcher.ResumeBackgroundAsync();

    var result = DoExpensiveProcessing(isEnabled, message);

    // Switch to the UI thread to update with results
    await ThreadSwitcher.ResumeForegroundAsync(this.Dispatcher);

    ResultTextBlock.Text = result;
}

```

This pattern makes it easier to share variables between
the different parts of a task.
The version with explicit lambdas would look like this:

```C#
private async Task OnStatusChanged()
{
    bool isEnabled = false;
    string message = null;

    // Switch to the UI thread to interact with UI elements
    await Dispatcher.RunAsync(() =>
    {
        isEnabled = EnabledCheckBox.IsChecked.Value;
        message = MessageTextBlock.Text;
    });

    // Back on the background thread
    var result = DoExpensiveProcessing(isEnabled, message);

    // Switch to the UI thread to update with results
    await Dispatcher.RunAsync(() =>
    {
        ResultTextBlock.Text = result;
    });
}
```

Note that we had to predeclare and preinitialize
the `isEnabled` and
`message` variables so that they can be shared between
the main function and the lambda.
This can be problematic if the shared variables are of
an anonymous type.

This pattern is particularly useful when the thread switching is
conditional:

```C#
private async Task OnStatusChanged()
{
    if (requiresUI)
    {
        await ThreadSwitcher.ResumeForegroundAsync(this.Dispatcher);
    }

    /* task is on UI thread if requiresUI is true */
}

```

or if the thread switch is part of a
more complex control flow:

```C#
private async Task OnStatusChanged()
{
    int total = 0;
    for (var view in viewsToUpdate)
    {
        // Switch to each view's thread in order to call GetItemCount().
        await ThreadSwitcher.ResumeForegroundAsync(view.Dispatcher);
        total += view.GetItemCount();
    }

    // Return to background thread to finish up
    await ThreadSwitcher.ResumeBackgroundAsync();

    /* do something with the total */
}
```

The lambda-based version of the above loop would be

```C#
private async Task OnStatusChanged()
{
    int total = 0;
    for (var view in viewsToUpdate)
    {
        // Switch to each view's thread in order to call GetItemCount().
        await view.Dispatcher.RunAsync(() =>
        {
            total += view.GetItemCount();
        });
    }

    /* do something with the total */
}
```

The lambda-based version returns to the original thread
after interrogating each view,
resulting in twice as many thread switches as the
`ThreadSwitcher`-based version.

## See also

[CoreDispatcher.RunAsync method](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher.RunAsync)  
[ThreadPool.RunAsync method](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPool.RunAsync)  

[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
