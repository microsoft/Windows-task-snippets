# Time-specific salutation

Generates a simple greeting based on the time of day.

```C#
public static string Salutation()
{
    var now = System.DateTime.Now;
    return
        now.Hour < 12 ? "Good morning" :
        now.Hour < 18 ? "Good afternoon" :
        now.Hour < 21 ? "Good evening" :
        /* otherwise */ "Good night";
}
```

## Usage

```C#
 System.Diagnostics.Debug.WriteLine(Salutation());
 ```
 
 ## See also

[DateTime](https://msdn.microsoft.com/library/windows/apps/windows.foundation.datetime.aspx)  
[?: operator](https://msdn.microsoft.com/library/ty67wk28.aspx)  