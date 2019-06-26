<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="4c521-101">打开 Visual Studio, 然后选择 "**新建项目**"。</span><span class="sxs-lookup"><span data-stu-id="4c521-101">Open Visual Studio, and select **Create a new project**.</span></span> <span data-ttu-id="4c521-102">在 "**创建新项目**" 对话框中, 选择 "**移动应用 (Xamarin. Forms)**", 然后选择 "**下一步**"。</span><span class="sxs-lookup"><span data-stu-id="4c521-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then select **Next**.</span></span>

![Visual Studio 2019 "新建项目" 对话框](images/new-project-dialog.png)

<span data-ttu-id="4c521-104">在 "**配置新项目**" 对话框中, `GraphTutorial`输入**项目名称**和**解决方案名称**, 然后选择 "**创建**"。</span><span class="sxs-lookup"><span data-stu-id="4c521-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then select **Create**.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4c521-105">确保为在这些实验室说明中指定的 Visual Studio 项目输入完全相同的名称。</span><span class="sxs-lookup"><span data-stu-id="4c521-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="4c521-106">Visual Studio 项目名称将成为代码中的命名空间的一部分。</span><span class="sxs-lookup"><span data-stu-id="4c521-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="4c521-107">这些指令中的代码取决于与这些说明中指定的 Visual Studio 项目名称匹配的命名空间。</span><span class="sxs-lookup"><span data-stu-id="4c521-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="4c521-108">如果使用其他项目名称, 则代码将不会编译, 除非您调整所有命名空间以匹配您在创建项目时输入的 Visual Studio 项目名称。</span><span class="sxs-lookup"><span data-stu-id="4c521-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

![Visual Studio 2019 配置新项目对话框](images/configure-new-project-dialog.png)

<span data-ttu-id="4c521-110">在 "**新建跨平台应用程序**" 对话框中, 选择 "**空白**" 模板, 然后选择要在 "**平台**" 下构建的平台。</span><span class="sxs-lookup"><span data-stu-id="4c521-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="4c521-111">选择 **"确定"** 以创建解决方案。</span><span class="sxs-lookup"><span data-stu-id="4c521-111">Select **OK** to create the solution.</span></span>

![Visual Studio 2019 新建跨平台应用程序对话框](images/new-cross-platform-app-dialog.png)

