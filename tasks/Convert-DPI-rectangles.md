<!---
  category: GraphicsAndAnimation
  language: cpp
-->

# Convert the DPI of rectangles (C++)

Converts a rectangle in a window from logical coordinates to physical coordinates or vice-versa, 
regardless of the dots per inch (DPI) awareness of the caller.

Users can have multiple displays, each of which could have a different DPI setting.
Modern apps might need to account for this and adjust the display based on DPI changes.
The high-DPI APIs in the platform provide methods for converting points from one DPI to another.
The methods below enhance this so you can convert entire rectangles when you are concerned with potential different DPI values.

```C++
BOOL GetLogicalRect(HWND relativeWindow, LPRECT rect)
{
    if (rect == NULL)
    {
        return FALSE;
    }

    POINT topLeft, bottomRight;

    topLeft.x = rect->left;
    topLeft.y = rect->top;
    bottomRight.x = rect->right;
    bottomRight.y = rect->bottom;

    if (!PhysicalToLogicalPointForPerMonitorDPI(relativeWindow, &topLeft)) 
    {
        return FALSE;
    }
    if (!PhysicalToLogicalPointForPerMonitorDPI(relativeWindow, &bottomRight)) 
    {
        return FALSE;
    }

    rect->left = topLeft.x;
    rect->top = topLeft.y;
    rect->right = bottomRight.x;
    rect->bottom = bottomRight.y;

    return TRUE;
}

BOOL GetPhysicalRect(HWND relativeWindow, LPRECT rect)
{
    if (rect == NULL)
    {
        return FALSE;
    }

    POINT topLeft, bottomRight;

    topLeft.x = rect->left;
    topLeft.y = rect->top;
    bottomRight.x = rect->right;
    bottomRight.y = rect->bottom;

    if (!LogicalToPhysicalPointForPerMonitorDPI(relativeWindow, &topLeft)) 
    {
        return FALSE;
    }
    if (!LogicalToPhysicalPointForPerMonitorDPI(relativeWindow, &bottomRight)) 
    {
        return FALSE;
    }

    rect->left = topLeft.x;
    rect->top = topLeft.y;
    rect->right = bottomRight.x;
    rect->bottom = bottomRight.y;

    return TRUE;
}
```

## See also

[PhysicalToLogicalPointForPerMonitorDPI function](https://msdn.microsoft.com/library/windows/desktop/dn384112.aspx)  
[LogicalToPhysicalPointForPerMonitorDPI function](https://msdn.microsoft.com/library/windows/desktop/dn384110.aspx)  

For more info about how different DPI values work, see [High DPI](https://msdn.microsoft.com/library/windows/desktop/dd464646.aspx).  
