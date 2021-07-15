<!-- markdownlint-disable MD002 MD041 -->

1. 打开 Visual Studio ，选择“**新建一个项目**”。

1. 在创建新 **项目对话框中，** 选择移动应用 (**Xamarin.Forms) ，** 然后选择下一 **步**。

    ![Visual Studio 2019 创建新项目对话框](images/new-project-dialog.png)

1. 在"**配置新项目"** 对话框中，输入"Project名称"和"解决方案名称"， `GraphTutorial` 然后选择"创建 **"。**  

    > [!IMPORTANT]
    > 请确保为这些实验室说明中指定的Visual Studio Project输入完全相同的名称。 Visual Studio 项目名称在代码中成为了命名空间的一部分。 在这些说明里的代码依赖于匹配在这些说明中指定的 Visual Studio 项目名称的命名空间。 如果你使用不同的项目名称，代码不会编译，除非你调整所有的命名空间来匹配你在创建项目时输入的 Visual Studio 项目名称。

    ![Visual Studio 2019 配置新项目对话框](images/configure-new-project-dialog.png)

1. 在" **新建跨平台应用** "对话框中，选择 **"空白** "模板，然后选择想要在"平台"下构建 **的平台**。 选择 **"** 确定"以创建解决方案。

    ![Visual Studio 2019 新的跨平台应用对话框](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a>安装程序包

在继续之前，请安装一些NuGet包，你稍后会使用它。

- 用于处理 Azure AD 身份验证和令牌管理的[Microsoft.Identity.Client。](https://www.nuget.org/packages/Microsoft.Identity.Client/)
- [Microsoft。Graph](https://www.nuget.org/packages/Microsoft.Graph/)用于调用 Microsoft Graph。
- [用于跨平台处理时区的 TimeZoneConverter。](https://www.nuget.org/packages/TimeZoneConverter/)

1. 选择“**工具 > NuGet 程序包管理器 > 程序包管理控制台**”。

1. 在“程序包管理器控制台”中，输入以下命令。

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.34.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.34.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.34.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 4.0.0 -Project GraphTutorial
    Install-Package TimeZoneConverter -Project GraphTutorial
    ```

## <a name="design-the-app"></a>设计应用

首先更新 类 `App` 以添加变量以跟踪身份验证状态和登录用户。

1. 在 **"解决方案资源管理器**"中，展开 **GraphTutorial** 项目，然后展开 **App.xaml** 文件。 打开 **App.xaml.cs** 文件，将以下语句 `using` 添加到文件顶部。

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. 将 `INotifyPropertyChanged` 接口添加到类声明。

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. 将以下属性添加到 `App` 类。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. 将以下函数添加到 `App` 类。 目前 `SignIn` `SignOut` ， 、 和 `GetUserInfo` 函数只是占位符。

    ```csharp
    public async Task SignIn()
    {
        await GetUserInfo();

        IsSignedIn = true;
    }

    public async Task SignOut()
    {
        UserPhoto = null;
        UserName = string.Empty;
        UserEmail = string.Empty;
        IsSignedIn = false;
    }

    private async Task GetUserInfo()
    {
        UserPhoto = ImageSource.FromStream(() => GetUserPhoto());
        UserName = "Adele Vance";
        UserEmail = "adelev@contoso.com";
    }

    private Stream GetUserPhoto()
    {
        // Return the default photo
        return Assembly.GetExecutingAssembly().GetManifestResourceStream("GraphTutorial.no-profile-pic.png");
    }
    ```

1. `GetUserPhoto`函数现在返回默认照片。 你可以提供你自己的文件，也可以从 下载示例中使用的文件[GitHub。](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png) 如果使用自己的文件，请将其重命名为 **no-profile-pic.png。**

1. 将文件复制到 **./GraphTu一l/GraphTutorial** 目录。

1. 右键单击"解决方案资源管理器 **"中的文件，** 然后选择"属性 **"。** 在"**属性"** 窗口中，将"生成 **操作"** 的值更改为 **"嵌入资源"。**

    ![PNG 文件的"属性"窗口屏幕截图](./images/png-file-properties.png)

### <a name="app-navigation"></a>应用导航

在此部分中，你将应用程序的主页更改为 [FlyoutPage](/xamarin/xamarin-forms/app-fundamentals/navigation/flyoutpage)。 这将提供在应用中的视图之间切换的导航菜单。

1. 打开 **GraphTu一** l 项目中的 **MainPage.xaml** 文件，并将其内容替换为以下内容。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml":::

#### <a name="implement-the-menu"></a>实现菜单

1. 右键单击 **GraphTutorial** 项目，然后选择 **添加**，然后选择新 **文件夹**。 将文件夹命名 `Models` 。

1. 右键单击 **"模型"** 文件夹，然后选择 **"添加"，** 然后选择"**类..."。** 将类命名 `NavMenuItem` ，然后选择"添加 **"。**

1. 打开 **NavMenuItem.cs** 文件，并将其内容替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. 右键单击 **GraphTutorial** 项目，然后选择 **"添加**"，然后选择" **新建项目..."。** 选择 **"内容页面** "，并命名该页面 `MenuPage` 。 选择 **添加**。

1. 打开 **MenuPage.xaml** 文件，并将其内容替换为以下内容。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml":::

1. 在 **"解决方案资源管理器"中展开 MenuPage.xaml** 并打开 **MenuPage.xaml.cs** 文件。  将其内容替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > Visual Studio将报告 **MenuPage.xaml.cs 中的错误**。 这些错误将在稍后的步骤中解决。

#### <a name="implement-the-welcome-page"></a>实现欢迎页面

1. 右键单击 **GraphTutorial** 项目，然后选择 **"添加**"，然后选择" **新建项目..."。** 选择 **"内容页面** "，并命名该页面 `WelcomePage` 。 选择 **添加**。 打开 **WelcomePage.xaml** 文件，并将其内容替换为以下内容。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml":::

1. 在 **"解决方案资源管理器"中展开 WelcomePage.xaml** 并打开 **WelcomePage.xaml.cs** 文件。  将以下函数添加到 `WelcomePage` 类。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-and-new-event-pages"></a>添加日历和新事件页面

现在，添加一个日历页和一个新的事件页。 现在这些只是占位符。

1. 右键单击 **GraphTutorial** 项目，然后选择 **"添加**"，然后选择" **新建项目..."。** 选择 **"内容页面** "，并命名该页面 `CalendarPage` 。 选择 **添加**。

1. 右键单击 **GraphTutorial** 项目，然后选择 **"添加**"，然后选择" **新建项目..."。** 选择 **"内容页面** "，并命名该页面 `NewEventPage` 。 选择 **添加**。

#### <a name="update-mainpage-code-behind"></a>更新 MainPage 代码隐藏

现在，所有页面已就位，请更新 **MainPage.xaml** 的代码隐藏。

1. 在 **"解决方案资源管理器"中展开 MainPage.xaml，** 打开 **MainPage.xaml.cs** 文件，并将其全部内容替换为以下内容。 

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. 保存所有更改。 右键单击你想要在 Android、iOS 或 UWP (运行的项目，然后选择) 设置为"启动 **"Project。** 按 **F5** 或选择"调试 **>"开始** 调试"Visual Studio。

    ![应用程序的 Android、iOS 和 UWP 版本的屏幕截图](./images/welcome-page.png)
