<!---
  category: AppSettings 
  language: cs
-->

# Get app title and version info

Gets the title and version of an app as specified in the project manifest.  

```C#
using Windows.ApplicationModel;

public static string AppName
{
    get { return Package.Current.Id.Name; }
}

public static string AppVersion
{
    get
    {
        PackageVersion version = Package.Current.Id.Version;
        return $"{version.Major}.{version.Minor}.{version.Build}.{version.Revision}";
    }
}
```

## See also

[Package class](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.package.aspx)  
[Interpolated strings](https://msdn.microsoft.com/library/dn961160.aspx) (strings with a $ prefix)  
  