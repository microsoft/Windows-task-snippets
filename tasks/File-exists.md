# Determine whether a file exists

Determines whether a file exists in a given folder or a folder subtree. The file name should include the extension, but is not case-sensitive.  

```C#
using System;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.Storage.Search;

public static async Task<bool> FileExistsInFolderAsync(StorageFolder folder, string filename)
{
    var item = await folder.TryGetItemAsync(filename);
    return (item != null) && item.IsOfType(StorageItemTypes.File);
}

public static async Task<bool> FileExistsInSubtreeAsync(StorageFolder rootFolder, string filename)
{
    if (filename.IndexOf('"') >= 0) throw new ArgumentException("filename");
    var options = new QueryOptions
    {
        FolderDepth = FolderDepth.Deep,
        UserSearchFilter = $"filename:=\"{filename}\"" // “:=” is the exact-match operator
    };
    var files = await rootFolder.CreateFileQueryWithOptions(options).GetFilesAsync();
    return files.Count > 0;
}

public static async Task<bool> FileExistsAsync(StorageFolder folder, string filename, 
    bool isRecursive = false) => isRecursive 
        ? await FileExistsInSubtreeAsync(folder, filename) 
        : await FileExistsInFolderAsync(folder, filename);
```

## See also

[StorageFolder class](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefolder.aspx)  
[StorageFile class](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefile.aspx)  

[?? operator](https://msdn.microsoft.com/library/ms173224.aspx)  
[?: operator](https://msdn.microsoft.com/library/ty67wk28.aspx)  
[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
