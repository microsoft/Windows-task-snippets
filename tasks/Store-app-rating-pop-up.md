# Show a pop-up asking the user to rate the app on its Store page

Displays a pop-up message that asks the user to rate the app. If the user agrees, 
they are redirected to the app's store page to indicate their rating. 

```C#
using System;
using System.Threading.Tasks;
using Windows.ApplicationModel;
using Windows.System;
using Windows.UI.Popups;

public static async Task ShowStoreRatingDialogAsync(string message,
    string okButtonText = "OK", string cancelButtonText = "Cancel")
{
    Action handler = async () => await Launcher.LaunchUriAsync(new Uri(
        $"ms-windows-store:REVIEW?PFN={Package.Current.Id.FamilyName}"));
    var messageDialog = new MessageDialog(message) { CancelCommandIndex = 1 };
    messageDialog.Commands.Add(new UICommand(okButtonText, command => handler()));
    messageDialog.Commands.Add(new UICommand(cancelButtonText));
    await messageDialog.ShowAsync();
}
```

## See also

[MessageDialog class](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.popups.messagedialog.aspx)  
[UICommand class](https://msdn.microsoft.com/library/windows/apps/windows.ui.popups.uicommand.aspx)  
[Launcher class](https://msdn.microsoft.com/library/windows/apps/windows.system.launcher)  
[Launcher.LaunchUriAsync methods](https://msdn.microsoft.com/library/windows/apps/windows.system.launcher.launchuriasync.aspx)  
[Interpolated strings](https://msdn.microsoft.com/library/dn961160.aspx) (strings with a $ prefix)  
