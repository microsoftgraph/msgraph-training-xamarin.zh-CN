<!-- markdownlint-disable MD002 MD041 -->

1. <span data-ttu-id="5a885-101">打开Visual Studio，然后选择 **"新建项目"。**</span><span class="sxs-lookup"><span data-stu-id="5a885-101">Open Visual Studio, and select **Create a new project**.</span></span>

1. <span data-ttu-id="5a885-102">在"**新建项目"对话框中**，选择"移动应用" (**Xamarin.Forms) ，然后选择"下一步\*\*\*\*"。**</span><span class="sxs-lookup"><span data-stu-id="5a885-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then select **Next**.</span></span>

    ![Visual Studio 2019 新建项目对话框](images/new-project-dialog.png)

1. <span data-ttu-id="5a885-104">在 **"配置新项目"对话框中**，输入 `GraphTutorial` 项目名称和 **解决方案名称**，然后选择"**创建"。** </span><span class="sxs-lookup"><span data-stu-id="5a885-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then select **Create**.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="5a885-105">确保为这些实验室说明中指定的 Visual Studio 项目输入完全相同的名称。</span><span class="sxs-lookup"><span data-stu-id="5a885-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="5a885-106">项目Visual Studio名称将成为代码中命名空间的一部分。</span><span class="sxs-lookup"><span data-stu-id="5a885-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="5a885-107">这些说明中的代码取决于匹配这些Visual Studio中指定的项目名称的命名空间。</span><span class="sxs-lookup"><span data-stu-id="5a885-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="5a885-108">如果使用其他项目名称，则代码将不会编译，除非您调整所有命名空间以匹配Visual Studio项目名称时输入的项目名称。</span><span class="sxs-lookup"><span data-stu-id="5a885-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

    ![Visual Studio 2019 配置新项目对话框](images/configure-new-project-dialog.png)

