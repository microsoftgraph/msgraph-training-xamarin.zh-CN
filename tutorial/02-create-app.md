<!-- markdownlint-disable MD002 MD041 -->

1. 打开 Visual Studio，然后选择 "**新建项目**"。

1. 在 "**创建新项目**" 对话框中，选择 "**移动应用（Xamarin. Forms）**"，然后选择 "**下一步**"。

    ![Visual Studio 2019 "新建项目" 对话框](images/new-project-dialog.png)

1. 在 "**配置新项目**" 对话框中， `GraphTutorial`输入**项目名称**和**解决方案名称**，然后选择 "**创建**"。

    > [!IMPORTANT]
    > 确保为在这些实验室说明中指定的 Visual Studio 项目输入完全相同的名称。 Visual Studio 项目名称将成为代码中的命名空间的一部分。 这些指令中的代码取决于与这些说明中指定的 Visual Studio 项目名称匹配的命名空间。 如果使用其他项目名称，则代码将不会编译，除非您调整所有命名空间以匹配您在创建项目时输入的 Visual Studio 项目名称。

    ![Visual Studio 2019 配置新项目对话框](images/configure-new-project-dialog.png)

1. 在 "**新建跨平台应用程序**" 对话框中，选择 "**空白**" 模板，然后选择要在 "**平台**" 下构建的平台。 选择 **"确定"** 以创建解决方案。

    ![Visual Studio 2019 新建跨平台应用程序对话框](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a>安装程序包

在继续之前，请安装稍后将使用的一些其他 NuGet 包。

- 用于处理 Azure AD 身份验证和令牌管理的[Microsoft Identity。](https://www.nuget.org/packages/Microsoft.Identity.Client/)
- 用于调用 Microsoft Graph 的[microsoft](https://www.nuget.org/packages/Microsoft.Graph/) graph。

1. 选择 "**工具" > NuGet 包管理器 "> 程序包管理器控制台**"。

1. 在 "程序包管理器控制台" 中，输入以下命令。

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.0.1 -Project GraphTutorial
    ```

## <a name="design-the-app"></a>设计应用程序

首先更新`App`类以添加用于跟踪身份验证状态和已登录用户的变量。

1. 在 "**解决方案资源管理器**" 中，展开 " **GraphTutorial** " 项目，然后展开**app.xaml**文件。 打开**App.xaml.cs**文件，并将以下`using`语句添加到文件顶部。

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. 将`INotifyPropertyChanged`接口添加到类声明中。

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. 将以下属性添加到`App`类中。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. 将以下函数添加到`App`类中。 `SignIn`、 `SignOut`和`GetUserInfo`函数现在只是占位符。

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

1. `GetUserPhoto`函数现在返回默认照片。 你可以提供自己的文件，也可以从[GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)中的示例中下载所使用的文件。 如果您使用自己的文件，请将其重命名为**no-profile-pic**。

1. 将文件复制到 **/GraphTutorial/GraphTutorial**目录。

1. 在 "**解决方案资源管理器**" 中右键单击该文件，然后选择 "**属性**"。 在 "**属性**" 窗口中，将 "**生成操作**" 的值更改为 "**嵌入的资源**"。

    ![PNG 文件的 "属性" 窗口的屏幕截图](./images/png-file-properties.png)

### <a name="app-navigation"></a>应用程序导航

在本节中，您将把应用程序的主页面更改为一个[主-详细信息页面](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page)。 这将提供导航菜单，以便在应用程序中的视图之间切换。

1. 打开**GraphTutorial**项目中的**MainPage**文件，并将其内容替换为以下内容。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml":::

#### <a name="implement-the-menu"></a>实现菜单

1. 右键单击 " **GraphTutorial** " 项目，然后依次选择 "**添加**"、"**新建文件夹**"。 将文件夹`Models`命名为。

1. 右键单击 "**模型**" 文件夹，然后依次选择 "**添加**"、"**类 ...**"。命名该类`NavMenuItem`并选择 "**添加**"。

1. 打开 " **NavMenuItem.cs** " 文件，并将其内容替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. 右键单击 " **GraphTutorial** " 项目，然后依次选择 "**添加**"、"**新建项目 ...**"。选择 "**内容页面**" 并命名`MenuPage`页面。 选择“**添加**”。

1. 打开**MenuPage**文件，并将其内容替换为以下内容。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml":::

1. 展开 "**解决方案资源管理器**" 中的 " **MenuPage** "，然后打开 " **MenuPage.xaml.cs** " 文件。 将其内容替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > Visual Studio 将报告**MenuPage.xaml.cs**中的错误。 这些错误将在后续步骤中予以解决。

#### <a name="implement-the-welcome-page"></a>实施欢迎页面

1. 右键单击 " **GraphTutorial** " 项目，然后依次选择 "**添加**"、"**新建项目 ...**"。选择 "**内容页面**" 并命名`WelcomePage`页面。 选择“**添加**”。 打开**WelcomePage**文件，并将其内容替换为以下内容。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml":::

1. 展开 "**解决方案资源管理器**" 中的 " **WelcomePage** "，然后打开 " **WelcomePage.xaml.cs** " 文件。 将以下函数添加到 `WelcomePage` 类。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-page"></a>添加日历页

现在添加 "日历" 页。 这将是现在的占位符。

1. 右键单击 " **GraphTutorial** " 项目，然后依次选择 "**添加**"、"**新建项目 ...**"。选择 "**内容页面**" 并命名`CalendarPage`页面。 选择“**添加**”。

#### <a name="update-mainpage-code-behind"></a>更新 MainPage 代码隐藏

现在，已准备好所有页面，更新**MainPage**的代码隐藏。

1. 展开 "**解决方案资源管理器**" 中的**MainPage** ，打开**MainPage.xaml.cs**文件，并将其全部内容替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. 保存所有更改。 右键单击要运行的项目（Android、iOS 或 UWP），然后选择 "**设为启动项目**"。 按**F5**或选择 "调试" > 在 Visual Studio 中**启动调试**。

    ![应用程序的 Android、iOS 和 UWP 版本的屏幕截图](./images/welcome-page.png)
