# Conversion Between Pointer And C# String

```cs
public static IntPtr CreateUtf8String(string s, out uint size)
{
    byte[] bytes = Encoding.UTF8.GetBytes(s);
    IntPtr ptr = Marshal.AllocHGlobal(bytes.Length);
    Marshal.Copy(bytes, 0, ptr, bytes.Length);
    size = (uint) bytes.Length;
    return ptr;
}

public static void FreeUtf8String(IntPtr ptr)
{
    Marshal.FreeHGlobal(ptr);
}

public static string Utf8StringFromPtr(IntPtr ptr, int length)
{
    return Marshal.PtrToStringUTF8(ptr, length);
}

public static IntPtr Utf8StringAlloc(uint size)
{
    return Marshal.AllocHGlobal((int)size);
}
```
