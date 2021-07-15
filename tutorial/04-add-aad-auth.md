<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f7b49-101">在此练习中，你将从上一练习中扩展应用程序，以支持使用 Azure AD 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="f7b49-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="f7b49-102">这是必需的，才能获取必要的 OAuth 访问令牌来调用 Microsoft Graph。</span><span class="sxs-lookup"><span data-stu-id="f7b49-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="f7b49-103">在此步骤中，你将将适用于 .NET 的 [Microsoft 身份验证库 (MSAL) ](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) 集成到应用程序中。</span><span class="sxs-lookup"><span data-stu-id="f7b49-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="f7b49-104">在 **"解决方案资源管理器**"中，展开 **GraphTutorial** 项目并右键单击 **"模型"** 文件夹。</span><span class="sxs-lookup"><span data-stu-id="f7b49-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="f7b49-105">选择 **"添加>类..."。** 将类命名 `OAuthSettings` ，然后选择"添加 **"。**</span><span class="sxs-lookup"><span data-stu-id="f7b49-105">Select **Add > Class...**. Name the class `OAuthSettings` and select **Add**.</span></span>

1. <span data-ttu-id="f7b49-106">打开 **OAuthSettings.cs** 文件，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="f7b49-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. <span data-ttu-id="f7b49-107">将 `YOUR_APP_ID_HERE` 替换为应用注册中的应用程序 ID。</span><span class="sxs-lookup"><span data-stu-id="f7b49-107">Replace `YOUR_APP_ID_HERE` with the application ID from your app registration.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="f7b49-108">如果你使用的是源代码管理（如 git），那么现在是从源代码管理中排除文件以避免意外泄露应用 `OAuthSettings.cs` ID 的一个好时间。</span><span class="sxs-lookup"><span data-stu-id="f7b49-108">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="f7b49-109">实施登录</span><span class="sxs-lookup"><span data-stu-id="f7b49-109">Implement sign-in</span></span>

