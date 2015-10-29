# Get the Bing image of the day URI

Gets the URI for the the Bing image of the day, optionally 
for the specified resolution and market. 

The market parameter defaults to "en-ww", which refers to "English - worldwide".
For other values, see the [table of language codes](http://aka.ms/languagecodes).
Some images may not be available in all markets.

The resolution parameter defaults to Unspecified, which is typically 1366x768, but may 
vary for different images. Images may not be available in other resolutions, 
so any Resolution values other than Unspecified might not produce usable URLs. 
Also, there may be other resolutions available besides the ones in the Resolution 
enumeration below. 

```C#
using System;
using System.Linq;
using System.Threading.Tasks;
using System.Xml.Linq;
using Windows.Web.Http;

public enum Resolution { Unspecified, _800x600, _1024x768, _1366x768, _1920x1080, _1920x1200 }

public static async Task<Uri> GetBingImageOfTheDayUriAsync(
    Resolution resolution = Resolution.Unspecified,
    string market = "en-ww")
{
    var request = new Uri($"http://www.bing.com/hpimagearchive.aspx?n=1&mkt={market}");
    string result = null;
    using (var httpClient = new HttpClient())
    {
        result = await httpClient.GetStringAsync(request);
    }
    var targetElement = resolution == Resolution.Unspecified ? "url" : "urlBase";
    var pathString = XDocument.Parse(result).Descendants(targetElement).First().Value;
    var resolutionString = resolution == Resolution.Unspecified ? "" : $"{resolution}.jpg";
    return new Uri($"http://www.bing.com{pathString}{resolutionString}");
}
```

## Usage

```C#
private async void MainPage_Loaded(object sender, Windows.UI.Xaml.RoutedEventArgs e)
{
    Image1.Source = new Windows.UI.Xaml.Media.Imaging.BitmapImage(
        await GetBingImageOfTheDayUriAsync());
}

// Normally declared in XAML; included here so the snippet will compile.
private Windows.UI.Xaml.Controls.Image Image1 = new Windows.UI.Xaml.Controls.Image();
```

## See also

[HttpClient class](https://msdn.microsoft.com/library/windows/apps/windows.web.http.httpclient.aspx)  
[Uri class](https://msdn.microsoft.com/library/system.uri.aspx)  
[Interpolated strings](https://msdn.microsoft.com/library/dn961160.aspx) (strings with a $ prefix)  
[?: operator](https://msdn.microsoft.com/library/ty67wk28.aspx)  
