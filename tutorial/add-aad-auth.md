<!-- markdownlint-disable MD002 MD041 -->

在本练习中, 你将扩展上一练习中的应用程序, 以支持 Azure AD 的身份验证。 若要获取所需的 OAuth 访问令牌以调用 Microsoft Graph, 这是必需的。 在此步骤中, 将[.net 的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)集成到应用程序中。

在 "**解决方案资源管理器**" 中, 展开 " **GraphTutorial** " 项目并右键单击 "**模型**" 文件夹。 选择 "**添加 > 类 ...**"。命名该类`OAuthSettings` , 然后选择 "**添加**"。 打开 " **OAuthSettings.cs** " 文件, 并将其内容替换为以下内容。

```cs
namespace GraphTutorial.Models
{
    public static class OAuthSettings
    {
        public const string ApplicationId = "YOUR_APP_ID_HERE";
        public const string RedirectUri = "YOUR_REDIRECT_URI_HERE";
        public const string Scopes = "User.Read Calendars.Read";
    }
}
```

> [!IMPORTANT]
> 如果您使用的是源代码管理 (如 git), 现在可以从源代码管理中排除`OAuthSettings.cs`该文件, 以避免无意中泄漏您的应用程序 ID。

## <a name="implement-sign-in"></a>实施登录

打开**GraphTutorial**项目中的`using` **App.xaml.cs**文件, 并将以下语句添加到文件顶部。

```cs
using GraphTutorial.Models;
using Microsoft.Identity.Client;
using Microsoft.Graph;
using System.Diagnostics;
using System.Linq;
using System.Net.Http.Headers;
```

将以下属性添加到`App`类中。

```cs
// UIParent used by Android version of the app
public static UIParent AuthUIParent = null;

// Microsoft Authentication client for native/mobile apps
public static PublicClientApplication PCA;

// Microsoft Graph client
public static GraphServiceClient GraphClient;
```

接下来, 在`PublicClientApplication` `App`类的构造函数中创建一个新的。

```cs
public App()
{
    InitializeComponent();

    PCA = new PublicClientApplication(OAuthSettings.ApplicationId);

    MainPage = new MainPage();
}
```

现在, 更新`SignIn`函数以使用`PublicClientApplication`获取访问令牌。 在该行的`await GetUserInfo();`上方添加以下代码。

```cs
var scopes = OAuthSettings.Scopes.Split(' ');

// First, attempt silent sign in
// If the user's information is already in the app's cache,
// they won't have to sign in again.
try
{
    var accounts = await PCA.GetAccountsAsync();
    var silentAuthResult = await PCA.AcquireTokenSilentAsync(
        scopes, accounts.FirstOrDefault());

    Debug.WriteLine("User already signed in.");
    Debug.WriteLine($"Access token: {silentAuthResult.AccessToken}");
}
catch (MsalUiRequiredException)
{
    // This exception is thrown when an interactive sign-in is required.
    // Prompt the user to sign-in
    var authResult = await PCA.AcquireTokenAsync(scopes, AuthUIParent);
    Debug.WriteLine($"Access Token: {authResult.AccessToken}");
}
```

此代码将首先尝试以无提示方式获取访问令牌。 如果用户的信息已在应用程序的缓存中 (例如, 如果用户之前关闭了应用程序, 则没有注销), 这将会成功, 并且没有理由提示用户。 如果缓存中没有用户的信息, 该`AcquireTokenSilentAsync`函数将引发。 `MsalUiRequiredException` 在这种情况下, 代码调用交互式函数以获取令牌`AcquireTokenAsync`。

现在, 更新`SignOut`函数以从缓存中删除用户的信息。 将以下代码添加到`SignOut`函数的开头。

```cs
// Get all cached accounts for the app
// (Should only be one)
var accounts = await PCA.GetAccountsAsync();
while (accounts.Any())
{
    // Remove the account info from the cache
    await PCA.RemoveAsync(accounts.First());
    accounts = await PCA.GetAccountsAsync();
}
```

最后, 在加载`MainPage`时更新类以进行登录。 将以下函数添加到`MainPage` **MainPage.xaml.cs**中的类。

```cs
protected override async void OnAppearing()
{
    base.OnAppearing();
    await (Application.Current as App).SignIn();
}
```

### <a name="update-android-project-to-enable-sign-in"></a>更新 Android 项目以启用登录

在 Xamarin Android 项目中使用时, Microsoft 身份验证库具有[特定于 Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)的一些要求。

首先, 需要将应用注册中的重定向 URI 添加到 Android 清单中。 若要执行此操作, 请将新活动添加到**GraphTutorial**项目中。 右键单击 " **GraphTutorial** ", 然后依次选择 "**添加**"、"**新建项目 ...**"。选择 "**活动**", 命名`MsalActivity`活动, 然后选择 "**添加**"。

打开**MsalActivity.cs**文件并删除`[Activity(Label = "MsalActivity")]`该行, 然后将以下属性添加到类声明的上方。

