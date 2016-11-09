<!---
  category: FilesFoldersAndLibraries
  language: cs
-->

# Preserve access to a StorageFolder

Saves access permissions to a specific StorageFolder that a user has granted access to.  

By default, Universal Windows Platform (UWP) apps cannot access most of the file system - the user must manually "pick" folders before an app can use the files.
If an app wants to access files in a particular folder repeatedly, it's a bad user experience to repeatedly ask the user for permission. 
This snippet shows how to persist permissions to a folder (even between suspend or shutdown) so an app only needs to ask the user to pick it once. 

The key parameter is an identifier (supplied by the developer) that specifies the folder to access.
    
```C#
using System;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.Storage.AccessCache;
using Windows.Storage.Pickers;

public static async Task<StorageFolder> GetOrPickAndRememberFolderAsync(string key)
{
    if (StorageApplicationPermissions.FutureAccessList.ContainsItem(key))
    {
        return await StorageApplicationPermissions.FutureAccessList.GetFolderAsync(key);
    }
    var picker = new FolderPicker();
    picker.FileTypeFilter.Add("*");
    StorageFolder folder = await picker.PickSingleFolderAsync();
    if (folder != null)
    {
        StorageApplicationPermissions.FutureAccessList.AddOrReplace(key, folder);
    }
    return folder;
}
```

## See also

[Files, folders, and libraries](https://msdn.microsoft.com/library/windows/apps/mt185399.aspx)  
[StorageFolder class](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefolder.aspx)  
[StorageApplicationPermissions class](https://msdn.microsoft.com/library/windows/apps/windows.storage.accesscache.storageapplicationpermissions.aspx)  
[FolderPicker class](https://msdn.microsoft.com/library/windows/apps/windows.storage.pickers.folderpicker.aspx)  
  