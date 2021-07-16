<!-- markdownlint-disable MD002 MD041 -->

在此练习中，你将从上一练习中扩展应用程序，以支持使用 Azure AD 进行身份验证。 这是必需的，才能获取必要的 OAuth 访问令牌来调用 Microsoft Graph。 在此步骤中，你将将适用于 .NET 的 [Microsoft 身份验证库 (MSAL) ](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) 集成到应用程序中。

1. 在 **"解决方案资源管理器**"中，展开 **GraphTutorial** 项目并右键单击 **"模型"** 文件夹。 选择 **"添加>类..."。** 将类命名 `OAuthSettings` ，然后选择"添加 **"。**

1. 打开 **OAuthSettings.cs** 文件，并将其内容替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.example.cs":::

1. 将 `YOUR_APP_ID_HERE` 替换为应用注册中的应用程序 ID。

    > [!IMPORTANT]
    > 如果你使用的是源代码管理（如 git），那么现在是从源代码管理中排除文件以避免意外泄露应用 `OAuthSettings.cs` ID 的一个好时间。

## <a name="implement-sign-in"></a>实施登录

1. 打开 **GraphTu一l** 项目中的 **App.xaml.cs** 文件，将以下语句 `using` 添加到文件顶部。

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    using TimeZoneConverter;
    ```

1. 更改 **应用程序类** 声明行来解决应用程序 的名称 **冲突**。

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. 将以下属性添加到 `App` 类。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. 接下来，在 类 `PublicClientApplication` 的构造函数中创建新的 `App` 。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. 更新 `SignIn` 函数以使用 `PublicClientApplication` 获取访问令牌。 在行上方添加以下 `await GetUserInfo();` 代码。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    此代码首先尝试以静默方式获取访问令牌。 例如，如果用户的信息已位于应用的缓存 (例如，如果用户之前在没有退出) 的情况下关闭了应用，这将成功，并且没有理由提示用户。 如果缓存中不存在用户的信息，函数 `AcquireTokenSilent().ExecuteAsync()` 将引发 `MsalUiRequiredException` 。 在这种情况下，代码调用交互式函数获取令牌 `AcquireTokenInteractive` 。

1. 更新 `SignOut` 函数以从缓存中删除用户信息。 将以下代码添加到 函数 `SignOut` 的开头。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a>更新 Android 项目以启用登录

在 Xamarin Android 项目中使用时，Microsoft 身份验证库有一些特定于 [Android 的要求](/azure/active-directory/develop/msal-net-xamarin-android-considerations)。

1. 在 **GraphTuoidl.Android** 项目中，展开 **"属性**"文件夹，然后打开"AndroidManifest.xml"。 **** If you're using Visual Studio for Mac， Control click **AndroidManifest.xml** and choose **Open With**， then Source **Code Editor**. 将全部内容替换为以下内容。

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. 打开 **MainActivity.cs，** 将以下 `using` 语句添加到文件顶部。

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. 重写 `OnActivityResult` 函数以将控件传递给 MSAL 库。 将以下内容添加到 `MainActivity` 类。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. 在 `OnCreate` 函数中，在 行后添加以下 `LoadApplication(new App());` 行。

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a>更新 iOS 项目以启用登录

> [!IMPORTANT]
> 由于 MSAL 需要使用 Entitlements.plist 文件，因此你必须Visual Studio Apple 开发人员帐户来启用预配。 如果你正在 iPhone 模拟器中运行本教程，则需要在 **GraphTutorial.iOS** 项目的设置 **Build->iOS** 捆绑签名的"自定义权利"字段中添加 **Entitlements.plist。**  有关详细信息，请参阅 [Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning)的设备预配。

在 Xamarin iOS 项目中使用时，Microsoft 身份验证库有一些特定于 [iOS 的要求](/azure/active-directory/develop/msal-net-xamarin-ios-considerations)。

1. 在"解决方案资源管理器"中，展开 **GraphTu一一l.iOS** 项目，然后打开 **Entitlements.plist** 文件。

1. 找到密钥 **链权利** ，然后选择启用 **密钥链**。

1. 在 **"钥匙链组**"中，添加格式为 的条目 `com.companyname.GraphTutorial` 。

    ![密钥链权利配置的屏幕截图](./images/enable-keychain-access.png)

1. 更新 **GraphTu一l.iOS** 项目中的代码，以处理身份验证期间重定向。 打开 **AppDelegate.cs** 文件，在文件顶部 `using` 添加以下语句。

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. 添加以下行以 `FinishedLaunching` 在行之前 `LoadApplication(new App());` 运行。

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. 重写 `OpenUrl` 函数以将 URL 传递到 MSAL 库。 将以下内容添加到 `AppDelegate` 类。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a>存储令牌

在 Xamarin 项目中使用 Microsoft 身份验证库时，它默认利用本机安全存储来缓存令牌。 有关详细信息 [，请参阅令牌](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) 缓存序列化。

## <a name="test-sign-in"></a>测试登录

此时，如果运行该应用程序并点击" **登录"** 按钮，系统将提示你登录。 成功登录后，应该会看到访问令牌打印到调试程序的输出中。

!["输出"窗口中的屏幕截图Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>获取用户详细信息

1. 将新函数添加到 **App** 类以初始化 `GraphServiceClient` 。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. 更新 `SignIn` **App.xaml.cs** 中的 函数以调用此函数，而不是 `GetUserInfo` 。 从 函数中删除 `SignIn` 以下内容。

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. 将以下内容添加到 函数 `SignIn` 的末尾。

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. 更新 `GetUserInfo` 函数，从 Microsoft Graph。 将现有的 `GetUserInfo` 函数替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. 保存更改并运行该应用程序。 登录 UI 后，会使用用户的 显示名称 和电子邮件地址进行更新。
