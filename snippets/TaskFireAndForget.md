# Launch Tasks Without Having To Handle Return Value

```cs
public static class TaskFireAndForget
{
    public static void FireAndForget(this Task task)
    {
        _ = task.ContinueWith(it =>
        {
            Debug.WriteLineIf(it.IsFaulted, it.Exception);
        }, TaskScheduler.Default);
    }
}
```