<span data-ttu-id="4c521-113">在继续之前, 请安装稍后将使用的一些其他 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="4c521-113">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="4c521-114">用于处理 Azure AD 身份验证和令牌管理的[Microsoft Identity。](https://www.nuget.org/packages/Microsoft.Identity.Client/)</span><span class="sxs-lookup"><span data-stu-id="4c521-114">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="4c521-115">用于调用 Microsoft Graph 的[microsoft](https://www.nuget.org/packages/Microsoft.Graph/) graph。</span><span class="sxs-lookup"><span data-stu-id="4c521-115">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>

<span data-ttu-id="4c521-116">选择 "**工具" > NuGet 包管理器 "> 程序包管理器控制台**"。</span><span class="sxs-lookup"><span data-stu-id="4c521-116">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span> <span data-ttu-id="4c521-117">在 "程序包管理器控制台" 中, 输入以下命令。</span><span class="sxs-lookup"><span data-stu-id="4c521-117">In the Package Manager Console, enter the following commands.</span></span>

```Powershell
Install-Package Microsoft.Identity.Client -Version 3.0.8 -Project GraphTutorial
Install-Package Microsoft.Identity.Client -Version 3.0.8 -Project GraphTutorial.Android
Install-Package Microsoft.Identity.Client -Version 3.0.8 -Project GraphTutorial.iOS
Install-Package Microsoft.Graph -Version 1.15.0 -Project GraphTutorial
```

## <a name="design-the-app"></a><span data-ttu-id="4c521-118">设计应用程序</span><span class="sxs-lookup"><span data-stu-id="4c521-118">Design the app</span></span>

<span data-ttu-id="4c521-119">首先更新`App`类以添加用于跟踪身份验证状态和已登录用户的变量。</span><span class="sxs-lookup"><span data-stu-id="4c521-119">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span> <span data-ttu-id="4c521-120">在 "**解决方案资源管理器**" 中, 展开 " **GraphTutorial** " 项目, 然后展开**app.xaml**文件。</span><span class="sxs-lookup"><span data-stu-id="4c521-120">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="4c521-121">打开**App.xaml.cs**文件, 并将以下`using`语句添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="4c521-121">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

```cs
using System.ComponentModel;
using System.IO;
using System.Reflection;
using System.Threading.Tasks;
```

<span data-ttu-id="4c521-122">接下来, 将`INotifyPropertyChanged`接口添加到类声明中。</span><span class="sxs-lookup"><span data-stu-id="4c521-122">Next, add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

```cs
public partial class App : Application, INotifyPropertyChanged
```

<span data-ttu-id="4c521-123">现在, 将以下属性添加到`App`类中。</span><span class="sxs-lookup"><span data-stu-id="4c521-123">Now add the following properties to the `App` class.</span></span>

```cs
// Is a user signed in?
private bool isSignedIn;
public bool IsSignedIn
{
    get { return isSignedIn; }
    set
    {
        isSignedIn = value;
        OnPropertyChanged("IsSignedIn");
        OnPropertyChanged("IsSignedOut");
    }
}

public bool IsSignedOut { get { return !isSignedIn; } }

// The user's display name
private string userName;
public string UserName
{
    get { return userName; }
    set
    {
        userName = value;
        OnPropertyChanged("UserName");
    }
}

// The user's email address
private string userEmail;
public string UserEmail
{
    get { return userEmail; }
    set
    {
        userEmail = value;
        OnPropertyChanged("UserEmail");
    }
}

// The user's profile photo
private ImageSource userPhoto;
public ImageSource UserPhoto
{
    get { return userPhoto; }
    set
    {
        userPhoto = value;
        OnPropertyChanged("UserPhoto");
    }
}
```

<span data-ttu-id="4c521-124">现在, 将以下函数添加到`App`类中。</span><span class="sxs-lookup"><span data-stu-id="4c521-124">Now add the following functions to the `App` class.</span></span> <span data-ttu-id="4c521-125">`SignIn`、 `SignOut`和`GetUserInfo`函数现在只是占位符。</span><span class="sxs-lookup"><span data-stu-id="4c521-125">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

```cs
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

<span data-ttu-id="4c521-126">`GetUserPhoto`函数现在返回默认照片。</span><span class="sxs-lookup"><span data-stu-id="4c521-126">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="4c521-127">你可以在此处提供自己的文件, 也可以从[GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)中的示例中下载所使用的文件。</span><span class="sxs-lookup"><span data-stu-id="4c521-127">You can either supply your own file here, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="4c521-128">将文件复制到`./GraphTutorial/GraphTutorial`目录。</span><span class="sxs-lookup"><span data-stu-id="4c521-128">Copy the file to the `./GraphTutorial/GraphTutorial` directory.</span></span> <span data-ttu-id="4c521-129">在 "**解决方案资源管理器**" 中右键单击 " **GraphTutorial** " 项目, 然后依次选择 "**添加**"、"**现有项目 ...**"。选择文件`no-profile-pic.png` , 然后选择 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="4c521-129">Right-click the **GraphTutorial** project in **Solution Explorer** and select **Add**, then **Existing Item...**. Select the `no-profile-pic.png` file and select **Add**.</span></span> <span data-ttu-id="4c521-130">现在, 在 "**解决方案资源管理器**" 中右键单击该文件, 然后选择 "**属性**"。</span><span class="sxs-lookup"><span data-stu-id="4c521-130">Now right-click the file in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="4c521-131">在 "**属性**" 窗口中, 将 "**生成操作**" 的值更改为 "**嵌入的资源**"。</span><span class="sxs-lookup"><span data-stu-id="4c521-131">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

![PNG 文件的 "属性" 窗口的屏幕截图](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="4c521-133">应用程序导航</span><span class="sxs-lookup"><span data-stu-id="4c521-133">App navigation</span></span>

<span data-ttu-id="4c521-134">接下来, 将应用程序的主页面更改[为主-详细信息页面](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page)。</span><span class="sxs-lookup"><span data-stu-id="4c521-134">Next, change the application's main page to a [Master-Detail page](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span></span> <span data-ttu-id="4c521-135">这将提供导航菜单, 以便在应用程序中的视图之间切换。</span><span class="sxs-lookup"><span data-stu-id="4c521-135">This will provide a navigation menu to switch between view in the app.</span></span>

<span data-ttu-id="4c521-136">打开**GraphTutorial**项目中的**MainPage**文件, 并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="4c521-136">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:GraphTutorial"
             x:Class="GraphTutorial.MainPage">

    <MasterDetailPage.Master>
        <local:MenuPage/>
    </MasterDetailPage.Master>

    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:WelcomePage/>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>

</MasterDetailPage>
```

#### <a name="implement-the-menu"></a><span data-ttu-id="4c521-137">实现菜单</span><span class="sxs-lookup"><span data-stu-id="4c521-137">Implement the menu</span></span>

<span data-ttu-id="4c521-138">首先, 创建一个用于表示菜单项的模型。</span><span class="sxs-lookup"><span data-stu-id="4c521-138">Start by creating a model to represent the menu items.</span></span> <span data-ttu-id="4c521-139">右键单击 " **GraphTutorial** " 项目, 然后依次选择 "**添加**"、"**新建文件夹**"。</span><span class="sxs-lookup"><span data-stu-id="4c521-139">Right-click the **GraphTutorial** project and select **Add**, then **New Folder**.</span></span> <span data-ttu-id="4c521-140">将文件夹`Models`命名为。</span><span class="sxs-lookup"><span data-stu-id="4c521-140">Name the folder `Models`.</span></span>

<span data-ttu-id="4c521-141">右键单击 "**模型**" 文件夹, 然后依次选择 "**添加**"、"**类 ...**"。命名该类`NavMenuItem`并选择 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="4c521-141">Right-click the **Models** folder and select **Add**, then **Class...**. Name the class `NavMenuItem` and select **Add**.</span></span> <span data-ttu-id="4c521-142">打开 " **NavMenuItem.cs** " 文件, 并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="4c521-142">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

```cs
namespace GraphTutorial.Models
{
    public enum MenuItemType
    {
        Welcome,
        Calendar
    }

    public class NavMenuItem
    {
        public MenuItemType Id { get; set; }

        public string Title { get; set; }
    }
}
```

<span data-ttu-id="4c521-143">现在, 添加菜单页。</span><span class="sxs-lookup"><span data-stu-id="4c521-143">Now add the menu page.</span></span> <span data-ttu-id="4c521-144">右键单击 " **GraphTutorial** " 项目, 然后依次选择 "**添加**"、"**新建项目 ...**"。选择 "**内容页面**" 并命名`MenuPage`页面。</span><span class="sxs-lookup"><span data-stu-id="4c521-144">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="4c521-145">选择“添加”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="4c521-145">Select **Add**.</span></span> <span data-ttu-id="4c521-146">打开**MenuPage**文件, 并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="4c521-146">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.UseSafeArea="true"
             Title="Menu"
             x:Class="GraphTutorial.MenuPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="UWP" Value="10, 10, 10, 10" />
        </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout VerticalOptions="Start" HorizontalOptions="Center">
            <StackLayout x:Name="UserArea" />

            <!-- Signed out UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedOut, Source={x:Static Application.Current}}">
                <Label Text="Sign in to get started"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Medium"
                       Margin="10,20,10,20" />
                <Button Text="Sign in"
                        Clicked="OnSignIn"
                        HorizontalOptions="Center" />
            </StackLayout>

            <!-- Signed in UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedIn, Source={x:Static Application.Current}}">
                <Image Source="{Binding Path=UserPhoto, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       Margin="0,20,0,10" />
                <Label Text="{Binding Path=UserName, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Small" />
                <Label Text="{Binding Path=UserEmail, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       FontAttributes="Italic" />
                <Button Text="Sign out"
                        Margin="0,20,0,20"
                        Clicked="OnSignOut"
                        HorizontalOptions="Center" />
                <ListView x:Name="ListViewMenu"
                          HasUnevenRows="True"
                          HorizontalOptions="Start">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <ViewCell>
                                <Grid Padding="10">
                                    <Label Text="{Binding Title}" FontSize="20"/>
                                </Grid>
                            </ViewCell>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

