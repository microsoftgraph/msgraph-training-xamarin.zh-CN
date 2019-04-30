<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="28914-101">在本练习中, 你将扩展上一练习中的应用程序, 以支持 Azure AD 的身份验证。</span><span class="sxs-lookup"><span data-stu-id="28914-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="28914-102">若要获取所需的 OAuth 访问令牌以调用 Microsoft Graph, 这是必需的。</span><span class="sxs-lookup"><span data-stu-id="28914-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="28914-103">在此步骤中, 将[.net 的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)集成到应用程序中。</span><span class="sxs-lookup"><span data-stu-id="28914-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="28914-104">在 "**解决方案资源管理器**" 中, 展开 " **GraphTutorial** " 项目并右键单击 "**模型**" 文件夹。</span><span class="sxs-lookup"><span data-stu-id="28914-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="28914-105">选择 "**添加 > 类 ...**"。命名该类`OAuthSettings` , 然后选择 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="28914-105">Choose **Add > Class...**. Name the class `OAuthSettings` and choose **Add**.</span></span> <span data-ttu-id="28914-106">打开 " **OAuthSettings.cs** " 文件, 并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="28914-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

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
> <span data-ttu-id="28914-107">如果您使用的是源代码管理 (如 git), 现在可以从源代码管理中排除`OAuthSettings.cs`该文件, 以避免无意中泄漏您的应用程序 ID。</span><span class="sxs-lookup"><span data-stu-id="28914-107">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="28914-108">实施登录</span><span class="sxs-lookup"><span data-stu-id="28914-108">Implement sign-in</span></span>

<span data-ttu-id="28914-109">打开**GraphTutorial**项目中的`using` **App.xaml.cs**文件, 并将以下语句添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="28914-109">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

```cs
using GraphTutorial.Models;
using Microsoft.Identity.Client;
using Microsoft.Graph;
using System.Diagnostics;
using System.Linq;
using System.Net.Http.Headers;
```

<span data-ttu-id="28914-110">将以下属性添加到`App`类中。</span><span class="sxs-lookup"><span data-stu-id="28914-110">Add the following properties to the `App` class.</span></span>

```cs
// UIParent used by Android version of the app
public static UIParent AuthUIParent = null;

// Microsoft Authentication client for native/mobile apps
public static PublicClientApplication PCA;

// Microsoft Graph client
public static GraphServiceClient GraphClient;
```

<span data-ttu-id="28914-111">接下来, 在`PublicClientApplication` `App`类的构造函数中创建一个新的。</span><span class="sxs-lookup"><span data-stu-id="28914-111">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

```cs
public App()
{
    InitializeComponent();

    PCA = new PublicClientApplication(OAuthSettings.ApplicationId);

    MainPage = new MainPage();
}
```

<span data-ttu-id="28914-112">现在, 更新`SignIn`函数以使用`PublicClientApplication`获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="28914-112">Now update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="28914-113">在该行的`await GetUserInfo();`上方添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="28914-113">Add the following code above the `await GetUserInfo();` line.</span></span>

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

<span data-ttu-id="28914-114">此代码将首先尝试以无提示方式获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="28914-114">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="28914-115">如果用户的信息已在应用程序的缓存中 (例如, 如果用户之前关闭了应用程序, 则没有注销), 这将会成功, 并且没有理由提示用户。</span><span class="sxs-lookup"><span data-stu-id="28914-115">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="28914-116">如果缓存中没有用户的信息, 该`AcquireTokenSilentAsync`函数将引发。 `MsalUiRequiredException`</span><span class="sxs-lookup"><span data-stu-id="28914-116">If there is not a user's information in the cache, the `AcquireTokenSilentAsync` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="28914-117">在这种情况下, 代码调用交互式函数以获取令牌`AcquireTokenAsync`。</span><span class="sxs-lookup"><span data-stu-id="28914-117">In this case, the code calls the interactive function to get a token, `AcquireTokenAsync`.</span></span>

