<!-- markdownlint-disable MD002 MD041 -->

1. <span data-ttu-id="6da32-101">打开 Visual Studio ，选择“**新建一个项目**”。</span><span class="sxs-lookup"><span data-stu-id="6da32-101">Open Visual Studio, and select **Create a new project**.</span></span>

1. <span data-ttu-id="6da32-102">在创建新 **项目对话框中，** 选择移动应用 (**Xamarin.Forms) ，** 然后选择下一 **步**。</span><span class="sxs-lookup"><span data-stu-id="6da32-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then select **Next**.</span></span>

    ![Visual Studio 2019 创建新项目对话框](images/new-project-dialog.png)

1. <span data-ttu-id="6da32-104">在"**配置新项目"** 对话框中，输入"Project名称"和"解决方案名称"， `GraphTutorial` 然后选择"创建 **"。**  </span><span class="sxs-lookup"><span data-stu-id="6da32-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then select **Create**.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="6da32-105">请确保为这些实验室说明中指定的Visual Studio Project输入完全相同的名称。</span><span class="sxs-lookup"><span data-stu-id="6da32-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="6da32-106">Visual Studio 项目名称在代码中成为了命名空间的一部分。</span><span class="sxs-lookup"><span data-stu-id="6da32-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="6da32-107">在这些说明里的代码依赖于匹配在这些说明中指定的 Visual Studio 项目名称的命名空间。</span><span class="sxs-lookup"><span data-stu-id="6da32-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="6da32-108">如果你使用不同的项目名称，代码不会编译，除非你调整所有的命名空间来匹配你在创建项目时输入的 Visual Studio 项目名称。</span><span class="sxs-lookup"><span data-stu-id="6da32-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

    ![Visual Studio 2019 配置新项目对话框](images/configure-new-project-dialog.png)