<span data-ttu-id="4c521-147">现在, 展开 "**解决方案资源管理器**" 中的 " **MenuPage** ", 然后打开 " **MenuPage.xaml.cs** " 文件。</span><span class="sxs-lookup"><span data-stu-id="4c521-147">Now, expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="4c521-148">将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="4c521-148">Replace its contents with the following.</span></span>

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

using GraphTutorial.Models;

namespace GraphTutorial
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MenuPage : ContentPage
    {
        MainPage RootPage => Application.Current.MainPage as MainPage;
        List<NavMenuItem> menuItems;

        public MenuPage ()
        {
            InitializeComponent ();

            // Add items to the menu
            menuItems = new List<NavMenuItem>
            {
                new NavMenuItem {Id = MenuItemType.Welcome, Title="Home" },
                new NavMenuItem {Id = MenuItemType.Calendar, Title="Calendar" }
            };
            ListViewMenu.ItemsSource = menuItems;

            // Initialize the selected item
            ListViewMenu.SelectedItem = menuItems[0];

            // Handle the ItemSelected event to navigate to the
            // selected page
            ListViewMenu.ItemSelected += async (sender, e) =>
            {
                if (e.SelectedItem == null)
                    return;

                var id = (int)((NavMenuItem)e.SelectedItem).Id;
                await RootPage.NavigateFromMenu(id);
            };
        }

        private async void OnSignOut(object sender, EventArgs e)
        {
            var signout = await DisplayAlert("Sign out?", "Do you want to sign out?", "Yes", "No");
            if (signout)
            {
                await (Application.Current as App).SignOut();
            }
        }

        private async void OnSignIn(object sender, EventArgs e)
        {
            await (Application.Current as App).SignIn();
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="4c521-149">Visual Studio 将报告**MenuPage.xaml.cs**中的错误。</span><span class="sxs-lookup"><span data-stu-id="4c521-149">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="4c521-150">这些错误将在后续步骤中予以解决。</span><span class="sxs-lookup"><span data-stu-id="4c521-150">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="4c521-151">实施欢迎页面</span><span class="sxs-lookup"><span data-stu-id="4c521-151">Implement the welcome page</span></span>

<span data-ttu-id="4c521-152">右键单击 " **GraphTutorial** " 项目, 然后依次选择 "**添加**"、"**新建项目 ...**"。选择 "**内容页面**" 并命名`WelcomePage`页面。</span><span class="sxs-lookup"><span data-stu-id="4c521-152">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="4c521-153">选择“添加”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="4c521-153">Select **Add**.</span></span> <span data-ttu-id="4c521-154">打开**WelcomePage**文件, 并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="4c521-154">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             Title="Home"
             x:Class="GraphTutorial.WelcomePage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="UWP" Value="10, 10, 10, 10" />
        </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Graph Xamarin Tutorial App"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Large"
                       Margin="10,20,10,20" />

            <!-- Signed out UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedOut, Source={x:Static Application.Current}}">
                <Label Text="Please sign in to get started"
                       HorizontalOptions="Center"
                       FontSize="Medium"
                       Margin="10,0,10,20"/>
                <Button Text="Sign in"
                        HorizontalOptions="Center"
                        Clicked="OnSignIn" />
            </StackLayout>

            <!-- Signed in UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedIn, Source={x:Static Application.Current}}">
                <Label Text="{Binding Path=UserName, Source={x:Static Application.Current}, StringFormat='Welcome \{0\}!'}"
                       HorizontalOptions="Center"
                       FontSize="Medium"/>
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