<span data-ttu-id="28914-118">现在, 更新`SignOut`函数以从缓存中删除用户的信息。</span><span class="sxs-lookup"><span data-stu-id="28914-118">Now update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="28914-119">将以下代码添加到`SignOut`函数的开头。</span><span class="sxs-lookup"><span data-stu-id="28914-119">Add the following code to the beginning of the `SignOut` function.</span></span>

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

<span data-ttu-id="28914-120">最后, 在加载`MainPage`时更新类以进行登录。</span><span class="sxs-lookup"><span data-stu-id="28914-120">Finally, update the `MainPage` class to sign-in when it loads.</span></span> <span data-ttu-id="28914-121">将以下函数添加到`MainPage` **MainPage.xaml.cs**中的类。</span><span class="sxs-lookup"><span data-stu-id="28914-121">Add the following function to the `MainPage` class in **MainPage.xaml.cs**.</span></span>

```cs
protected override async void OnAppearing()
{
    base.OnAppearing();
    await (Application.Current as App).SignIn();
}
```

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="28914-122">更新 Android 项目以启用登录</span><span class="sxs-lookup"><span data-stu-id="28914-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="28914-123">在 Xamarin Android 项目中使用时, Microsoft 身份验证库具有[特定于 Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)的一些要求。</span><span class="sxs-lookup"><span data-stu-id="28914-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

<span data-ttu-id="28914-124">首先, 需要将应用注册中的重定向 URI 添加到 Android 清单中。</span><span class="sxs-lookup"><span data-stu-id="28914-124">First, you need to add the redirect URI from your app registration to the Android manifest.</span></span> <span data-ttu-id="28914-125">若要执行此操作, 请将新活动添加到**GraphTutorial**项目中。</span><span class="sxs-lookup"><span data-stu-id="28914-125">To do this, add a new activity to the **GraphTutorial.Android** project.</span></span> <span data-ttu-id="28914-126">右键单击 " **GraphTutorial** ", 然后依次选择 "**添加**"、"**新建项目 ...**"。选择 "**活动**", 命名`MsalActivity`活动, 然后选择 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="28914-126">Right-click the **GraphTutorial.Android** and choose **Add**, then **New Item...**. Choose **Activity**, name the activity `MsalActivity`, and choose **Add**.</span></span>

<span data-ttu-id="28914-127">打开**MsalActivity.cs**文件并删除`[Activity(Label = "MsalActivity")]`该行, 然后将以下属性添加到类声明的上方。</span><span class="sxs-lookup"><span data-stu-id="28914-127">Open the **MsalActivity.cs** file and delete the `[Activity(Label = "MsalActivity")]` line, then add the following attributes above the class declaration.</span></span>

```cs
// This class only exists to create the necessary activity in the Android
// manifest. Doing it this way allows the value of the RedirectUri constant
// to be inserted at build.
[Activity(Name = "microsoft.identity.client.BrowserTabActivity")]
[IntentFilter(new[] { Intent.ActionView },
    Categories = new[] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataScheme = Models.OAuthSettings.RedirectUri, DataHost = "auth")]
```

<span data-ttu-id="28914-128">接下来, 打开**MainActivity.cs** , 并将`using`以下语句添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="28914-128">Next, open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

<span data-ttu-id="28914-129">然后, 重写`OnActivityResult`函数以将控制权传递给 MSAL 库。</span><span class="sxs-lookup"><span data-stu-id="28914-129">Then, override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="28914-130">将以下项添加到`MainActivity`类中。</span><span class="sxs-lookup"><span data-stu-id="28914-130">Add the following to the `MainActivity` class.</span></span>

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="28914-131">更新 iOS 项目以启用登录</span><span class="sxs-lookup"><span data-stu-id="28914-131">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="28914-132">由于 MSAL 需要使用 plist 文件, 因此必须将 Visual Studio 配置为 Apple 开发人员帐户才能启用预配。</span><span class="sxs-lookup"><span data-stu-id="28914-132">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="28914-133">有关详细信息, 请参阅[适用于 Xamarin 的设备设置](/xamarin/ios/get-started/installation/device-provisioning)。</span><span class="sxs-lookup"><span data-stu-id="28914-133">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="28914-134">在 Xamarin iOS 项目中使用时, Microsoft 身份验证库具有[特定于 iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)的一些要求。</span><span class="sxs-lookup"><span data-stu-id="28914-134">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

