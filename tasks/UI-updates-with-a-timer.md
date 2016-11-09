<!---
  category: Threading
  language: cs
-->

# Update the UI from a periodic timer

Performs the specified action at the specified interval in minutes. 

```C#
using System;
using Windows.UI.Xaml;

public static void StartTimer(int intervalInMinutes, Action action)
{
    var timer = new DispatcherTimer();
    timer.Interval = new TimeSpan(0, intervalInMinutes, 0);
    timer.Tick += (s, e) => action();
    timer.Start();
}
```

## Usage

```C#
StartTimer(5, () =>
{
    // Do something every five minutes.
});
```

## See also

[DispatcherTimer class](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.dispatchertimer.aspx)  
[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  

The [TrafficApp sample](https://github.com/Microsoft/Windows-appsample-trafficapp) uses this method to 
[update location travel info and freshness timestamps](https://github.com/Microsoft/Windows-appsample-trafficapp/blob/master/TrafficApp/MainPage.xaml.cs#L121-L125).  