1. <span data-ttu-id="f7b49-110">打开 **GraphTu一l** 项目中的 **App.xaml.cs** 文件，将以下语句 `using` 添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="f7b49-110">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    using TimeZoneConverter;
    ```

1. <span data-ttu-id="f7b49-111">更改 **应用程序类** 声明行来解决应用程序 的名称 **冲突**。</span><span class="sxs-lookup"><span data-stu-id="f7b49-111">Change the **App** class declaration line to resolve the name conflict for **Application**.</span></span>

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="f7b49-112">将以下属性添加到 `App` 类。</span><span class="sxs-lookup"><span data-stu-id="f7b49-112">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. <span data-ttu-id="f7b49-113">接下来，在 类 `PublicClientApplication` 的构造函数中创建新的 `App` 。</span><span class="sxs-lookup"><span data-stu-id="f7b49-113">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. <span data-ttu-id="f7b49-114">更新 `SignIn` 函数以使用 `PublicClientApplication` 获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="f7b49-114">Update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="f7b49-115">在行上方添加以下 `await GetUserInfo();` 代码。</span><span class="sxs-lookup"><span data-stu-id="f7b49-115">Add the following code above the `await GetUserInfo();` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    <span data-ttu-id="f7b49-116">此代码首先尝试以静默方式获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="f7b49-116">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="f7b49-117">例如，如果用户的信息已位于应用的缓存 (例如，如果用户之前在没有退出) 的情况下关闭了应用，这将成功，并且没有理由提示用户。</span><span class="sxs-lookup"><span data-stu-id="f7b49-117">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="f7b49-118">如果缓存中不存在用户的信息，函数 `AcquireTokenSilent().ExecuteAsync()` 将引发 `MsalUiRequiredException` 。</span><span class="sxs-lookup"><span data-stu-id="f7b49-118">If there is not a user's information in the cache, the `AcquireTokenSilent().ExecuteAsync()` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="f7b49-119">在这种情况下，代码调用交互式函数获取令牌 `AcquireTokenInteractive` 。</span><span class="sxs-lookup"><span data-stu-id="f7b49-119">In this case, the code calls the interactive function to get a token, `AcquireTokenInteractive`.</span></span>

1. <span data-ttu-id="f7b49-120">更新 `SignOut` 函数以从缓存中删除用户信息。</span><span class="sxs-lookup"><span data-stu-id="f7b49-120">Update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="f7b49-121">将以下代码添加到 函数 `SignOut` 的开头。</span><span class="sxs-lookup"><span data-stu-id="f7b49-121">Add the following code to the beginning of the `SignOut` function.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="f7b49-122">更新 Android 项目以启用登录</span><span class="sxs-lookup"><span data-stu-id="f7b49-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="f7b49-123">在 Xamarin Android 项目中使用时，Microsoft 身份验证库有一些特定于 [Android 的要求](/azure/active-directory/develop/msal-net-xamarin-android-considerations)。</span><span class="sxs-lookup"><span data-stu-id="f7b49-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](/azure/active-directory/develop/msal-net-xamarin-android-considerations).</span></span>

1. <span data-ttu-id="f7b49-124">在 **GraphTuoidl.Android** 项目中，展开 **"属性**"文件夹，然后打开"AndroidManifest.xml"。 \*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="f7b49-124">In **GraphTutorial.Android** project, expand the **Properties** folder, then open **AndroidManifest.xml**.</span></span> <span data-ttu-id="f7b49-125">If you're using Visual Studio for Mac， Control click **AndroidManifest.xml** and choose **Open With**， then Source **Code Editor**.</span><span class="sxs-lookup"><span data-stu-id="f7b49-125">If you're using Visual Studio for Mac, Control click **AndroidManifest.xml** and choose **Open With**, then **Source Code Editor**.</span></span> <span data-ttu-id="f7b49-126">将全部内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="f7b49-126">Replace the entire contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. <span data-ttu-id="f7b49-127">打开 **MainActivity.cs，** 将以下 `using` 语句添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="f7b49-127">Open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="f7b49-128">重写 `OnActivityResult` 函数以将控件传递给 MSAL 库。</span><span class="sxs-lookup"><span data-stu-id="f7b49-128">Override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="f7b49-129">将以下内容添加到 `MainActivity` 类。</span><span class="sxs-lookup"><span data-stu-id="f7b49-129">Add the following to the `MainActivity` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. <span data-ttu-id="f7b49-130">在 `OnCreate` 函数中，在 行后添加以下 `LoadApplication(new App());` 行。</span><span class="sxs-lookup"><span data-stu-id="f7b49-130">In the `OnCreate` function, add the following line after the `LoadApplication(new App());` line.</span></span>

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="f7b49-131">更新 iOS 项目以启用登录</span><span class="sxs-lookup"><span data-stu-id="f7b49-131">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f7b49-132">由于 MSAL 需要使用 Entitlements.plist 文件，因此你必须Visual Studio Apple 开发人员帐户来启用预配。</span><span class="sxs-lookup"><span data-stu-id="f7b49-132">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="f7b49-133">如果你正在 iPhone 模拟器中运行本教程，则需要在 **GraphTutorial.iOS** 项目的设置 **Build->iOS** 捆绑签名的"自定义权利"字段中添加 **Entitlements.plist。** </span><span class="sxs-lookup"><span data-stu-id="f7b49-133">If you are running this tutorial in the iPhone simulator, you need to add **Entitlements.plist** in the **Custom Entitlements** field in **GraphTutorial.iOS** project's settings, **Build->iOS Bundle Signing**.</span></span> <span data-ttu-id="f7b49-134">有关详细信息，请参阅 [Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning)的设备预配。</span><span class="sxs-lookup"><span data-stu-id="f7b49-134">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="f7b49-135">在 Xamarin iOS 项目中使用时，Microsoft 身份验证库有一些特定于 [iOS 的要求](/azure/active-directory/develop/msal-net-xamarin-ios-considerations)。</span><span class="sxs-lookup"><span data-stu-id="f7b49-135">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](/azure/active-directory/develop/msal-net-xamarin-ios-considerations).</span></span>

1. <span data-ttu-id="f7b49-136">在"解决方案资源管理器"中，展开 **GraphTu一一l.iOS** 项目，然后打开 **Entitlements.plist** 文件。</span><span class="sxs-lookup"><span data-stu-id="f7b49-136">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span>

1. <span data-ttu-id="f7b49-137">找到密钥 **链权利** ，然后选择启用 **密钥链**。</span><span class="sxs-lookup"><span data-stu-id="f7b49-137">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span>

1. <span data-ttu-id="f7b49-138">在 **"钥匙链组**"中，添加格式为 的条目 `com.companyname.GraphTutorial` 。</span><span class="sxs-lookup"><span data-stu-id="f7b49-138">In **Keychain Groups**, add an entry with the format `com.companyname.GraphTutorial`.</span></span>

    ![密钥链权利配置的屏幕截图](./images/enable-keychain-access.png)

1. <span data-ttu-id="f7b49-140">更新 **GraphTu一l.iOS** 项目中的代码，以处理身份验证期间重定向。</span><span class="sxs-lookup"><span data-stu-id="f7b49-140">Update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="f7b49-141">打开 **AppDelegate.cs** 文件，在文件顶部 `using` 添加以下语句。</span><span class="sxs-lookup"><span data-stu-id="f7b49-141">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="f7b49-142">添加以下行以 `FinishedLaunching` 在行之前 `LoadApplication(new App());` 运行。</span><span class="sxs-lookup"><span data-stu-id="f7b49-142">Add the following line to `FinishedLaunching` function just before the `LoadApplication(new App());` line.</span></span>

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. <span data-ttu-id="f7b49-143">重写 `OpenUrl` 函数以将 URL 传递到 MSAL 库。</span><span class="sxs-lookup"><span data-stu-id="f7b49-143">Override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="f7b49-144">将以下内容添加到 `AppDelegate` 类。</span><span class="sxs-lookup"><span data-stu-id="f7b49-144">Add the following to the `AppDelegate` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a><span data-ttu-id="f7b49-145">存储令牌</span><span class="sxs-lookup"><span data-stu-id="f7b49-145">Storing the tokens</span></span>

<span data-ttu-id="f7b49-146">在 Xamarin 项目中使用 Microsoft 身份验证库时，它默认利用本机安全存储来缓存令牌。</span><span class="sxs-lookup"><span data-stu-id="f7b49-146">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="f7b49-147">有关详细信息 [，请参阅令牌](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) 缓存序列化。</span><span class="sxs-lookup"><span data-stu-id="f7b49-147">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="f7b49-148">测试登录</span><span class="sxs-lookup"><span data-stu-id="f7b49-148">Test sign-in</span></span>

<span data-ttu-id="f7b49-149">此时，如果运行该应用程序并点击" **登录"** 按钮，系统将提示你登录。</span><span class="sxs-lookup"><span data-stu-id="f7b49-149">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="f7b49-150">成功登录后，应该会看到访问令牌打印到调试程序的输出中。</span><span class="sxs-lookup"><span data-stu-id="f7b49-150">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

!["输出"窗口中的屏幕截图Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="f7b49-152">获取用户详细信息</span><span class="sxs-lookup"><span data-stu-id="f7b49-152">Get user details</span></span>

1. <span data-ttu-id="f7b49-153">将新函数添加到 **App** 类以初始化 `GraphServiceClient` 。</span><span class="sxs-lookup"><span data-stu-id="f7b49-153">Add a new function to the **App** class to initialize the `GraphServiceClient`.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. <span data-ttu-id="f7b49-154">更新 `SignIn` **App.xaml.cs** 中的 函数以调用此函数，而不是 `GetUserInfo` 。</span><span class="sxs-lookup"><span data-stu-id="f7b49-154">Update the `SignIn` function in **App.xaml.cs** to call this function instead of `GetUserInfo`.</span></span> <span data-ttu-id="f7b49-155">从 函数中删除 `SignIn` 以下内容。</span><span class="sxs-lookup"><span data-stu-id="f7b49-155">Remove the following from the `SignIn` function.</span></span>

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. <span data-ttu-id="f7b49-156">将以下内容添加到 函数 `SignIn` 的末尾。</span><span class="sxs-lookup"><span data-stu-id="f7b49-156">Add the following to the end of the `SignIn` function.</span></span>

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. <span data-ttu-id="f7b49-157">更新 `GetUserInfo` 函数，从 Microsoft Graph。</span><span class="sxs-lookup"><span data-stu-id="f7b49-157">Update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="f7b49-158">将现有的 `GetUserInfo` 函数替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="f7b49-158">Replace the existing `GetUserInfo` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. <span data-ttu-id="f7b49-159">保存更改并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="f7b49-159">Save your changes and run the app.</span></span> <span data-ttu-id="f7b49-160">登录 UI 后，会使用用户的 显示名称 和电子邮件地址进行更新。</span><span class="sxs-lookup"><span data-stu-id="f7b49-160">After sign-in the UI is updated with the user's display name and email address.</span></span>