<span data-ttu-id="28914-135">首先, 需要启用密钥链访问。</span><span class="sxs-lookup"><span data-stu-id="28914-135">First, you need to enable Keychain access.</span></span> <span data-ttu-id="28914-136">在 "解决方案资源管理器" 中, 展开 " **GraphTutorial** " 项目, 然后打开**plist**文件。</span><span class="sxs-lookup"><span data-stu-id="28914-136">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span> <span data-ttu-id="28914-137">找到 "**密钥链**" 权限, 然后选择 "**启用密钥链**"。</span><span class="sxs-lookup"><span data-stu-id="28914-137">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span> <span data-ttu-id="28914-138">在**密钥链组**中, 添加带格式`com.YOUR_DOMAIN.GraphTutorial`的条目。</span><span class="sxs-lookup"><span data-stu-id="28914-138">In **Keychain Groups**, add an entry with the format `com.YOUR_DOMAIN.GraphTutorial`.</span></span>

![密钥链权限配置的屏幕截图](./images/enable-keychain-access.png)

<span data-ttu-id="28914-140">接下来, 您需要将在应用注册步骤中配置的重定向 URI 注册为您的应用程序处理的 URL 类型。</span><span class="sxs-lookup"><span data-stu-id="28914-140">Next, you need to register the redirect URI you configured in the app registration step as a URL type that your app handles.</span></span> <span data-ttu-id="28914-141">打开**plist**文件并进行以下更改。</span><span class="sxs-lookup"><span data-stu-id="28914-141">Open the **Info.plist** file and make the following changes.</span></span>

- <span data-ttu-id="28914-142">在 "**应用程序**" 选项卡上, 检查 "**捆绑包标识符**" 的值是否与您为**plist**中的 "**密钥链组**" 设置的值相匹配。</span><span class="sxs-lookup"><span data-stu-id="28914-142">On the **Application** tab, check that the value of **Bundle identifier** matches the value you set for **Keychain Groups** in **Entitlements.plist**.</span></span> <span data-ttu-id="28914-143">如果不是, 请立即更新。</span><span class="sxs-lookup"><span data-stu-id="28914-143">If it doesn't, update it now.</span></span>
- <span data-ttu-id="28914-144">在 "**高级**" 选项卡上, 找到 " **URL 类型**" 部分。</span><span class="sxs-lookup"><span data-stu-id="28914-144">On the **Advanced** tab, locate the **URL Types** section.</span></span> <span data-ttu-id="28914-145">在此处使用以下值添加 URL 类型:</span><span class="sxs-lookup"><span data-stu-id="28914-145">Add a URL type here with the following values:</span></span>
    - <span data-ttu-id="28914-146">**标识符**: 设置为你的**捆绑包标识符**的值</span><span class="sxs-lookup"><span data-stu-id="28914-146">**Identifier**: set to the value of your **Bundle identifier**</span></span>
    - <span data-ttu-id="28914-147">**URL 方案**: 设置为从开始的应用注册重定向 URI`msal`</span><span class="sxs-lookup"><span data-stu-id="28914-147">**URL Schemes**: set to the redirect URI from your app registration that begins with `msal`</span></span>
    - <span data-ttu-id="28914-148">**角色**:`Editor`</span><span class="sxs-lookup"><span data-stu-id="28914-148">**Role**: `Editor`</span></span>
    - <span data-ttu-id="28914-149">**图标**: 留空</span><span class="sxs-lookup"><span data-stu-id="28914-149">**Icon**: Leave empty</span></span>

