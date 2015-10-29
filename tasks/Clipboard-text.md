# Get or set clipboard text

Gets or sets the current clipboard text. 

If the clipboard contains a non-text item (like an image), GetClipboardTextAsync returns an empty string. 

```C#
using System;
using System.Threading.Tasks;
using Windows.ApplicationModel.DataTransfer;

public static async Task<string> GetClipboardTextAsync()
{
    DataPackageView dataPackage = Clipboard.GetContent();
    return dataPackage.Contains(StandardDataFormats.Text) ? 
        await dataPackage.GetTextAsync() : ""; 
}

public static void SetClipboardText(string text)
{
    var dataPackage = new DataPackage();
    dataPackage.SetText(text);
    Clipboard.SetContent(dataPackage);
}
```

## See also

[Clipboard class](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.clipboard.aspx)  
[DataPackage class](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datapackage.aspx)  
[DataPackageView class](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datapackageview.aspx)  
[?: operator](https://msdn.microsoft.com/library/ty67wk28.aspx)  

For more advanced clipboard interactions, including handling non-text objects or firing events when the clipboard 
contents change, see [Copy and paste](https://msdn.microsoft.com/library/windows/apps/mt243291.aspx).

