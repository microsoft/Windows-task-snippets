# Launch executable

Launches the default app or other executable (.exe or .bat file) associated with the specified filename extension.  

The exeId parameter specifies the extension. You must also have configured the association
between the custom file type and the target executable, either manually or through Group Policy.
If the association is not set when LaunchExeAsync is run, the system will prompt the user to select 
any program of their choice. 

The prompt parameter specifies whether to warn the user that the current app is trying to switch apps 
or whether to proceed silently. 

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.System;

public static async Task LaunchExeAsync(string exeId, bool prompt = true)
{
    exeId = "." + exeId;
    StorageFile file = null;
    try
    {
        file = await ApplicationData.Current.LocalFolder.GetFileAsync(exeId);
    }
    catch (FileNotFoundException)
    {
        file = await ApplicationData.Current.LocalFolder.CreateFileAsync(exeId);
    }
    await Launcher.LaunchFileAsync(file, new LauncherOptions { TreatAsUntrusted = prompt });
}
```

This method is primarily useful when you can configure the file type association 
ahead of time, for example with internal testing or with line-of-business (LOB) apps where 
you have some control over your users' systems. It works by first creating a blank file with the specified custom extension, 
then using the Launcher class to "open" the file using the associated executable. Although the 
file is blank, so there is nothing to actually open, the executable is launched anyway.  

## See also

[Launcher class](https://msdn.microsoft.com/library/windows/apps/windows.system.launcher)  
[Launcher.LaunchFileAsync method](https://msdn.microsoft.com/library/windows/apps/hh701465.aspx)  
  