# Convert RGB to HSV

Converts RGB (red, green, blue) color values to HSV (hue, saturation, value). 
Since Universal Windows Platform (UWP) system colors (contained in the Windows.UI.Colors class) 
only provide access to RGB values, this utility is useful for converting them if needed.

```C#
using System;
using System.Linq;

public static Tuple<double, double, double> RgbToHsv(double r, double g, double b)
{
    double[] hsv = new double[3]; 
    r = r / 255.0;
    g = g / 255.0;
    b = b / 255.0;
    double max = new[] { r, g, b }.Max();
    double min = new[] { r, g, b }.Min(); 
    double delta = max - min;
    hsv[1] = max != 0 ? delta / max : 0;
    hsv[2] = max;
    if (hsv[1] == 0) 
    {
        return new Tuple<double, double, double>(hsv[0], hsv[1], hsv[2]);
    }
    if (r == max)
    {
        hsv[0] = ((g - b) / delta);
    }
    else if (g == max)
    {
        hsv[0] = ((b - r) / delta) + 2.0;
    }
    else if (b == max)
    {
        hsv[0] = ((r - g) / delta) + 4.0;
    }
    hsv[0] *= 60.0;
    if (hsv[0] < 0)
    {
        hsv[0] += 360.0;
    }
    return new Tuple<double, double, double>(hsv[0], hsv[1], hsv[2]);
}
```

## See also

[Colors class](https://msdn.microsoft.com/library/windows/apps/windows.ui.colors.aspx)  

This C# task snippet is based on generic graphics algorithms from the text 
"Introduction to Computer Graphics" by Foley et al. ISBN # 0201609215. 
