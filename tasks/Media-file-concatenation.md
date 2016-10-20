# Concatenate media files

Concatenate two or more media files and save the result to a file using the specified preferences. 

This method defaults to encoding MP3 files, but works with media files in a variety of formats, including video files. 

```C#
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;
using Windows.ApplicationModel.Core;
using Windows.Media.Editing;
using Windows.Media.MediaProperties;
using Windows.Storage;
using Windows.UI.Core;

public static class Helpers
{
    public static async Task ConcatenateMediaFilesAsync(this IEnumerable<StorageFile> inputFiles,
        StorageFile outputFile, MediaEncodingProfile encodingProfile = null,
        MediaTrimmingPreference trimmingPreference = MediaTrimmingPreference.Fast)
    {
        encodingProfile = encodingProfile ?? MediaEncodingProfile.CreateMp3(AudioEncodingQuality.High);
        var composition = new MediaComposition();
        try
        {
            inputFiles.ToList().ForEach(async file =>
                composition.Clips.Add(await MediaClip.CreateFromFileAsync(file)));

            var transcodeOperation = composition.RenderToFileAsync(
                outputFile, trimmingPreference, encodingProfile);

            transcodeOperation.Progress = async (transcodeResult, progress) =>
                await CoreApplication.MainView.Dispatcher.RunAsync(CoreDispatcherPriority.Normal,
                    () => { Debug.WriteLine($"Transcode progress: {progress}"); });

            var result = await transcodeOperation;
            Debug.WriteLine($"Transcode result: {result.ToString()}");
        }
        catch (Exception e)
        {
            Debug.WriteLine($"Exception during transcoding: {e.Message}");
        }
    }
}
```

## Usage

The following usage snippet shows how to concatenate MP3 files selected by the user. (Note that ConcatenateMediaFilesAsync 

```C#
using System;
using System.Diagnostics;
using Windows.Storage;
using Windows.Storage.Pickers;
using Windows.UI.Xaml;

private async void Button_Click(object sender, RoutedEventArgs e)
{
    var picker = new FileOpenPicker() { SuggestedStartLocation = PickerLocationId.MusicLibrary };
    picker.FileTypeFilter.Add(".mp3");

    var pickedFiles = await picker.PickMultipleFilesAsync();
    if (pickedFiles == null) { Debug.WriteLine("File picking cancelled"); return; }

    var outputFile = await ApplicationData.Current.LocalFolder.CreateFileAsync(
        "test.mp3", CreationCollisionOption.ReplaceExisting);

    await pickedFiles.ConcatenateMediaFilesAsync(outputFile);
}
```

## See also

[MediaComposition class](https://msdn.microsoft.com/library/windows/apps/windows.media.editing.mediacomposition.aspx)  
[MediaClip class](https://msdn.microsoft.com/library/windows/apps/windows.media.editing.mediaclip.aspx)  
[MediaEncodingProfile](https://msdn.microsoft.com/library/windows/apps/windows.media.mediaproperties.mediaencodingprofile.aspx)  

[?? operator](https://msdn.microsoft.com/library/ms173224.aspx)  
[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
[Extension methods](https://msdn.microsoft.com/en-us/library/bb383977.aspx)  
[Interpolated strings](https://msdn.microsoft.com/library/dn961160.aspx) (strings with a $ prefix)  
