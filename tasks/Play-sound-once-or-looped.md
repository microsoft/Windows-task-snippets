# Play a sound once or looped

Plays a sound, either once or continuously.

```C#
using System;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.UI.Xaml.Controls;

public static async Task PlayAudioAsync(IStorageFile mediaFile, bool looping = false) 
{
    var stream = await mediaFile.OpenAsync(FileAccessMode.Read).AsTask();
    var mediaControl = new MediaElement() { IsLooping = looping };
    mediaControl.SetSource(stream, mediaFile.ContentType);
    mediaControl.Play();
}
```

The sound is passed in as an IStorageFile. You can load your sound file as a 
StorageFile object by using a method like GetFileFromApplicationUriAsync. 

There are several ways to play sound in a Universal Windows Platform (UWP) app. This method 
uses the MediaElement class. The AudioGraph class provides additional control over playing sound. 

## Usage

```C#
var soundFile = await StorageFile.GetFileFromApplicationUriAsync(
    new Uri("ms-appx:///Assets/test.mp3"));
await PlayAudioAsync(soundFile);
```

## See also

[IStorageFile interface](https://msdn.microsoft.com/library/windows/apps/windows.storage.istoragefile.aspx)  
[StorageFile class](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefile.aspx)  
[StorageFile.GetFileFromApplicationUriAsync method](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefile.getfilefromapplicationuriasync.aspx)   
[MediaElement class](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaelement.aspx)  
[AudioGraph class](https://msdn.microsoft.com/library/windows/apps/windows.media.audio.audiograph.aspx)  