<span data-ttu-id="4c521-155">现在, 展开 "**解决方案资源管理器**" 中的 " **WelcomePage** ", 然后打开 " **WelcomePage.xaml.cs** " 文件。</span><span class="sxs-lookup"><span data-stu-id="4c521-155">Now, expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="4c521-156">将以下函数添加到 `WelcomePage` 类。</span><span class="sxs-lookup"><span data-stu-id="4c521-156">Add the following function to the `WelcomePage` class.</span></span>

```cs
private void OnSignIn(object sender, EventArgs e)
{
    (App.Current.MainPage as MasterDetailPage).IsPresented = true;
}
```

#### <a name="add-calendar-page"></a><span data-ttu-id="4c521-157">添加日历页</span><span class="sxs-lookup"><span data-stu-id="4c521-157">Add calendar page</span></span>

<span data-ttu-id="4c521-158">现在添加 "日历" 页。</span><span class="sxs-lookup"><span data-stu-id="4c521-158">Now add a calendar page.</span></span> <span data-ttu-id="4c521-159">这将是现在的占位符。</span><span class="sxs-lookup"><span data-stu-id="4c521-159">This will just be a placeholder for now.</span></span> <span data-ttu-id="4c521-160">右键单击 " **GraphTutorial** " 项目, 然后依次选择 "**添加**"、"**新建项目 ...**"。选择 "**内容页面**" 并命名`CalendarPage`页面。</span><span class="sxs-lookup"><span data-stu-id="4c521-160">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="4c521-161">选择“添加”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="4c521-161">Select **Add**.</span></span>