1. <span data-ttu-id="5a885-110">在 **"新建跨平台应用** "对话框中，选择 **空白** 模板，然后选择想要在平台下构建 **的平台**。</span><span class="sxs-lookup"><span data-stu-id="5a885-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="5a885-111">选择 **"** 确定"以创建解决方案。</span><span class="sxs-lookup"><span data-stu-id="5a885-111">Select **OK** to create the solution.</span></span>

    ![Visual Studio 2019 新的跨平台应用对话框](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a><span data-ttu-id="5a885-113">安装程序包</span><span class="sxs-lookup"><span data-stu-id="5a885-113">Install packages</span></span>

<span data-ttu-id="5a885-114">在继续之前，请安装一些稍后将使用的其他 NuGet 程序包。</span><span class="sxs-lookup"><span data-stu-id="5a885-114">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="5a885-115">用于处理 Azure AD 身份验证和令牌管理的[Microsoft.Identity.Client。](https://www.nuget.org/packages/Microsoft.Identity.Client/)</span><span class="sxs-lookup"><span data-stu-id="5a885-115">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="5a885-116">[用于调用 Microsoft Graph](https://www.nuget.org/packages/Microsoft.Graph/) 的 Microsoft.Graph。</span><span class="sxs-lookup"><span data-stu-id="5a885-116">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="5a885-117">[用于跨平台处理时区的 TimeZoneConverter。](https://www.nuget.org/packages/TimeZoneConverter/)</span><span class="sxs-lookup"><span data-stu-id="5a885-117">[TimeZoneConverter](https://www.nuget.org/packages/TimeZoneConverter/) for handling time zones cross-platform.</span></span>

1. <span data-ttu-id="5a885-118">Select **Tools > NuGet 程序包管理器 > 程序包管理器 Console.**</span><span class="sxs-lookup"><span data-stu-id="5a885-118">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span>

1. <span data-ttu-id="5a885-119">在程序包管理器控制台中，输入以下命令。</span><span class="sxs-lookup"><span data-stu-id="5a885-119">In the Package Manager Console, enter the following commands.</span></span>

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.21.0 -Project GraphTutorial
    Install-Package TimeZoneConverter -Version 3.3.0 -Project GraphTutorial
    ```

## <a name="design-the-app"></a><span data-ttu-id="5a885-120">设计应用</span><span class="sxs-lookup"><span data-stu-id="5a885-120">Design the app</span></span>

<span data-ttu-id="5a885-121">首先更新类 `App` 以添加变量以跟踪身份验证状态和登录用户。</span><span class="sxs-lookup"><span data-stu-id="5a885-121">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span>

1. <span data-ttu-id="5a885-122">在 **解决方案资源管理器** 中，展开 **GraphTu一l** 项目，然后展开 **App.xaml** 文件。</span><span class="sxs-lookup"><span data-stu-id="5a885-122">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="5a885-123">打开 **App.xaml.cs** 文件，将以下 `using` 语句添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="5a885-123">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. <span data-ttu-id="5a885-124">将 `INotifyPropertyChanged` 接口添加到类声明。</span><span class="sxs-lookup"><span data-stu-id="5a885-124">Add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="5a885-125">将以下属性添加到 `App` 类。</span><span class="sxs-lookup"><span data-stu-id="5a885-125">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. <span data-ttu-id="5a885-126">将以下函数添加到 `App` 类。</span><span class="sxs-lookup"><span data-stu-id="5a885-126">Add the following functions to the `App` class.</span></span> <span data-ttu-id="5a885-127">目前 `SignIn` `SignOut` ， 和 `GetUserInfo` 函数只是占位符。</span><span class="sxs-lookup"><span data-stu-id="5a885-127">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

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

1. <span data-ttu-id="5a885-128">该 `GetUserPhoto` 函数现在返回默认照片。</span><span class="sxs-lookup"><span data-stu-id="5a885-128">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="5a885-129">你可以提供你自己的文件，也可以从 [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)下载示例中使用的文件。</span><span class="sxs-lookup"><span data-stu-id="5a885-129">You can either supply your own file, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="5a885-130">如果使用自己的文件，请将其重命名 **为no-profile-pic.png。**</span><span class="sxs-lookup"><span data-stu-id="5a885-130">If you use your own file, rename it to **no-profile-pic.png**.</span></span>

1. <span data-ttu-id="5a885-131">将文件复制到 **./GraphTu一l/GraphTu一l** 目录。</span><span class="sxs-lookup"><span data-stu-id="5a885-131">Copy the file to the **./GraphTutorial/GraphTutorial** directory.</span></span>

1. <span data-ttu-id="5a885-132">右键单击解决方案资源管理器中的 **文件，** 然后选择"**属性"。**</span><span class="sxs-lookup"><span data-stu-id="5a885-132">Right-click the file in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="5a885-133">在"**属性**"窗口中，将"生成 **操作"的值更改为\*\*\*\*"嵌入"资源**。</span><span class="sxs-lookup"><span data-stu-id="5a885-133">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

    ![PNG 文件的属性窗口屏幕截图](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="5a885-135">应用导航</span><span class="sxs-lookup"><span data-stu-id="5a885-135">App navigation</span></span>

<span data-ttu-id="5a885-136">在此部分中，将应用程序的主页更改为"母版 [-细节"页](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page)。</span><span class="sxs-lookup"><span data-stu-id="5a885-136">In this section, you'll change the application's main page to a [Master-Detail page](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span></span> <span data-ttu-id="5a885-137">这将提供在应用中的视图之间切换的导航菜单。</span><span class="sxs-lookup"><span data-stu-id="5a885-137">This will provide a navigation menu to switch between view in the app.</span></span>

1. <span data-ttu-id="5a885-138">打开 **GraphTu一** l 项目中的 **MainPage.xaml** 文件，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="5a885-138">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml" id="MainPageXamlSnippet":::

#### <a name="implement-the-menu"></a><span data-ttu-id="5a885-139">实现菜单</span><span class="sxs-lookup"><span data-stu-id="5a885-139">Implement the menu</span></span>

1. <span data-ttu-id="5a885-140">右键单击 **GraphTu一l** 项目，然后选择 **"添加**"，然后选择"**新建文件夹"。**</span><span class="sxs-lookup"><span data-stu-id="5a885-140">Right-click the **GraphTutorial** project and select **Add**, then **New Folder**.</span></span> <span data-ttu-id="5a885-141">命名文件夹 `Models` 。</span><span class="sxs-lookup"><span data-stu-id="5a885-141">Name the folder `Models`.</span></span>

1. <span data-ttu-id="5a885-142">右键单击 **Models** 文件夹，然后选择 **"添加**"，然后选择"**类..."。** 命名类 `NavMenuItem` ，然后选择"**添加"。**</span><span class="sxs-lookup"><span data-stu-id="5a885-142">Right-click the **Models** folder and select **Add**, then **Class...**. Name the class `NavMenuItem` and select **Add**.</span></span>

1. <span data-ttu-id="5a885-143">打开 **NavMenuItem.cs** 文件，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="5a885-143">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. <span data-ttu-id="5a885-144">右键单击 **GraphTu一l** 项目，然后选择 **"添加**"，然后选择 **"新项目..."。** 选择 **内容页** 并命名该页面 `MenuPage` 。</span><span class="sxs-lookup"><span data-stu-id="5a885-144">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="5a885-145">选择“**添加**”。</span><span class="sxs-lookup"><span data-stu-id="5a885-145">Select **Add**.</span></span>

1. <span data-ttu-id="5a885-146">打开 **MenuPage.xaml** 文件，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="5a885-146">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml" id="MenuPageXamlSnippet":::

1. <span data-ttu-id="5a885-147">在 **解决方案资源管理器中展开 MenuPage.xaml** 并 **打开MenuPage.xaml.cs文件**。 </span><span class="sxs-lookup"><span data-stu-id="5a885-147">Expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="5a885-148">将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="5a885-148">Replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > <span data-ttu-id="5a885-149">Visual Studio中 **报告MenuPage.xaml.cs。**</span><span class="sxs-lookup"><span data-stu-id="5a885-149">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="5a885-150">这些错误将在稍后步骤中解决。</span><span class="sxs-lookup"><span data-stu-id="5a885-150">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="5a885-151">实现欢迎页面</span><span class="sxs-lookup"><span data-stu-id="5a885-151">Implement the welcome page</span></span>

1. <span data-ttu-id="5a885-152">右键单击 **GraphTu一l** 项目，然后选择 **"添加**"，然后选择 **"新项目..."。** 选择 **内容页** 并命名该页面 `WelcomePage` 。</span><span class="sxs-lookup"><span data-stu-id="5a885-152">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="5a885-153">选择“**添加**”。</span><span class="sxs-lookup"><span data-stu-id="5a885-153">Select **Add**.</span></span> <span data-ttu-id="5a885-154">打开 **WelcomePage.xaml** 文件，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="5a885-154">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml" id="WelcomePageXamlSnippet":::

1. <span data-ttu-id="5a885-155">在 **解决方案资源管理器中展开 WelcomePage.xaml** 并 **打开WelcomePage.xaml.cs文件**。 </span><span class="sxs-lookup"><span data-stu-id="5a885-155">Expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="5a885-156">将以下函数添加到 `WelcomePage` 类。</span><span class="sxs-lookup"><span data-stu-id="5a885-156">Add the following function to the `WelcomePage` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-and-new-event-pages"></a><span data-ttu-id="5a885-157">添加日历和新事件页面</span><span class="sxs-lookup"><span data-stu-id="5a885-157">Add calendar and new event pages</span></span>

<span data-ttu-id="5a885-158">现在，添加日历页和新的事件页面。</span><span class="sxs-lookup"><span data-stu-id="5a885-158">Now add a calendar page and a new event page.</span></span> <span data-ttu-id="5a885-159">现在这些只是占位符。</span><span class="sxs-lookup"><span data-stu-id="5a885-159">These will just be placeholders for now.</span></span>

1. <span data-ttu-id="5a885-160">右键单击 **GraphTu一l** 项目，然后选择 **"添加**"，然后选择 **"新项目..."。** 选择 **内容页** 并命名该页面 `CalendarPage` 。</span><span class="sxs-lookup"><span data-stu-id="5a885-160">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="5a885-161">选择“**添加**”。</span><span class="sxs-lookup"><span data-stu-id="5a885-161">Select **Add**.</span></span>

1. <span data-ttu-id="5a885-162">右键单击 **GraphTu一l** 项目，然后选择 **"添加**"，然后选择 **"新项目..."。** 选择 **内容页** 并命名该页面 `NewEventPage` 。</span><span class="sxs-lookup"><span data-stu-id="5a885-162">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `NewEventPage`.</span></span> <span data-ttu-id="5a885-163">选择“**添加**”。</span><span class="sxs-lookup"><span data-stu-id="5a885-163">Select **Add**.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="5a885-164">更新 MainPage 代码隐藏</span><span class="sxs-lookup"><span data-stu-id="5a885-164">Update MainPage code-behind</span></span>

<span data-ttu-id="5a885-165">现在，所有页面已就位，请更新 **MainPage.xaml 的代码隐藏。**</span><span class="sxs-lookup"><span data-stu-id="5a885-165">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span>

1. <span data-ttu-id="5a885-166">在 **解决方案资源管理器中展开 MainPage.xaml** 并打开 MainPage.xaml.cs **文件，** 并将其全部内容替换为以下内容。 </span><span class="sxs-lookup"><span data-stu-id="5a885-166">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. <span data-ttu-id="5a885-167">保存所有更改。</span><span class="sxs-lookup"><span data-stu-id="5a885-167">Save all of your changes.</span></span> <span data-ttu-id="5a885-168">右键单击你想要在 Android、iOS 或 UWP (运行的项目，) "设置为 **启动项目"。**</span><span class="sxs-lookup"><span data-stu-id="5a885-168">Right-click the project that you want to run (Android, iOS, or UWP) and select **Set as StartUp Project**.</span></span> <span data-ttu-id="5a885-169">按 **F5** 或选择>**开始调试Visual Studio。**</span><span class="sxs-lookup"><span data-stu-id="5a885-169">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

    ![应用程序的 Android、iOS 和 UWP 版本的屏幕截图](./images/welcome-page.png)