```cs
// This class only exists to create the necessary activity in the Android
// manifest. Doing it this way allows the value of the RedirectUri constant
// to be inserted at build.
[Activity(Name = "microsoft.identity.client.BrowserTabActivity")]
[IntentFilter(new[] { Intent.ActionView },
    Categories = new[] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataScheme = Models.OAuthSettings.RedirectUri, DataHost = "auth")]
```

接下来, 打开**MainActivity.cs** , 并将`using`以下语句添加到文件顶部。

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

然后, 重写`OnActivityResult`函数以将控制权传递给 MSAL 库。 将以下项添加到`MainActivity`类中。

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="update-ios-project-to-enable-sign-in"></a>更新 iOS 项目以启用登录

> [!IMPORTANT]
> 由于 MSAL 需要使用 plist 文件, 因此必须将 Visual Studio 配置为 Apple 开发人员帐户才能启用预配。 有关详细信息, 请参阅[适用于 Xamarin 的设备设置](/xamarin/ios/get-started/installation/device-provisioning)。

在 Xamarin iOS 项目中使用时, Microsoft 身份验证库具有[特定于 iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)的一些要求。

首先, 需要启用密钥链访问。 在 "解决方案资源管理器" 中, 展开 " **GraphTutorial** " 项目, 然后打开**plist**文件。 找到 "**密钥链**" 权限, 然后选择 "**启用密钥链**"。 在**密钥链组**中, 添加带格式`com.YOUR_DOMAIN.GraphTutorial`的条目。

![密钥链权限配置的屏幕截图](./images/enable-keychain-access.png)

接下来, 您需要将在应用注册步骤中配置的重定向 URI 注册为您的应用程序处理的 URL 类型。 打开**plist**文件并进行以下更改。

- 在 "**应用程序**" 选项卡上, 检查 "**捆绑包标识符**" 的值是否与您为**plist**中的 "**密钥链组**" 设置的值相匹配。 如果不是, 请立即更新。
- 在 "**高级**" 选项卡上, 找到 " **URL 类型**" 部分。 在此处使用以下值添加 URL 类型:
    - **标识符**: 设置为你的**捆绑包标识符**的值
    - **URL 方案**: 设置为从开始的应用注册重定向 URI`msal`
    - **角色**:`Editor`
    - **图标**: 留空

![Info 的 URL 类型部分的屏幕截图 plist](./images/add-url-type.png)

最后, 更新**GraphTutorial**项目中的代码以在身份验证过程中处理重定向。 打开**AppDelegate.cs**文件, 并在该文件`using`的顶部添加以下语句。

```cs
using Microsoft.Identity.Client;
```

添加以下行以`FinishedLaunching`在`return`语句之前运行。

```cs
// Specify the Keychain access group
App.PCA.iOSKeychainSecurityGroup = "com.graphdevx.GraphTutorial";
```

最后, 重写`OpenUrl`函数以将 URL 传递给 MSAL 库。 将以下项添加到`AppDelegate`类中。

```cs
// Handling redirect URL
// See: https://github.com/azuread/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

## <a name="storing-the-tokens"></a>存储令牌

在 Xamarin 项目中使用 Microsoft 身份验证库时, 默认情况下, 它会利用本机安全存储来缓存令牌。 有关详细信息, 请参阅[令牌缓存序列化](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)。

## <a name="test-sign-in"></a>测试登录

在这种情况下, 如果运行应用程序并点击 "**登录**" 按钮, 系统会提示您登录。 成功登录后, 您应该会看到打印到调试器输出中的访问令牌。

![Visual Studio 中的输出窗口的屏幕截图](./images/debugger-access-token.png)

## <a name="get-user-details"></a>获取用户详细信息

现在, 更新`SignIn` **App.xaml.cs**中的函数以初始化`GraphServiceClient`。 在`await GetUserInfo();`行前面添加以下代码。

```cs
// Initialize Graph client
GraphClient = new GraphServiceClient(new DelegateAuthenticationProvider(
    async (requestMessage) =>
    {
        var accounts = await PCA.GetAccountsAsync();

        var result = await PCA.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());

        requestMessage.Headers.Authorization =
            new AuthenticationHeaderValue("Bearer", result.AccessToken);
    }));
```

现在, 更新`GetUserInfo`函数以从 Microsoft Graph 中获取用户的详细信息。 将现有的 `GetUserInfo` 函数替换为以下内容。

```cs
private async Task GetUserInfo()
{
    // Get the logged on user's profile (/me)
    var user = await GraphClient.Me.Request().GetAsync();

    UserPhoto = ImageSource.FromStream(() => GetUserPhoto());
    UserName = user.DisplayName;
    UserEmail = string.IsNullOrEmpty(user.Mail) ? user.UserPrincipalName : user.Mail;
}
```

如果您现在保存更改并运行应用程序, 则在使用用户的显示名称和电子邮件地址更新 UI 后, 登录 UI。