1. <span data-ttu-id="6da32-110">在" **新建跨平台应用** "对话框中，选择 **"空白** "模板，然后选择想要在"平台"下构建 **的平台**。</span><span class="sxs-lookup"><span data-stu-id="6da32-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="6da32-111">选择 **"** 确定"以创建解决方案。</span><span class="sxs-lookup"><span data-stu-id="6da32-111">Select **OK** to create the solution.</span></span>

    ![Visual Studio 2019 新的跨平台应用对话框](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a><span data-ttu-id="6da32-113">安装程序包</span><span class="sxs-lookup"><span data-stu-id="6da32-113">Install packages</span></span>

<span data-ttu-id="6da32-114">在继续之前，请安装一些NuGet包，你稍后会使用它。</span><span class="sxs-lookup"><span data-stu-id="6da32-114">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="6da32-115">用于处理 Azure AD 身份验证和令牌管理的[Microsoft.Identity.Client。](https://www.nuget.org/packages/Microsoft.Identity.Client/)</span><span class="sxs-lookup"><span data-stu-id="6da32-115">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="6da32-116">[Microsoft。Graph](https://www.nuget.org/packages/Microsoft.Graph/)用于调用 Microsoft Graph。</span><span class="sxs-lookup"><span data-stu-id="6da32-116">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="6da32-117">[用于跨平台处理时区的 TimeZoneConverter。](https://www.nuget.org/packages/TimeZoneConverter/)</span><span class="sxs-lookup"><span data-stu-id="6da32-117">[TimeZoneConverter](https://www.nuget.org/packages/TimeZoneConverter/) for handling time zones cross-platform.</span></span>

1. <span data-ttu-id="6da32-118">选择“**工具 > NuGet 程序包管理器 > 程序包管理控制台**”。</span><span class="sxs-lookup"><span data-stu-id="6da32-118">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span>

1. <span data-ttu-id="6da32-119">在“程序包管理器控制台”中，输入以下命令。</span><span class="sxs-lookup"><span data-stu-id="6da32-119">In the Package Manager Console, enter the following commands.</span></span>

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.34.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.34.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.34.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 4.0.0 -Project GraphTutorial
    Install-Package TimeZoneConverter -Project GraphTutorial
    ```

## <a name="design-the-app"></a><span data-ttu-id="6da32-120">设计应用</span><span class="sxs-lookup"><span data-stu-id="6da32-120">Design the app</span></span>

<span data-ttu-id="6da32-121">首先更新 类 `App` 以添加变量以跟踪身份验证状态和登录用户。</span><span class="sxs-lookup"><span data-stu-id="6da32-121">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span>

1. <span data-ttu-id="6da32-122">在 **"解决方案资源管理器**"中，展开 **GraphTutorial** 项目，然后展开 **App.xaml** 文件。</span><span class="sxs-lookup"><span data-stu-id="6da32-122">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="6da32-123">打开 **App.xaml.cs** 文件，将以下语句 `using` 添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="6da32-123">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. <span data-ttu-id="6da32-124">将 `INotifyPropertyChanged` 接口添加到类声明。</span><span class="sxs-lookup"><span data-stu-id="6da32-124">Add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="6da32-125">将以下属性添加到 `App` 类。</span><span class="sxs-lookup"><span data-stu-id="6da32-125">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. <span data-ttu-id="6da32-126">将以下函数添加到 `App` 类。</span><span class="sxs-lookup"><span data-stu-id="6da32-126">Add the following functions to the `App` class.</span></span> <span data-ttu-id="6da32-127">目前 `SignIn` `SignOut` ， 、 和 `GetUserInfo` 函数只是占位符。</span><span class="sxs-lookup"><span data-stu-id="6da32-127">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

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

1. <span data-ttu-id="6da32-128">`GetUserPhoto`函数现在返回默认照片。</span><span class="sxs-lookup"><span data-stu-id="6da32-128">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="6da32-129">你可以提供你自己的文件，也可以从 下载示例中使用的文件[GitHub。](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)</span><span class="sxs-lookup"><span data-stu-id="6da32-129">You can either supply your own file, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="6da32-130">如果使用自己的文件，请将其重命名为 **no-profile-pic.png。**</span><span class="sxs-lookup"><span data-stu-id="6da32-130">If you use your own file, rename it to **no-profile-pic.png**.</span></span>

1. <span data-ttu-id="6da32-131">将文件复制到 **./GraphTu一l/GraphTutorial** 目录。</span><span class="sxs-lookup"><span data-stu-id="6da32-131">Copy the file to the **./GraphTutorial/GraphTutorial** directory.</span></span>

1. <span data-ttu-id="6da32-132">右键单击"解决方案资源管理器 **"中的文件，** 然后选择"属性 **"。**</span><span class="sxs-lookup"><span data-stu-id="6da32-132">Right-click the file in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="6da32-133">在"**属性"** 窗口中，将"生成 **操作"** 的值更改为 **"嵌入资源"。**</span><span class="sxs-lookup"><span data-stu-id="6da32-133">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

    ![PNG 文件的"属性"窗口屏幕截图](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="6da32-135">应用导航</span><span class="sxs-lookup"><span data-stu-id="6da32-135">App navigation</span></span>

<span data-ttu-id="6da32-136">在此部分中，你将应用程序的主页更改为 [FlyoutPage](/xamarin/xamarin-forms/app-fundamentals/navigation/flyoutpage)。</span><span class="sxs-lookup"><span data-stu-id="6da32-136">In this section, you'll change the application's main page to a [FlyoutPage](/xamarin/xamarin-forms/app-fundamentals/navigation/flyoutpage).</span></span> <span data-ttu-id="6da32-137">这将提供在应用中的视图之间切换的导航菜单。</span><span class="sxs-lookup"><span data-stu-id="6da32-137">This will provide a navigation menu to switch between view in the app.</span></span>

1. <span data-ttu-id="6da32-138">打开 **GraphTu一** l 项目中的 **MainPage.xaml** 文件，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="6da32-138">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml":::

#### <a name="implement-the-menu"></a><span data-ttu-id="6da32-139">实现菜单</span><span class="sxs-lookup"><span data-stu-id="6da32-139">Implement the menu</span></span>

1. <span data-ttu-id="6da32-140">右键单击 **GraphTutorial** 项目，然后选择 **添加**，然后选择新 **文件夹**。</span><span class="sxs-lookup"><span data-stu-id="6da32-140">Right-click the **GraphTutorial** project and select **Add**, then **New Folder**.</span></span> <span data-ttu-id="6da32-141">将文件夹命名 `Models` 。</span><span class="sxs-lookup"><span data-stu-id="6da32-141">Name the folder `Models`.</span></span>

1. <span data-ttu-id="6da32-142">右键单击 **"模型"** 文件夹，然后选择 **"添加"，** 然后选择"**类..."。** 将类命名 `NavMenuItem` ，然后选择"添加 **"。**</span><span class="sxs-lookup"><span data-stu-id="6da32-142">Right-click the **Models** folder and select **Add**, then **Class...**. Name the class `NavMenuItem` and select **Add**.</span></span>

1. <span data-ttu-id="6da32-143">打开 **NavMenuItem.cs** 文件，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="6da32-143">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. <span data-ttu-id="6da32-144">右键单击 **GraphTutorial** 项目，然后选择 **"添加**"，然后选择" **新建项目..."。** 选择 **"内容页面** "，并命名该页面 `MenuPage` 。</span><span class="sxs-lookup"><span data-stu-id="6da32-144">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="6da32-145">选择 **添加**。</span><span class="sxs-lookup"><span data-stu-id="6da32-145">Select **Add**.</span></span>

1. <span data-ttu-id="6da32-146">打开 **MenuPage.xaml** 文件，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="6da32-146">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml":::

1. <span data-ttu-id="6da32-147">在 **"解决方案资源管理器"中展开 MenuPage.xaml** 并打开 **MenuPage.xaml.cs** 文件。 </span><span class="sxs-lookup"><span data-stu-id="6da32-147">Expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="6da32-148">将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="6da32-148">Replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > <span data-ttu-id="6da32-149">Visual Studio将报告 **MenuPage.xaml.cs 中的错误**。</span><span class="sxs-lookup"><span data-stu-id="6da32-149">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="6da32-150">这些错误将在稍后的步骤中解决。</span><span class="sxs-lookup"><span data-stu-id="6da32-150">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="6da32-151">实现欢迎页面</span><span class="sxs-lookup"><span data-stu-id="6da32-151">Implement the welcome page</span></span>

1. <span data-ttu-id="6da32-152">右键单击 **GraphTutorial** 项目，然后选择 **"添加**"，然后选择" **新建项目..."。** 选择 **"内容页面** "，并命名该页面 `WelcomePage` 。</span><span class="sxs-lookup"><span data-stu-id="6da32-152">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="6da32-153">选择 **添加**。</span><span class="sxs-lookup"><span data-stu-id="6da32-153">Select **Add**.</span></span> <span data-ttu-id="6da32-154">打开 **WelcomePage.xaml** 文件，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="6da32-154">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml":::

1. <span data-ttu-id="6da32-155">在 **"解决方案资源管理器"中展开 WelcomePage.xaml** 并打开 **WelcomePage.xaml.cs** 文件。 </span><span class="sxs-lookup"><span data-stu-id="6da32-155">Expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="6da32-156">将以下函数添加到 `WelcomePage` 类。</span><span class="sxs-lookup"><span data-stu-id="6da32-156">Add the following function to the `WelcomePage` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-and-new-event-pages"></a><span data-ttu-id="6da32-157">添加日历和新事件页面</span><span class="sxs-lookup"><span data-stu-id="6da32-157">Add calendar and new event pages</span></span>

<span data-ttu-id="6da32-158">现在，添加一个日历页和一个新的事件页。</span><span class="sxs-lookup"><span data-stu-id="6da32-158">Now add a calendar page and a new event page.</span></span> <span data-ttu-id="6da32-159">现在这些只是占位符。</span><span class="sxs-lookup"><span data-stu-id="6da32-159">These will just be placeholders for now.</span></span>

1. <span data-ttu-id="6da32-160">右键单击 **GraphTutorial** 项目，然后选择 **"添加**"，然后选择" **新建项目..."。** 选择 **"内容页面** "，并命名该页面 `CalendarPage` 。</span><span class="sxs-lookup"><span data-stu-id="6da32-160">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="6da32-161">选择 **添加**。</span><span class="sxs-lookup"><span data-stu-id="6da32-161">Select **Add**.</span></span>

1. <span data-ttu-id="6da32-162">右键单击 **GraphTutorial** 项目，然后选择 **"添加**"，然后选择" **新建项目..."。** 选择 **"内容页面** "，并命名该页面 `NewEventPage` 。</span><span class="sxs-lookup"><span data-stu-id="6da32-162">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `NewEventPage`.</span></span> <span data-ttu-id="6da32-163">选择 **添加**。</span><span class="sxs-lookup"><span data-stu-id="6da32-163">Select **Add**.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="6da32-164">更新 MainPage 代码隐藏</span><span class="sxs-lookup"><span data-stu-id="6da32-164">Update MainPage code-behind</span></span>

<span data-ttu-id="6da32-165">现在，所有页面已就位，请更新 **MainPage.xaml** 的代码隐藏。</span><span class="sxs-lookup"><span data-stu-id="6da32-165">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span>

1. <span data-ttu-id="6da32-166">在 **"解决方案资源管理器"中展开 MainPage.xaml，** 打开 **MainPage.xaml.cs** 文件，并将其全部内容替换为以下内容。 </span><span class="sxs-lookup"><span data-stu-id="6da32-166">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. <span data-ttu-id="6da32-167">保存所有更改。</span><span class="sxs-lookup"><span data-stu-id="6da32-167">Save all of your changes.</span></span> <span data-ttu-id="6da32-168">右键单击你想要在 Android、iOS 或 UWP (运行的项目，然后选择) 设置为"启动 **"Project。**</span><span class="sxs-lookup"><span data-stu-id="6da32-168">Right-click the project that you want to run (Android, iOS, or UWP) and select **Set as StartUp Project**.</span></span> <span data-ttu-id="6da32-169">按 **F5** 或选择"调试 **>"开始** 调试"Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="6da32-169">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

    ![应用程序的 Android、iOS 和 UWP 版本的屏幕截图](./images/welcome-page.png)
