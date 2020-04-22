<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f58b7-101">在本练习中，你将扩展上一练习中的应用程序，以支持 Azure AD 的身份验证。</span><span class="sxs-lookup"><span data-stu-id="f58b7-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="f58b7-102">若要获取所需的 OAuth 访问令牌以调用 Microsoft Graph，这是必需的。</span><span class="sxs-lookup"><span data-stu-id="f58b7-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="f58b7-103">在此步骤中，将[.net 的 Microsoft 身份验证库（MSAL）](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)集成到应用程序中。</span><span class="sxs-lookup"><span data-stu-id="f58b7-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="f58b7-104">在 "**解决方案资源管理器**" 中，展开 " **GraphTutorial** " 项目并右键单击 "**模型**" 文件夹。</span><span class="sxs-lookup"><span data-stu-id="f58b7-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="f58b7-105">选择 "**添加 > 类 ...**"。命名该类`OAuthSettings`并选择 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="f58b7-105">Select **Add > Class...**. Name the class `OAuthSettings` and select **Add**.</span></span>

1. <span data-ttu-id="f58b7-106">打开 " **OAuthSettings.cs** " 文件，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="f58b7-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. <span data-ttu-id="f58b7-107">将`YOUR_APP_ID_HERE`替换为应用注册中的应用程序 ID。</span><span class="sxs-lookup"><span data-stu-id="f58b7-107">Replace `YOUR_APP_ID_HERE` with the application ID from your app registration.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="f58b7-108">如果您使用的是源代码管理（如 git），现在可以从源代码管理中排除`OAuthSettings.cs`该文件，以避免无意中泄漏您的应用程序 ID。</span><span class="sxs-lookup"><span data-stu-id="f58b7-108">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="f58b7-109">实施登录</span><span class="sxs-lookup"><span data-stu-id="f58b7-109">Implement sign-in</span></span>

