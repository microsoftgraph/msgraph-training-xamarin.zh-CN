<!-- markdownlint-disable MD002 MD041 -->

1. 打开Visual Studio，然后选择 **"新建项目"。**

1. 在"**新建项目"对话框中**，选择"移动应用" (**Xamarin.Forms) ，然后选择"下一步****"。**

    ![Visual Studio 2019 新建项目对话框](images/new-project-dialog.png)

1. 在 **"配置新项目"对话框中**，输入 `GraphTutorial` 项目名称和 **解决方案名称**，然后选择"**创建"。** 

    > [!IMPORTANT]
    > 确保为这些实验室说明中指定的 Visual Studio 项目输入完全相同的名称。 项目Visual Studio名称将成为代码中命名空间的一部分。 这些说明中的代码取决于匹配这些Visual Studio中指定的项目名称的命名空间。 如果使用其他项目名称，则代码将不会编译，除非您调整所有命名空间以匹配Visual Studio项目名称时输入的项目名称。

    ![Visual Studio 2019 配置新项目对话框](images/configure-new-project-dialog.png)

1. 在 **"新建跨平台应用** "对话框中，选择 **空白** 模板，然后选择想要在平台下构建 **的平台**。 选择 **"** 确定"以创建解决方案。

    ![Visual Studio 2019 新的跨平台应用对话框](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a>安装程序包

在继续之前，请安装一些稍后将使用的其他 NuGet 程序包。

- 用于处理 Azure AD 身份验证和令牌管理的[Microsoft.Identity.Client。](https://www.nuget.org/packages/Microsoft.Identity.Client/)
- [用于调用 Microsoft Graph](https://www.nuget.org/packages/Microsoft.Graph/) 的 Microsoft.Graph。
- [用于跨平台处理时区的 TimeZoneConverter。](https://www.nuget.org/packages/TimeZoneConverter/)

1. Select **Tools > NuGet 程序包管理器 > 程序包管理器 Console.**

1. 在程序包管理器控制台中，输入以下命令。

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.21.0 -Project GraphTutorial
    Install-Package TimeZoneConverter -Version 3.3.0 -Project GraphTutorial
    ```

## <a name="design-the-app"></a>设计应用

首先更新类 `App` 以添加变量以跟踪身份验证状态和登录用户。

1. 在 **解决方案资源管理器** 中，展开 **GraphTu一l** 项目，然后展开 **App.xaml** 文件。 打开 **App.xaml.cs** 文件，将以下 `using` 语句添加到文件顶部。

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

1. 将以下函数添加到 `App` 类。 目前 `SignIn` `SignOut` ， 和 `GetUserInfo` 函数只是占位符。

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

1. 该 `GetUserPhoto` 函数现在返回默认照片。 你可以提供你自己的文件，也可以从 [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)下载示例中使用的文件。 如果使用自己的文件，请将其重命名 **为no-profile-pic.png。**

1. 将文件复制到 **./GraphTu一l/GraphTu一l** 目录。

1. 右键单击解决方案资源管理器中的 **文件，** 然后选择"**属性"。** 在"**属性**"窗口中，将"生成 **操作"的值更改为****"嵌入"资源**。

    ![PNG 文件的属性窗口屏幕截图](./images/png-file-properties.png)

### <a name="app-navigation"></a>应用导航

在此部分中，将应用程序的主页更改为"母版 [-细节"页](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page)。 这将提供在应用中的视图之间切换的导航菜单。

1. 打开 **GraphTu一** l 项目中的 **MainPage.xaml** 文件，并将其内容替换为以下内容。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml" id="MainPageXamlSnippet":::

#### <a name="implement-the-menu"></a>实现菜单

1. 右键单击 **GraphTu一l** 项目，然后选择 **"添加**"，然后选择"**新建文件夹"。** 命名文件夹 `Models` 。

1. 右键单击 **Models** 文件夹，然后选择 **"添加**"，然后选择"**类..."。** 命名类 `NavMenuItem` ，然后选择"**添加"。**

1. 打开 **NavMenuItem.cs** 文件，并将其内容替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. 右键单击 **GraphTu一l** 项目，然后选择 **"添加**"，然后选择 **"新项目..."。** 选择 **内容页** 并命名该页面 `MenuPage` 。 选择“**添加**”。

1. 打开 **MenuPage.xaml** 文件，并将其内容替换为以下内容。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml" id="MenuPageXamlSnippet":::

1. 在 **解决方案资源管理器中展开 MenuPage.xaml** 并 **打开MenuPage.xaml.cs文件**。  将其内容替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > Visual Studio中 **报告MenuPage.xaml.cs。** 这些错误将在稍后步骤中解决。

#### <a name="implement-the-welcome-page"></a>实现欢迎页面

1. 右键单击 **GraphTu一l** 项目，然后选择 **"添加**"，然后选择 **"新项目..."。** 选择 **内容页** 并命名该页面 `WelcomePage` 。 选择“**添加**”。 打开 **WelcomePage.xaml** 文件，并将其内容替换为以下内容。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml" id="WelcomePageXamlSnippet":::

1. 在 **解决方案资源管理器中展开 WelcomePage.xaml** 并 **打开WelcomePage.xaml.cs文件**。  将以下函数添加到 `WelcomePage` 类。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-and-new-event-pages"></a>添加日历和新事件页面

现在，添加日历页和新的事件页面。 现在这些只是占位符。

1. 右键单击 **GraphTu一l** 项目，然后选择 **"添加**"，然后选择 **"新项目..."。** 选择 **内容页** 并命名该页面 `CalendarPage` 。 选择“**添加**”。

1. 右键单击 **GraphTu一l** 项目，然后选择 **"添加**"，然后选择 **"新项目..."。** 选择 **内容页** 并命名该页面 `NewEventPage` 。 选择“**添加**”。

#### <a name="update-mainpage-code-behind"></a>更新 MainPage 代码隐藏

现在，所有页面已就位，请更新 **MainPage.xaml 的代码隐藏。**

1. 在 **解决方案资源管理器中展开 MainPage.xaml** 并打开 MainPage.xaml.cs **文件，** 并将其全部内容替换为以下内容。 

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. 保存所有更改。 右键单击你想要在 Android、iOS 或 UWP (运行的项目，) "设置为 **启动项目"。** 按 **F5** 或选择>**开始调试Visual Studio。**

    ![应用程序的 Android、iOS 和 UWP 版本的屏幕截图](./images/welcome-page.png)
