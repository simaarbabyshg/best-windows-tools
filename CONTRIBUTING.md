# Contributing to Best Windows Tools
# راهنمای مشارکت در پروژه ابزارهای ویندوز

Thank you for your interest in contributing to Best Windows Tools! / از علاقه شما به مشارکت در پروژه ابزارهای ویندوز متشکریم!

## Getting Started / شروع کار

### Prerequisites / پیش‌نیازها

Based on the platform recommendations, you should have:
براساس توصیه‌های پلتفرم، باید داشته باشید:

For C#/.NET Development:
- Visual Studio 2022 Community Edition (free) or higher
- .NET 7 or .NET 8 SDK
- Windows 10/11 for testing

For PowerShell Scripts:
- PowerShell 7.x
- Visual Studio Code with PowerShell extension (recommended)

For C++ Development:
- Visual Studio 2022 with C++ Desktop Development workload
- Windows SDK

## How to Contribute / نحوه مشارکت

### 1. Fork the Repository / فورک کردن مخزن
Fork this repository to your GitHub account.
این مخزن را به اکانت گیت‌هاب خود فورک کنید.

### 2. Clone Your Fork / کلون کردن فورک
```bash
git clone https://github.com/YOUR_USERNAME/best-windows-tools.git
cd best-windows-tools
```

### 3. Create a Branch / ایجاد شاخه
```bash
git checkout -b feature/your-feature-name
```

### 4. Make Your Changes / انجام تغییرات

- Follow the coding standards outlined below
- Write clear, commented code
- Test your changes thoroughly
- Ensure elevated privileges are handled correctly

- استانداردهای کدنویسی ذکر شده در زیر را دنبال کنید
- کد واضح و با توضیحات بنویسید
- تغییرات خود را به طور کامل تست کنید
- اطمینان حاصل کنید که دسترسی‌های بالا به درستی مدیریت می‌شوند

### 5. Commit Your Changes / کامیت کردن تغییرات
```bash
git add .
git commit -m "Add: Brief description of your changes"
```

Use clear commit messages:
- `Add:` for new features
- `Fix:` for bug fixes
- `Update:` for updates to existing features
- `Docs:` for documentation changes

### 6. Push to Your Fork / پوش به فورک
```bash
git push origin feature/your-feature-name
```

### 7. Create a Pull Request / ایجاد درخواست Pull

Open a pull request from your fork to the main repository.
یک درخواست pull از فورک خود به مخزن اصلی باز کنید.

## Coding Standards / استانداردهای کدنویسی

### For C# Code:

```csharp
// Use meaningful variable names
// Use PascalCase for public members, camelCase for private
// Include XML documentation for public APIs

/// <summary>
/// Checks if the current process has administrator privileges
/// </summary>
/// <returns>True if elevated, false otherwise</returns>
public static bool IsAdministrator()
{
    var identity = WindowsIdentity.GetCurrent();
    var principal = new WindowsPrincipal(identity);
    return principal.IsInRole(WindowsBuiltInRole.Administrator);
}
```

### For PowerShell Scripts:

```powershell
# Use approved verbs (Get, Set, New, etc.)
# Include comment-based help
# Use proper error handling

<#
.SYNOPSIS
    Brief description of the function
.DESCRIPTION
    Detailed description
.EXAMPLE
    Example usage
#>
function Get-SystemInfo {
    [CmdletBinding()]
    param()
    
    try {
        # Implementation here
    }
    catch {
        Write-Error "An error occurred: $_"
    }
}
```

### For C++ Code:

```cpp
// Use RAII principles
// Check return values
// Handle errors appropriately

// Check for admin privileges
bool IsElevated() 
{
    BOOL fRet = FALSE;
    HANDLE hToken = NULL;
    
    if (OpenProcessToken(GetCurrentProcess(), TOKEN_QUERY, &hToken)) 
    {
        TOKEN_ELEVATION Elevation;
        DWORD cbSize = sizeof(TOKEN_ELEVATION);
        
        if (GetTokenInformation(hToken, TokenElevation, 
                              &Elevation, sizeof(Elevation), &cbSize)) 
        {
            fRet = Elevation.TokenIsElevated;
        }
    }
    
    if (hToken) 
    {
        CloseHandle(hToken);
    }
    
    return fRet;
}
```

## Testing Guidelines / راهنمای تست

### Before Submitting / قبل از ارسال:

1. ✅ Test on Windows 10 and Windows 11 if possible
2. ✅ Test with both elevated and non-elevated privileges
3. ✅ Verify UAC prompts work correctly
4. ✅ Check for memory leaks (especially in C++ code)
5. ✅ Ensure error messages are clear and helpful
6. ✅ Test edge cases and invalid inputs

1. ✅ در صورت امکان در ویندوز 10 و 11 تست کنید
2. ✅ با دسترسی‌های بالا و معمولی تست کنید
3. ✅ بررسی کنید که درخواست‌های UAC به درستی کار می‌کنند
4. ✅ بررسی نشت حافظه (بخصوص در کد C++)
5. ✅ اطمینان از واضح و مفید بودن پیام‌های خطا
6. ✅ تست موارد خاص و ورودی‌های نامعتبر

## Security Requirements / الزامات امنیتی

### Critical Security Rules / قوانین امنیتی حیاتی:

1. **Never store passwords or secrets in code**
   هرگز رمز عبور یا اطلاعات محرمانه را در کد ذخیره نکنید

2. **Validate all user input**
   تمام ورودی‌های کاربر را اعتبارسنجی کنید

3. **Use least privilege principle**
   از اصل حداقل دسترسی استفاده کنید

4. **Log security-sensitive operations**
   عملیات حساس امنیتی را لاگ کنید

5. **Handle exceptions securely - don't expose system details**
   استثناها را به صورت امن مدیریت کنید - جزئیات سیستم را افشا نکنید

## Documentation / مستندات

- Add comments for complex logic
- Update README.md if adding major features
- Include usage examples
- Document any new dependencies

- برای منطق پیچیده توضیحات اضافه کنید
- در صورت اضافه کردن ویژگی‌های مهم README.md را به‌روز کنید
- نمونه‌های استفاده را شامل شوید
- وابستگی‌های جدید را مستند کنید

## Questions? / سوالات؟

If you have questions, please:
- Open an issue for discussion
- Check existing issues and pull requests
- Review the platform recommendations document

اگر سوالی دارید، لطفاً:
- یک issue برای بحث باز کنید
- issue ها و pull request های موجود را بررسی کنید
- سند توصیه‌های پلتفرم را مطالعه کنید

## Code of Conduct / قوانین رفتاری

- Be respectful and inclusive / محترمانه و فراگیر باشید
- Provide constructive feedback / بازخورد سازنده ارائه دهید
- Focus on the code, not the person / روی کد تمرکز کنید، نه فرد
- Help others learn and grow / به دیگران کمک کنید یاد بگیرند و رشد کنند

Thank you for contributing! / از مشارکت شما متشکریم!
