# Convert hex to color

Converts a hexadecimal string representation of a color to its Windows.UI.Color ARGB (alpha, red, green, blue) equivalent or vice-versa. 

```C#
using System;
using System.Globalization;
using Windows.UI;

public static Color ConvertHexToColor(string hex)
{
    hex = hex.Remove(0, 1);
    byte a = hex.Length == 8 ? Byte.Parse(hex.Substring(0, 2), NumberStyles.HexNumber) : (byte)255; 
    byte r = Byte.Parse(hex.Substring(hex.Length - 6, 2), NumberStyles.HexNumber);
    byte g = Byte.Parse(hex.Substring(hex.Length - 4, 2), NumberStyles.HexNumber);
    byte b = Byte.Parse(hex.Substring(hex.Length - 2), NumberStyles.HexNumber);
    return Color.FromArgb(a, r, g, b);
}

public static string ConvertColorToHex(Color color)
{
    return $"#{color.A}{color.R}{color.G}{color.B}"; 
}
```

## See also

[Color class](https://msdn.microsoft.com/library/windows/apps/windows.ui.colors.aspx)  
[ARGB](https://en.wikipedia.org/wiki/RGBA_color_space#ARGB_.28word-order.29)  
[Interpolated strings](https://msdn.microsoft.com/library/dn961160.aspx) (strings with a $ prefix)  
[?: operator](https://msdn.microsoft.com/library/ty67wk28.aspx)  
