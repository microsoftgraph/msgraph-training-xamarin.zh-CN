<!-- markdownlint-disable MD002 MD041 -->

打开 Visual Studio, 然后选择 " **File > New > Project**"。 在 "**新建项目**" 对话框中, 执行以下操作:

1. 选择 " **Visual c # > 跨平台**"。
1. 选择**移动应用 (Xamarin. Forms)**。
1. 输入项目名称的**GraphTutorial** 。

![Visual Studio 2017 "新建项目" 对话框](images/new-project-dialog.png)

> [!IMPORTANT]
> 确保为在这些实验室说明中指定的 Visual Studio 项目输入完全相同的名称。 Visual Studio 项目名称将成为代码中的命名空间的一部分。 这些指令中的代码取决于与这些说明中指定的 Visual Studio 项目名称匹配的命名空间。 如果使用其他项目名称, 则代码将不会编译, 除非您调整所有命名空间以匹配您在创建项目时输入的 Visual Studio 项目名称。

选择“确定”****。 在 "**新建跨平台应用程序**" 对话框中, 选择 "**空白**" 模板, 并确保**代码共享策略**选择是 **.net Standard**。 如果您计划跳过某个特定平台, 则可以在 "**平台**" 下立即取消选中它。 选择 **"确定"** 以创建解决方案。

![Visual Studio 2017 新建跨平台应用程序对话框](images/new-cross-platform-app-dialog.png)

在继续之前, 请安装稍后将使用的一些其他 NuGet 包。

- 用于处理 Azure AD 身份验证和令牌管理的[Microsoft Identity。](https://www.nuget.org/packages/Microsoft.Identity.Client/)
- 用于调用 microsoft Graph 的[microsoft](https://www.nuget.org/packages/Microsoft.Graph/) graph。

选择 "**工具 > NuGet 包管理器 > 程序包管理器控制台**"。 在 "程序包管理器控制台" 中, 输入以下命令。

```Powershell
Install-Package Microsoft.Identity.Client -Version 2.7.0 -Project GraphTutorial
Install-Package Xamarin.Android.Support.Compat -Version 27.0.2.1 -Project GraphTutorial.Android
Install-Package Microsoft.Identity.Client -Version 2.7.0 -Project GraphTutorial.Android
Install-Package Microsoft.Identity.Client -Version 2.7.0 -Project GraphTutorial.iOS
Install-Package Microsoft.Graph -Version 1.12.0 -Project GraphTutorial
```

## <a name="design-the-app"></a>设计应用程序

首先更新`App`类以添加用于跟踪身份验证状态和已登录用户的变量。 在 "**解决方案资源管理器**" 中, 展开 " **GraphTutorial** " 项目, 然后展开**app.xaml**文件。 打开**App.xaml.cs**文件, 并将以下`using`语句添加到文件顶部。

```cs
using System.ComponentModel;
using System.IO;
using System.Reflection;
using System.Threading.Tasks;
```

接下来, 将`INotifyPropertyChanged`接口添加到类声明中。

```cs
public partial class App : Application, INotifyPropertyChanged
```

现在, 将以下属性添加到`App`类中。

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

现在, 将以下函数添加到`App`类中。 `SignIn`、 `SignOut`和`GetUserInfo`函数现在只是占位符。

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

`GetUserPhoto`函数现在返回默认照片。 你可以在此处提供自己的文件, 也可以从[GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)中的示例中下载所使用的文件。 将文件复制到`./GraphTutorial/GraphTutorial`目录。 在 "**解决方案资源管理器**" 中右键单击 " **GraphTutorial** " 项目, 然后依次选择 "**添加**"、"**现有项 ...**"。选择文件`no-profile-pic.png` , 然后选择 "**添加**"。 现在, 在 "**解决方案资源管理器**" 中右键单击该文件, 然后选择 "**属性**"。 在 "**属性**" 窗口中, 将 "**生成操作**" 的值更改为 "**嵌入的资源**"。

![PNG 文件的 "属性" 窗口的屏幕截图](./images/png-file-properties.png)

### <a name="app-navigation"></a>应用程序导航

接下来, 将应用程序的主页面更改[为主-详细信息页面](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page)。 这将提供导航菜单, 以便在应用程序中的视图之间切换。

打开**GraphTutorial**项目中的**MainPage**文件, 并将其内容替换为以下内容。

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

#### <a name="implement-the-menu"></a>实现菜单

首先, 创建一个用于表示菜单项的模型。 右键单击 " **GraphTutorial** " 项目, 然后依次选择 "**添加**"、"**新建文件夹**"。 将文件夹`Models`命名为。

右键单击 "**模型**" 文件夹, 然后依次选择 "**添加**"、"**类 ...**"。命名该类`NavMenuItem` , 然后选择 "**添加**"。 打开 " **NavMenuItem.cs** " 文件, 并将其内容替换为以下内容。

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

现在, 添加菜单页。 右键单击 " **GraphTutorial** " 项目, 然后依次选择 "**添加**"、"**新建项目 ...**"。选择 "**内容页面**" 并命名`MenuPage`页面。 选择**添加**。 打开**MenuPage**文件, 并将其内容替换为以下内容。

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

现在, 展开 "**解决方案资源管理器**" 中的 " **MenuPage** ", 然后打开 " **MenuPage.xaml.cs** " 文件。 将其内容替换为以下内容。

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
        MainPage RootPage { get => Application.Current.MainPage as MainPage; }
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

#### <a name="implement-the-welcome-page"></a>实施欢迎页面

右键单击 " **GraphTutorial** " 项目, 然后依次选择 "**添加**"、"**新建项目 ...**"。选择 "**内容页面**" 并命名`WelcomePage`页面。 选择**添加**。 打开**WelcomePage**文件, 并将其内容替换为以下内容。

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

现在, 展开 "**解决方案资源管理器**" 中的 " **WelcomePage** ", 然后打开 " **WelcomePage.xaml.cs** " 文件。 将以下函数添加到 `WelcomePage` 类。

```cs
private void OnSignIn(object sender, EventArgs e)
{
    (App.Current.MainPage as MasterDetailPage).IsPresented = true;
}
```

#### <a name="add-calendar-page"></a>添加日历页

现在添加 "日历" 页。 这将是现在的占位符。 右键单击 " **GraphTutorial** " 项目, 然后依次选择 "**添加**"、"**新建项目 ...**"。选择 "**内容页面**" 并命名`CalendarPage`页面。 选择**添加**。

现在, 将添加的页面保持为-为。

#### <a name="update-mainpage-code-behind"></a>更新 MainPage 代码隐藏

现在, 已准备好所有页面, 更新**MainPage**的代码隐藏。 展开 "**解决方案资源管理器**" 中的**MainPage** , 打开**MainPage.xaml.cs**文件, 并将其全部内容替换为以下内容。

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

保存所有更改。 右键单击要运行的项目 (Android、iOS 或 UWP), 然后选择 "**设为启动项目**"。 按**F5**或选择**调试 >** 在 Visual Studio 中启动调试。

![应用程序的 Android、iOS 和 UWP 版本的屏幕截图](./images/welcome-page.png)