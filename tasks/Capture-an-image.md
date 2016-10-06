# Capture an image

Captures an image using the device's camera and returns it as a bitmap.

```C#
using System;
using System.Threading.Tasks;
using Windows.Media.Capture;
using Windows.Storage;
using Windows.Storage.Streams;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media.Imaging;

public async Task<BitmapImage> CaptureImage(bool allowCropping = true, 
    CameraCaptureUIPhotoFormat photoFormat = CameraCaptureUIPhotoFormat.Jpeg, 
    CameraCaptureUIMaxPhotoResolution maxPhotoResolution = CameraCaptureUIMaxPhotoResolution.HighestAvailable)
{
    var captureUI = new CameraCaptureUI();
    captureUI.PhotoSettings.AllowCropping = allowCropping;
    captureUI.PhotoSettings.Format = photoFormat;
    captureUI.PhotoSettings.MaxResolution = maxPhotoResolution;
    StorageFile file = await captureUI.CaptureFileAsync(CameraCaptureUIMode.Photo);
    if (file == null) return null;
    using (IRandomAccessStream ras = await file.OpenAsync(FileAccessMode.Read))
    {
        var bitmapImage = new BitmapImage();
        bitmapImage.SetSource(ras);
        return bitmapImage;
    }
}
```

## Usage

```C#
Image img = new Image 
{
    Source = await CaptureImage()
};
```

## See also

[CameraCaptureUI class](https://msdn.microsoft.com/library/windows/apps/xaml/windows.media.capture.cameracaptureui.aspx)  
[StorageFile class](https://msdn.microsoft.com/en-us/library/windows/apps/windows.storage.storagefile.aspx)  
[BitmapImage class](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.imaging.bitmapimage.aspx)  
[Image class](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.image.aspx)  