1. <span data-ttu-id="f58b7-110">打开**GraphTutorial**项目中的`using` **App.xaml.cs**文件，并将以下语句添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="f58b7-110">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    ```

1. <span data-ttu-id="f58b7-111">更改应用程序**类声明**行以解决**应用程序**的名称冲突。</span><span class="sxs-lookup"><span data-stu-id="f58b7-111">Change the **App** class declaration line to resolve the name conflict for **Application**.</span></span>

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="f58b7-112">将以下属性添加到`App`类中。</span><span class="sxs-lookup"><span data-stu-id="f58b7-112">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. <span data-ttu-id="f58b7-113">接下来，在`PublicClientApplication` `App`类的构造函数中创建一个新的。</span><span class="sxs-lookup"><span data-stu-id="f58b7-113">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. <span data-ttu-id="f58b7-114">更新`SignIn`函数以使用`PublicClientApplication`获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="f58b7-114">Update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="f58b7-115">在该行的`await GetUserInfo();`上方添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="f58b7-115">Add the following code above the `await GetUserInfo();` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    <span data-ttu-id="f58b7-116">此代码将首先尝试以无提示方式获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="f58b7-116">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="f58b7-117">如果用户的信息已在应用程序的缓存中（例如，如果用户之前关闭了应用程序，则没有注销），这将会成功，并且没有理由提示用户。</span><span class="sxs-lookup"><span data-stu-id="f58b7-117">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="f58b7-118">如果缓存中没有用户的信息，该`AcquireTokenSilent().ExecuteAsync()`函数将引发。 `MsalUiRequiredException`</span><span class="sxs-lookup"><span data-stu-id="f58b7-118">If there is not a user's information in the cache, the `AcquireTokenSilent().ExecuteAsync()` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="f58b7-119">在这种情况下，代码调用交互式函数以获取令牌`AcquireTokenInteractive`。</span><span class="sxs-lookup"><span data-stu-id="f58b7-119">In this case, the code calls the interactive function to get a token, `AcquireTokenInteractive`.</span></span>

1. <span data-ttu-id="f58b7-120">更新`SignOut`函数以从缓存中删除用户的信息。</span><span class="sxs-lookup"><span data-stu-id="f58b7-120">Update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="f58b7-121">将以下代码添加到`SignOut`函数的开头。</span><span class="sxs-lookup"><span data-stu-id="f58b7-121">Add the following code to the beginning of the `SignOut` function.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="f58b7-122">更新 Android 项目以启用登录</span><span class="sxs-lookup"><span data-stu-id="f58b7-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="f58b7-123">在 Xamarin Android 项目中使用时，Microsoft 身份验证库具有[特定于 Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)的一些要求。</span><span class="sxs-lookup"><span data-stu-id="f58b7-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

1. <span data-ttu-id="f58b7-124">在 " **GraphTutorial** " 项目中，展开 "**属性**" 文件夹，然后打开**androidmanifest.xml**。</span><span class="sxs-lookup"><span data-stu-id="f58b7-124">In **GraphTutorial.Android** project, expand the **Properties** folder, then open **AndroidManifest.xml**.</span></span> <span data-ttu-id="f58b7-125">如果使用的是 Visual Studio for Mac，请单击 " **androidmanifest.xml** "，然后依次选择 "**打开方式**" 和 "**源代码编辑器**"。</span><span class="sxs-lookup"><span data-stu-id="f58b7-125">If you're using Visual Studio for Mac, Control click **AndroidManifest.xml** and choose **Open With**, then **Source Code Editor**.</span></span> <span data-ttu-id="f58b7-126">将整个内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="f58b7-126">Replace the entire contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. <span data-ttu-id="f58b7-127">打开**MainActivity.cs** ，并将以下`using`语句添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="f58b7-127">Open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="f58b7-128">重写`OnActivityResult`函数以将控制权传递给 MSAL 库。</span><span class="sxs-lookup"><span data-stu-id="f58b7-128">Override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="f58b7-129">将以下项添加到`MainActivity`类中。</span><span class="sxs-lookup"><span data-stu-id="f58b7-129">Add the following to the `MainActivity` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. <span data-ttu-id="f58b7-130">在`OnCreate`函数中，将以下代码行添加到`LoadApplication(new App());`行的后面。</span><span class="sxs-lookup"><span data-stu-id="f58b7-130">In the `OnCreate` function, add the following line after the `LoadApplication(new App());` line.</span></span>

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="f58b7-131">更新 iOS 项目以启用登录</span><span class="sxs-lookup"><span data-stu-id="f58b7-131">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f58b7-132">由于 MSAL 需要使用 plist 文件，因此必须将 Visual Studio 配置为 Apple 开发人员帐户才能启用预配。</span><span class="sxs-lookup"><span data-stu-id="f58b7-132">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="f58b7-133">如果您在 iPhone 模拟器中运行此教程，则需要在**GraphTutorial**项目设置的 "**自定义权利**" 字段中添加**Plist** ， **>iOS 捆绑包签名**。</span><span class="sxs-lookup"><span data-stu-id="f58b7-133">If you are running this tutorial in the iPhone simulator, you need to add **Entitlements.plist** in the **Custom Entitlements** field in **GraphTutorial.iOS** project's settings, **Build->iOS Bundle Signing**.</span></span> <span data-ttu-id="f58b7-134">有关详细信息，请参阅[适用于 Xamarin 的设备设置](/xamarin/ios/get-started/installation/device-provisioning)。</span><span class="sxs-lookup"><span data-stu-id="f58b7-134">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="f58b7-135">在 Xamarin iOS 项目中使用时，Microsoft 身份验证库具有[特定于 iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)的一些要求。</span><span class="sxs-lookup"><span data-stu-id="f58b7-135">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

1. <span data-ttu-id="f58b7-136">在 "解决方案资源管理器" 中，展开 " **GraphTutorial** " 项目，然后打开**plist**文件。</span><span class="sxs-lookup"><span data-stu-id="f58b7-136">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span>

1. <span data-ttu-id="f58b7-137">找到 "**密钥链**" 权限，然后选择 "**启用密钥链**"。</span><span class="sxs-lookup"><span data-stu-id="f58b7-137">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span>

1. <span data-ttu-id="f58b7-138">在**密钥链组**中，添加带格式`com.companyname.GraphTutorial`的条目。</span><span class="sxs-lookup"><span data-stu-id="f58b7-138">In **Keychain Groups**, add an entry with the format `com.companyname.GraphTutorial`.</span></span>

    ![密钥链权限配置的屏幕截图](./images/enable-keychain-access.png)

1. <span data-ttu-id="f58b7-140">更新**GraphTutorial**项目中的代码，以在身份验证过程中处理重定向。</span><span class="sxs-lookup"><span data-stu-id="f58b7-140">Update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="f58b7-141">打开**AppDelegate.cs**文件，并在该文件`using`的顶部添加以下语句。</span><span class="sxs-lookup"><span data-stu-id="f58b7-141">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="f58b7-142">添加以下行，使`FinishedLaunching`其恰好在`LoadApplication(new App());`该行之前运行。</span><span class="sxs-lookup"><span data-stu-id="f58b7-142">Add the following line to `FinishedLaunching` function just before the `LoadApplication(new App());` line.</span></span>

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. <span data-ttu-id="f58b7-143">重写`OpenUrl`函数以将 URL 传递给 MSAL 库。</span><span class="sxs-lookup"><span data-stu-id="f58b7-143">Override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="f58b7-144">将以下项添加到`AppDelegate`类中。</span><span class="sxs-lookup"><span data-stu-id="f58b7-144">Add the following to the `AppDelegate` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a><span data-ttu-id="f58b7-145">存储令牌</span><span class="sxs-lookup"><span data-stu-id="f58b7-145">Storing the tokens</span></span>

<span data-ttu-id="f58b7-146">在 Xamarin 项目中使用 Microsoft 身份验证库时，默认情况下，它会利用本机安全存储来缓存令牌。</span><span class="sxs-lookup"><span data-stu-id="f58b7-146">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="f58b7-147">有关详细信息，请参阅[令牌缓存序列化](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)。</span><span class="sxs-lookup"><span data-stu-id="f58b7-147">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="f58b7-148">测试登录</span><span class="sxs-lookup"><span data-stu-id="f58b7-148">Test sign-in</span></span>

<span data-ttu-id="f58b7-149">在这种情况下，如果运行应用程序并点击 "**登录**" 按钮，系统会提示您登录。</span><span class="sxs-lookup"><span data-stu-id="f58b7-149">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="f58b7-150">成功登录后，您应该会看到打印到调试器输出中的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="f58b7-150">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Visual Studio 中的输出窗口的屏幕截图](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="f58b7-152">获取用户详细信息</span><span class="sxs-lookup"><span data-stu-id="f58b7-152">Get user details</span></span>

1. <span data-ttu-id="f58b7-153">将新函数添加到**App**类以初始化`GraphServiceClient`。</span><span class="sxs-lookup"><span data-stu-id="f58b7-153">Add a new function to the **App** class to initialize the `GraphServiceClient`.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. <span data-ttu-id="f58b7-154">更新 App.xaml.cs `SignIn`中的**App.xaml.cs**函数以调用此函数，而`GetUserInfo`不是。</span><span class="sxs-lookup"><span data-stu-id="f58b7-154">Update the `SignIn` function in **App.xaml.cs** to call this function instead of `GetUserInfo`.</span></span> <span data-ttu-id="f58b7-155">从`SignIn`函数中删除以下项。</span><span class="sxs-lookup"><span data-stu-id="f58b7-155">Remove the following from the `SignIn` function.</span></span>

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. <span data-ttu-id="f58b7-156">将以下项添加到`SignIn`函数的末尾。</span><span class="sxs-lookup"><span data-stu-id="f58b7-156">Add the following to the end of the `SignIn` function.</span></span>

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. <span data-ttu-id="f58b7-157">更新`GetUserInfo`函数以从 Microsoft Graph 中获取用户的详细信息。</span><span class="sxs-lookup"><span data-stu-id="f58b7-157">Update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="f58b7-158">将现有的 `GetUserInfo` 函数替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="f58b7-158">Replace the existing `GetUserInfo` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. <span data-ttu-id="f58b7-159">保存更改并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="f58b7-159">Save your changes and run the app.</span></span> <span data-ttu-id="f58b7-160">登录后，将使用用户的显示名称和电子邮件地址更新 UI。</span><span class="sxs-lookup"><span data-stu-id="f58b7-160">After sign-in the UI is updated with the user's display name and email address.</span></span>
