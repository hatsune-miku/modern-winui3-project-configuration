# Inherit This To Easily Apply Acrylic/Mica Effect To Your Window

```cs
public abstract class BaseWindow : Window
{
    protected readonly AppWindow CurrentAppWindow;
    protected readonly OverlappedPresenter Presenter;
    protected readonly SystemBackdropConfiguration backdropConfiguration;
    protected readonly MicaController micaController;

    protected BaseWindow()
    {
        CurrentAppWindow = UIUtil.GetAppWindow(this);
        Presenter = (OverlappedPresenter) CurrentAppWindow.Presenter;

        backdropConfiguration = new SystemBackdropConfiguration
        {
            IsInputActive = true
        };

        micaController = new MicaController
        {
            Kind = MicaKind.Base,
        };
    }

    bool TrySetMicaBackdrop()
    {
        if (!MicaController.IsSupported())
        {
            return false;
        }

        ((FrameworkElement)Content).RequestedTheme = ElementTheme.Default;
        ExtendsContentIntoTitleBar = true;

        EnsureWindowsSystemDispatcherQueueController();

        // Hooking up the policy object.
        this.Activated += OnWindowActivated;
        this.Closed += OnWindowClosed;
        ((FrameworkElement)Content).ActualThemeChanged += OnWindowThemeChanged;

        // Initial configuration state.
        UpdateConfigurationSourceTheme();

        // Enable the system backdrop.
        micaController.AddSystemBackdropTarget(this.As<ICompositionSupportsSystemBackdrop>());
        micaController.SetSystemBackdropConfiguration(backdropConfiguration);
        return true;
    }

    private void OnWindowActivated(object sender, WindowActivatedEventArgs args)
    {
        backdropConfiguration.IsInputActive =
            args.WindowActivationState != WindowActivationState.Deactivated;
    }

    private void OnWindowClosed(object sender, WindowEventArgs args)
    {
        // Make sure any Mica/Acrylic controller is disposed so it doesn't try to
        // use this closed window.
        if (micaController != null)
        {
            micaController.Dispose();
        }
        this.Activated -= OnWindowActivated;
    }

    private void OnWindowThemeChanged(FrameworkElement sender, object args)
    {
        if (backdropConfiguration != null)
        {
            UpdateConfigurationSourceTheme();
        }
    }

    private void UpdateConfigurationSourceTheme()
    {
        switch (((FrameworkElement)Content).ActualTheme)
        {
            case ElementTheme.Dark:
                backdropConfiguration.Theme = SystemBackdropTheme.Dark;
                break;

            case ElementTheme.Light:
                backdropConfiguration.Theme = SystemBackdropTheme.Light;
                break;

            case ElementTheme.Default:
                backdropConfiguration.Theme = SystemBackdropTheme.Default;
                break;
        }
    }

    [StructLayout(LayoutKind.Sequential)]
    struct DispatcherQueueOptions
    {
        internal int dwSize;
        internal int threadType;
        internal int apartmentType;
    }

    [DllImport("CoreMessaging.dll")]
    private static extern int CreateDispatcherQueueController([In] DispatcherQueueOptions options, [In, Out, MarshalAs(UnmanagedType.IUnknown)] ref object dispatcherQueueController);

    object m_dispatcherQueueController = null;
    public void EnsureWindowsSystemDispatcherQueueController()
    {
        if (Windows.System.DispatcherQueue.GetForCurrentThread() != null)
        {
            // one already exists, so we'll just use it.
            return;
        }

        if (m_dispatcherQueueController == null)
        {
            DispatcherQueueOptions options;
            options.dwSize = Marshal.SizeOf(typeof(DispatcherQueueOptions));
            options.threadType = 2;    // DQTYPE_THREAD_CURRENT
            options.apartmentType = 2; // DQTAT_COM_STA

            CreateDispatcherQueueController(options, ref m_dispatcherQueueController);
        }
    }
}
```