![Info 的 URL 类型部分的屏幕截图 plist](./images/add-url-type.png)

<span data-ttu-id="28914-151">最后, 更新**GraphTutorial**项目中的代码以在身份验证过程中处理重定向。</span><span class="sxs-lookup"><span data-stu-id="28914-151">Finally, update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="28914-152">打开**AppDelegate.cs**文件, 并在该文件`using`的顶部添加以下语句。</span><span class="sxs-lookup"><span data-stu-id="28914-152">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
```

<span data-ttu-id="28914-153">添加以下行以`FinishedLaunching`在`return`语句之前运行。</span><span class="sxs-lookup"><span data-stu-id="28914-153">Add the following line to `FinishedLaunching` function just before the `return` statement.</span></span>

```cs
// Specify the Keychain access group
App.PCA.iOSKeychainSecurityGroup = "com.graphdevx.GraphTutorial";
```

<span data-ttu-id="28914-154">最后, 重写`OpenUrl`函数以将 URL 传递给 MSAL 库。</span><span class="sxs-lookup"><span data-stu-id="28914-154">Finally, override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="28914-155">将以下项添加到`AppDelegate`类中。</span><span class="sxs-lookup"><span data-stu-id="28914-155">Add the following to the `AppDelegate` class.</span></span>

```cs
// Handling redirect URL
// See: https://github.com/azuread/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

## <a name="storing-the-tokens"></a><span data-ttu-id="28914-156">存储令牌</span><span class="sxs-lookup"><span data-stu-id="28914-156">Storing the tokens</span></span>

<span data-ttu-id="28914-157">在 Xamarin 项目中使用 Microsoft 身份验证库时, 默认情况下, 它会利用本机安全存储来缓存令牌。</span><span class="sxs-lookup"><span data-stu-id="28914-157">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="28914-158">有关详细信息, 请参阅[令牌缓存序列化](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)。</span><span class="sxs-lookup"><span data-stu-id="28914-158">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="28914-159">测试登录</span><span class="sxs-lookup"><span data-stu-id="28914-159">Test sign-in</span></span>

<span data-ttu-id="28914-160">在这种情况下, 如果运行应用程序并点击 "**登录**" 按钮, 系统会提示您登录。</span><span class="sxs-lookup"><span data-stu-id="28914-160">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="28914-161">成功登录后, 您应该会看到打印到调试器输出中的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="28914-161">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Visual Studio 中的输出窗口的屏幕截图](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="28914-163">获取用户详细信息</span><span class="sxs-lookup"><span data-stu-id="28914-163">Get user details</span></span>

<span data-ttu-id="28914-164">现在, 更新`SignIn` **App.xaml.cs**中的函数以初始化`GraphServiceClient`。</span><span class="sxs-lookup"><span data-stu-id="28914-164">Now update the `SignIn` function in **App.xaml.cs** to initialize the `GraphServiceClient`.</span></span> <span data-ttu-id="28914-165">在`await GetUserInfo();`行前面添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="28914-165">Add the following code before the `await GetUserInfo();` line.</span></span>

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

<span data-ttu-id="28914-166">现在, 更新`GetUserInfo`函数以从 Microsoft Graph 中获取用户的详细信息。</span><span class="sxs-lookup"><span data-stu-id="28914-166">Now update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="28914-167">将现有的 `GetUserInfo` 函数替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="28914-167">Replace the existing `GetUserInfo` function with the following.</span></span>

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

<span data-ttu-id="28914-168">如果您现在保存更改并运行应用程序, 则在使用用户的显示名称和电子邮件地址更新 UI 后, 登录 UI。</span><span class="sxs-lookup"><span data-stu-id="28914-168">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>