<span data-ttu-id="4c521-162">现在, 将添加的页面保持为-为。</span><span class="sxs-lookup"><span data-stu-id="4c521-162">Leave the added page as-is for now.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="4c521-163">更新 MainPage 代码隐藏</span><span class="sxs-lookup"><span data-stu-id="4c521-163">Update MainPage code-behind</span></span>

<span data-ttu-id="4c521-164">现在, 已准备好所有页面, 更新**MainPage**的代码隐藏。</span><span class="sxs-lookup"><span data-stu-id="4c521-164">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span> <span data-ttu-id="4c521-165">展开 "**解决方案资源管理器**" 中的**MainPage** , 打开**MainPage.xaml.cs**文件, 并将其全部内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="4c521-165">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

```cs
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Xamarin.Forms;
using GraphTutorial.Models;

namespace GraphTutorial
{
    public partial class MainPage : MasterDetailPage
    {
        Dictionary<int, NavigationPage> MenuPages = new Dictionary<int, NavigationPage>();

        public MainPage()
        {
            InitializeComponent();

            MasterBehavior = MasterBehavior.Popover;

            // Load the welcome page at start
            MenuPages.Add((int)MenuItemType.Welcome, (NavigationPage)Detail);
        }

        // Navigate to the selected page
        public async Task NavigateFromMenu(int id)
        {
            if (!MenuPages.ContainsKey(id))
            {
                switch (id)
                {
                    case (int)MenuItemType.Welcome:
                        MenuPages.Add(id, new NavigationPage(new WelcomePage()));
                        break;
                    case (int)MenuItemType.Calendar:
                        MenuPages.Add(id, new NavigationPage(new CalendarPage()));
                        break;
                }
            }

            var newPage = MenuPages[id];

            if (newPage != null && Detail != newPage)
            {
                Detail = newPage;

                if (Device.RuntimePlatform == Device.Android)
                    await Task.Delay(100);

                IsPresented = false;
            }
        }
    }
}
```

<span data-ttu-id="4c521-166">保存所有更改。</span><span class="sxs-lookup"><span data-stu-id="4c521-166">Save all of your changes.</span></span> <span data-ttu-id="4c521-167">右键单击要运行的项目 (Android、iOS 或 UWP), 然后选择 "**设为启动项目**"。</span><span class="sxs-lookup"><span data-stu-id="4c521-167">Right-click the project that you want to run (Android, iOS, or UWP) and select **Set as StartUp Project**.</span></span> <span data-ttu-id="4c521-168">按**F5**或选择 "调试" > 在 Visual Studio 中**启动调试**。</span><span class="sxs-lookup"><span data-stu-id="4c521-168">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

![应用程序的 Android、iOS 和 UWP 版本的屏幕截图](./images/welcome-page.png)
