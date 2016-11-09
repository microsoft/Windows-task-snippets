<!---
  category: GraphicsAndAnimation
  language: cs
-->

# Get all system colors as a collection

Gets a collection containing all system colors supported in Universal Windows Platform (UWP) apps.

```C#
using System.Collections.Generic;
using System.Linq;
using System.Reflection;
using Windows.UI;

public static IEnumerable<Color> GetAllColors()
{
    return typeof(Colors).GetRuntimeProperties().Select(x => (Color)x.GetValue(null));
}
```

## See also

[Colors class](https://msdn.microsoft.com/library/windows/apps/windows.ui.colors.aspx)  
[Color structure](https://msdn.microsoft.com/library/windows/apps/windows.ui.color.aspx)  
[GetRuntimeProperties extension method](https://msdn.microsoft.com/library/system.reflection.runtimereflectionextensions.getruntimeproperties.aspx)  
[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
