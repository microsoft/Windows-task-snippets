# Get a device's current location

Gets the device's current location (represented as longitude and latitude), if the user allows it. 

**Note:** To use this method, you must enable the Location capability in the Package.appxmanifest file of your app. 

```C#
using System;
using System.Threading.Tasks;
using Windows.Devices.Geolocation;

public static async Task<Tuple<double, double>> GetCurrentCoordinatesAsync()
{
    if (await Geolocator.RequestAccessAsync() == GeolocationAccessStatus.Allowed)
    {
        var geoposition = await new Geolocator().GetGeopositionAsync();
        var position = geoposition.Coordinate.Point.Position;
        return new Tuple<double, double>(position.Longitude, position.Latitude);
    }
    return null;
}
```

## See also

[Geolocator class](https://msdn.microsoft.com/library/windows/apps/windows.devices.geolocation.geolocator.aspx)  
[Geolocator.GetGeopositionAsync](https://msdn.microsoft.com/library/windows/apps/xaml/hh973536)  
