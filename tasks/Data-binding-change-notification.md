# Implement change notification

Provides a simple, standard change-notification implementation.
 
All one-way and two-way data bindings depend on change notification to refresh 
the data-bound controls. This typically involves a lot of boilerplate code that
you can minimize by using this task snippet.

To implement change notification for non-collection properties:
1. Either derive your class from this class, or use its contents to implement INotifyPropertyChanged on your class. (This 
is useful for classes that already derive from something else, such as Page.)
2. Implement your binding source properties using the pattern shown in the [Usage](#usage) section.  

```C#
using System;
using System.ComponentModel;
using System.Runtime.CompilerServices;

public abstract class BindableBase : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }

    protected bool SetProperty<T>(ref T storage, T value,
        [CallerMemberName] String propertyName = null)
    {
        if (object.Equals(storage, value)) return false;
        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }
}
```

The CallerMemberName attribute ensures that the propertyName parameter is set to the name
of the calling property unless the caller passes a value explicitly.

## Usage

```C#
public class TimestampInfo : BindableBase
{
    private DateTimeOffset _timestamp;

    // Read/write property for which any changes must refresh the 
    // UI controls bound to this property and to a dependent property.   
    public DateTimeOffset Timestamp
    {
        get { return _timestamp; }
        set
        {
            // Update the backing field and raise PropertyChanged for this property.
            SetProperty(ref _timestamp, value);

            // Raise PropertyChanged for a read-only property that depends on this property. 
            OnPropertyChanged(nameof(FormattedTimestamp));
        }
    }

    // Read-only property that depends on another property 
    // for both its value and for change notification. 
    public string FormattedTimestamp
    {
        get
        {
            double minutesAgo = Timestamp == DateTimeOffset.MinValue ? 
                0 : Math.Floor((DateTimeOffset.Now - Timestamp).TotalMinutes);
            return $"{minutesAgo} minute{(minutesAgo == 1 ? "" : "s")} ago";
        }
    }
    
    // Method that enables other code (such as a periodic timer) to raise PropertyChanged for FormattedTimestamp.  
    public void RefreshFormattedTimestamp() => OnPropertyChanged(nameof(FormattedTimestamp));
}
```

The nameof operator lets you pass the name of a property without using a string constant. 
This helps avoid a common bug caused when you rename a property but fail to update its string representation. 

## See also

When binding to a collection property, use [ObservableCollection<T>](https://msdn.microsoft.com/library/ms668604.aspx) 
as your property type, which provides a standard [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx)
implementation.

[Data binding](https://msdn.microsoft.com/library/windows/apps/mt210947.aspx)  
[INotifyPropertyChanged interface](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.inotifypropertychanged.propertychanged.aspx)  

[CallerMemberNameAttribute class](https://msdn.microsoft.com/library/system.runtime.compilerservices.callermembernameattribute.aspx)  
[nameof expression](https://msdn.microsoft.com/library/dn986596.aspx)  

[Interpolated strings](https://msdn.microsoft.com/library/dn961160.aspx) (strings with a $ prefix)  
[Expression-bodied function members](http://blogs.msdn.com/b/csharpfaq/archive/2014/11/20/new-features-in-c-6.aspx) (methods and properties with the "=>" syntax)  
[?. operator](https://msdn.microsoft.com/library/dn986595.aspx)  
[?: operator](https://msdn.microsoft.com/library/ty67wk28.aspx)  

  