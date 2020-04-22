<!-- markdownlint-disable MD002 MD041 -->

在本练习中，你将扩展上一练习中的应用程序，以支持 Azure AD 的身份验证。 若要获取所需的 OAuth 访问令牌以调用 Microsoft Graph，这是必需的。 在此步骤中，将[.net 的 Microsoft 身份验证库（MSAL）](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)集成到应用程序中。

1. 在 "**解决方案资源管理器**" 中，展开 " **GraphTutorial** " 项目并右键单击 "**模型**" 文件夹。 选择 "**添加 > 类 ...**"。命名该类`OAuthSettings`并选择 "**添加**"。

1. 打开 " **OAuthSettings.cs** " 文件，并将其内容替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. 将`YOUR_APP_ID_HERE`替换为应用注册中的应用程序 ID。

    > [!IMPORTANT]
    > 如果您使用的是源代码管理（如 git），现在可以从源代码管理中排除`OAuthSettings.cs`该文件，以避免无意中泄漏您的应用程序 ID。

## <a name="implement-sign-in"></a>实施登录

1. 打开**GraphTutorial**项目中的`using` **App.xaml.cs**文件，并将以下语句添加到文件顶部。

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    ```

1. 更改应用程序**类声明**行以解决**应用程序**的名称冲突。

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. 将以下属性添加到`App`类中。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. 接下来，在`PublicClientApplication` `App`类的构造函数中创建一个新的。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. 更新`SignIn`函数以使用`PublicClientApplication`获取访问令牌。 在该行的`await GetUserInfo();`上方添加以下代码。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    此代码将首先尝试以无提示方式获取访问令牌。 如果用户的信息已在应用程序的缓存中（例如，如果用户之前关闭了应用程序，则没有注销），这将会成功，并且没有理由提示用户。 如果缓存中没有用户的信息，该`AcquireTokenSilent().ExecuteAsync()`函数将引发。 `MsalUiRequiredException` 在这种情况下，代码调用交互式函数以获取令牌`AcquireTokenInteractive`。

1. 更新`SignOut`函数以从缓存中删除用户的信息。 将以下代码添加到`SignOut`函数的开头。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a>更新 Android 项目以启用登录

在 Xamarin Android 项目中使用时，Microsoft 身份验证库具有[特定于 Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)的一些要求。

1. 在 " **GraphTutorial** " 项目中，展开 "**属性**" 文件夹，然后打开**androidmanifest.xml**。 如果使用的是 Visual Studio for Mac，请单击 " **androidmanifest.xml** "，然后依次选择 "**打开方式**" 和 "**源代码编辑器**"。 将整个内容替换为以下内容。

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. 打开**MainActivity.cs** ，并将以下`using`语句添加到文件顶部。

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. 重写`OnActivityResult`函数以将控制权传递给 MSAL 库。 将以下项添加到`MainActivity`类中。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. 在`OnCreate`函数中，将以下代码行添加到`LoadApplication(new App());`行的后面。

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a>更新 iOS 项目以启用登录

> [!IMPORTANT]
> 由于 MSAL 需要使用 plist 文件，因此必须将 Visual Studio 配置为 Apple 开发人员帐户才能启用预配。 如果您在 iPhone 模拟器中运行此教程，则需要在**GraphTutorial**项目设置的 "**自定义权利**" 字段中添加**Plist** ， **>iOS 捆绑包签名**。 有关详细信息，请参阅[适用于 Xamarin 的设备设置](/xamarin/ios/get-started/installation/device-provisioning)。

在 Xamarin iOS 项目中使用时，Microsoft 身份验证库具有[特定于 iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)的一些要求。

1. 在 "解决方案资源管理器" 中，展开 " **GraphTutorial** " 项目，然后打开**plist**文件。

1. 找到 "**密钥链**" 权限，然后选择 "**启用密钥链**"。

1. 在**密钥链组**中，添加带格式`com.companyname.GraphTutorial`的条目。

    ![密钥链权限配置的屏幕截图](./images/enable-keychain-access.png)

1. 更新**GraphTutorial**项目中的代码，以在身份验证过程中处理重定向。 打开**AppDelegate.cs**文件，并在该文件`using`的顶部添加以下语句。

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. 添加以下行，使`FinishedLaunching`其恰好在`LoadApplication(new App());`该行之前运行。

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. 重写`OpenUrl`函数以将 URL 传递给 MSAL 库。 将以下项添加到`AppDelegate`类中。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a>存储令牌

在 Xamarin 项目中使用 Microsoft 身份验证库时，默认情况下，它会利用本机安全存储来缓存令牌。 有关详细信息，请参阅[令牌缓存序列化](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)。

## <a name="test-sign-in"></a>测试登录

在这种情况下，如果运行应用程序并点击 "**登录**" 按钮，系统会提示您登录。 成功登录后，您应该会看到打印到调试器输出中的访问令牌。

![Visual Studio 中的输出窗口的屏幕截图](./images/debugger-access-token.png)

## <a name="get-user-details"></a>获取用户详细信息

1. 将新函数添加到**App**类以初始化`GraphServiceClient`。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. 更新 App.xaml.cs `SignIn`中的**App.xaml.cs**函数以调用此函数，而`GetUserInfo`不是。 从`SignIn`函数中删除以下项。

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. 将以下项添加到`SignIn`函数的末尾。

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. 更新`GetUserInfo`函数以从 Microsoft Graph 中获取用户的详细信息。 将现有的 `GetUserInfo` 函数替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. 保存更改并运行该应用程序。 登录后，将使用用户的显示名称和电子邮件地址更新 UI。
