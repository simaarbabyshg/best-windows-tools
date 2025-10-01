# Quick Start Guide - Getting Started with C# and .NET
# راهنمای شروع سریع - شروع کار با C# و .NET

This guide will help you set up your development environment and create your first Windows tool using C# and .NET.

این راهنما به شما کمک می‌کند محیط توسعه خود را راه‌اندازی کرده و اولین ابزار ویندوز خود را با استفاده از C# و .NET بسازید.

## Step 1: Install Development Tools / مرحله 1: نصب ابزارهای توسعه

### Required Software / نرم‌افزارهای مورد نیاز:

1. **Visual Studio 2022 Community Edition** (FREE/رایگان)
   - Download from: https://visualstudio.microsoft.com/downloads/
   - Select workloads during installation / انتخاب بارهای کاری در هنگام نصب:
     - ✅ .NET Desktop Development
     - ✅ Desktop development with C++  (optional for Win32 interop)

2. **Windows SDK** (included with Visual Studio)
   - Required for Windows API access / برای دسترسی به Windows API لازم است

3. **Git for Windows** (optional but recommended)
   - Download from: https://git-scm.com/download/win

## Step 2: Create Your First Project / مرحله 2: ایجاد اولین پروژه

### Creating a WPF Application / ایجاد برنامه WPF:

1. Open Visual Studio 2022
2. Click "Create a new project" / کلیک بر "ایجاد پروژه جدید"
3. Search for "WPF Application" / جستجو برای "WPF Application"
4. Select "WPF App (.NET)" / انتخاب "WPF App (.NET)"
5. Name your project (e.g., "WindowsToolsManager")
6. Choose .NET 8.0 as the framework
7. Click "Create" / کلیک بر "ایجاد"

## Step 3: Configure UAC for Administrator Rights / مرحله 3: پیکربندی UAC برای دسترسی مدیر

### Add Application Manifest / افزودن Manifest برنامه:

1. Right-click on your project in Solution Explorer
2. Select "Add" → "New Item"
3. Search for "Application Manifest File"
4. Name it `app.manifest`
5. Open `app.manifest` and find this section:

```xml
<requestedExecutionLevel level="asInvoker" uiAccess="false" />
```

6. Change it to:

```xml
<requestedExecutionLevel level="requireAdministrator" uiAccess="false" />
```

7. Save the file / ذخیره فایل

## Step 4: Create a Simple System Tool / مرحله 4: ایجاد یک ابزار سیستم ساده

### Example: Check Administrator Status / مثال: بررسی وضعیت مدیر

Add this code to your `MainWindow.xaml.cs`:

```csharp
using System;
using System.Security.Principal;
using System.Windows;

namespace WindowsToolsManager
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
            CheckAdminStatus();
        }

        private void CheckAdminStatus()
        {
            bool isAdmin = IsAdministrator();
            
            if (isAdmin)
            {
                MessageBox.Show(
                    "Application is running with Administrator privileges.",
                    "Admin Status",
                    MessageBoxButton.OK,
                    MessageBoxImage.Information
                );
            }
            else
            {
                MessageBox.Show(
                    "Application is NOT running with Administrator privileges.",
                    "Admin Status",
                    MessageBoxButton.OK,
                    MessageBoxImage.Warning
                );
            }
        }

        /// <summary>
        /// Checks if the current process has administrator privileges
        /// بررسی اینکه آیا فرآیند فعلی دارای دسترسی مدیر است
        /// </summary>
        private bool IsAdministrator()
        {
            try
            {
                WindowsIdentity identity = WindowsIdentity.GetCurrent();
                WindowsPrincipal principal = new WindowsPrincipal(identity);
                return principal.IsInRole(WindowsBuiltInRole.Administrator);
            }
            catch
            {
                return false;
            }
        }
    }
}
```

## Step 5: Access Windows APIs / مرحله 5: دسترسی به Windows API

### Example: Get System Information / مثال: دریافت اطلاعات سیستم

Add this code to demonstrate P/Invoke for Windows APIs:

```csharp
using System;
using System.Runtime.InteropServices;
using System.Text;
using System.Windows;

namespace WindowsToolsManager
{
    public partial class MainWindow : Window
    {
        // Import Windows API functions
        [DllImport("kernel32.dll")]
        static extern bool GetComputerName(StringBuilder lpBuffer, ref int nSize);

        [DllImport("kernel32.dll")]
        static extern void GetSystemInfo(out SYSTEM_INFO lpSystemInfo);

        [StructLayout(LayoutKind.Sequential)]
        public struct SYSTEM_INFO
        {
            public ushort processorArchitecture;
            ushort reserved;
            public uint pageSize;
            public IntPtr minimumApplicationAddress;
            public IntPtr maximumApplicationAddress;
            public IntPtr activeProcessorMask;
            public uint numberOfProcessors;
            public uint processorType;
            public uint allocationGranularity;
            public ushort processorLevel;
            public ushort processorRevision;
        }

        private void GetSystemInformation()
        {
            // Get computer name
            StringBuilder computerName = new StringBuilder(256);
            int size = computerName.Capacity;
            if (GetComputerName(computerName, ref size))
            {
                Console.WriteLine($"Computer Name: {computerName}");
            }

            // Get system info
            SYSTEM_INFO sysInfo;
            GetSystemInfo(out sysInfo);
            Console.WriteLine($"Processor Count: {sysInfo.numberOfProcessors}");
            Console.WriteLine($"Page Size: {sysInfo.pageSize} bytes");
        }
    }
}
```

