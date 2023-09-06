# Get Screen Size

```cs
public static Size GetPrimaryScreenSize()
{
    int screenWidth = PInvoke.User32.GetSystemMetrics(PInvoke.User32.SystemMetric.SM_CXSCREEN);
    int screenHeight = PInvoke.User32.GetSystemMetrics(PInvoke.User32.SystemMetric.SM_CYSCREEN);
    return new Size(screenWidth, screenHeight);
}
```

# Get Center Screen Point

```cs
public static Point CalculateCenterScreenPoint(int width, int height)
{
    var size = GetPrimaryScreenSize();
    return new Point
    {
        X = size.Width / 2 - width / 2,
        Y = size.Height / 2 - height / 2
    };
}
```

# Move Window To Center Screen

```cs
public static void MoveWindowToCenterScreen(AppWindow window)
{
    var point = CalculateCenterScreenPoint(
        window.Size.Width, window.Size.Height);
    window.Move(new PointInt32((int) point.X, (int) point.Y));
}
```
