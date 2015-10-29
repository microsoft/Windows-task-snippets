# Determine whether a file exists

Determines whether a file exists in a given folder. If no folder is provided, the app's local storage
is used by default. You can also specify whether to recursively search subdirectories or 
only the top-level folder. The file name should include the extension, but is not case-sensitive.  

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.Storage.Search;

public static async Task<bool> FileExistsAsync(string fileName, bool isRecursive, StorageFolder folder = null)
{
    folder = folder ?? ApplicationData.Current.LocalFolder;
    IReadOnlyList<StorageFile> files = isRecursive ?
        await folder.GetFilesAsync(CommonFileQuery.OrderByName) :
        await folder.GetFilesAsync(CommonFileQuery.DefaultQuery);
    return files.Any(x => x.Name.Equals(x.Name, StringComparison.OrdinalIgnoreCase)); 
}
```

## See also

[StorageFolder class](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefolder.aspx)  
[StorageFile class](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefile.aspx)  

[?? operator](https://msdn.microsoft.com/library/ms173224.aspx)  
[?: operator](https://msdn.microsoft.com/library/ty67wk28.aspx)  
[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
