# A Typical ViewModel Class Without Having To Write Redundant Boilerplate Code

```cs
partial class MessageBoxViewModel : ObservableObject
{
    [ObservableProperty]
    public string title = "";

    [ObservableProperty]
    public string message = "";

    [ObservableProperty]
    public string buttonTitle = "OK";
}
```

# A Global ViewModel That Propagates Changes to All Bound Views

```cs
public partial class GlobalViewModel : ObservableObject
{
    public static GlobalViewModel Instance { get; set; } = new GlobalViewModel();

    [ObservableProperty]
    public bool isServiceOnline = false;

    [ObservableProperty]
    public string loggingGreetingsName = "AirX User";
}
```