## Step 6: Build and Run / مرحله 6: ساخت و اجرا

### Building Your Application / ساخت برنامه:

1. Press F5 or click "Start" button
2. You should see UAC prompt asking for administrator permission
3. Your application will launch with elevated privileges

### Important Notes / نکات مهم:

- ⚠️ Always test both with and without admin rights
- ⚠️ Handle cases where user denies UAC prompt
- ⚠️ Log all privileged operations
- ⚠️ Validate all inputs before performing system operations

## Common Patterns and Best Practices / الگوهای رایج و بهترین روش‌ها

### 1. Checking Privileges Before Operations / بررسی دسترسی قبل از عملیات

```csharp
public void PerformPrivilegedOperation()
{
    if (!IsAdministrator())
    {
        MessageBox.Show(
            "This operation requires administrator privileges.",
            "Insufficient Privileges",
            MessageBoxButton.OK,
            MessageBoxImage.Error
        );
        return;
    }

    try
    {
        // Perform the operation
        // انجام عملیات
    }
    catch (UnauthorizedAccessException)
    {
        MessageBox.Show(
            "Access denied. Please run as administrator.",
            "Error",
            MessageBoxButton.OK,
            MessageBoxImage.Error
        );
    }
}
```

### 2. Safe Windows API Calls / فراخوانی امن Windows API

```csharp
public class SafeApiWrapper
{
    [DllImport("kernel32.dll", SetLastError = true)]
    static extern bool CloseHandle(IntPtr hObject);

    public static bool SafeCloseHandle(IntPtr handle)
    {
        if (handle == IntPtr.Zero || handle == new IntPtr(-1))
        {
            return false;
        }

        bool result = CloseHandle(handle);
        if (!result)
        {
            int error = Marshal.GetLastWin32Error();
            Console.WriteLine($"CloseHandle failed with error: {error}");
        }
        return result;
    }
}
```

### 3. Logging Privileged Operations / لاگ کردن عملیات دارای دسترسی

```csharp
using System;
using System.IO;

public class OperationLogger
{
    private static readonly string LogPath = 
        Path.Combine(
            Environment.GetFolderPath(Environment.SpecialFolder.CommonApplicationData),
            "WindowsTools",
            "operations.log"
        );

    public static void LogOperation(string operation, string details)
    {
        try
        {
            Directory.CreateDirectory(Path.GetDirectoryName(LogPath));
            
            string logEntry = $"{DateTime.Now:yyyy-MM-dd HH:mm:ss} - {operation}: {details}{Environment.NewLine}";
            File.AppendAllText(LogPath, logEntry);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to log operation: {ex.Message}");
        }
    }
}
```

## Next Steps / مراحل بعدی

1. **Learn WPF / یادگیری WPF**
   - https://docs.microsoft.com/en-us/dotnet/desktop/wpf/

2. **Study Windows API / مطالعه Windows API**
   - https://docs.microsoft.com/en-us/windows/win32/

3. **Explore P/Invoke / کاوش P/Invoke**
   - https://docs.microsoft.com/en-us/dotnet/standard/native-interop/pinvoke

4. **Security Best Practices / بهترین روش‌های امنیتی**
   - https://docs.microsoft.com/en-us/dotnet/standard/security/

## Useful NuGet Packages / بسته‌های NuGet مفید

```
Install-Package System.Management       # For WMI queries
Install-Package Microsoft.Win32.Registry # For registry operations
Install-Package System.ServiceProcess.ServiceController # For service management
```

## Resources / منابع

- [.NET Documentation](https://docs.microsoft.com/dotnet/)
- [Windows API Documentation](https://docs.microsoft.com/windows/win32/)
- [WPF Tutorial](https://wpf-tutorial.com/)
- [P/Invoke.net](https://pinvoke.net/) - Database of P/Invoke signatures

## Troubleshooting / عیب‌یابی

### UAC Prompt Not Appearing / درخواست UAC نمایش داده نمی‌شود:
- Check that app.manifest is properly configured
- Verify manifest is included in project properties
- Clean and rebuild solution

### Access Denied Errors / خطاهای دسترسی رد شده:
- Ensure application is running as administrator
- Check file/registry permissions
- Verify antivirus is not blocking

### P/Invoke Errors / خطاهای P/Invoke:
- Check DLL name spelling
- Verify function signature matches Windows API
- Use correct calling convention (usually stdcall)

---

Happy coding! / برنامه‌نویسی موفق!

For more information, see [PLATFORM_RECOMMENDATIONS.md](PLATFORM_RECOMMENDATIONS.md)
