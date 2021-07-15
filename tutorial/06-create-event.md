<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="84174-101">在此部分中，您将添加在用户日历上创建事件的能力。</span><span class="sxs-lookup"><span data-stu-id="84174-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="84174-102">打开 **NewEventPage.xaml，** 并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="84174-102">Open **NewEventPage.xaml** and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml":::

1. <span data-ttu-id="84174-103">打开 **NewEventPage.xaml.cs，** 将以下语句 `using` 添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="84174-103">Open **NewEventPage.xaml.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System.ComponentModel;
    using Microsoft.Graph;
    ```

1. <span data-ttu-id="84174-104">将 **INotifyPropertyChange** 接口添加到 **NewEventPage** 类。</span><span class="sxs-lookup"><span data-stu-id="84174-104">Add the **INotifyPropertyChange** interface to the **NewEventPage** class.</span></span> <span data-ttu-id="84174-105">将现有的类声明替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="84174-105">Replace the existing class declaration with the following.</span></span>

    ```csharp
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class NewEventPage : ContentPage, INotifyPropertyChanged
    {
        public NewEventPage()
        {
            InitializeComponent();
            BindingContext = this;
        }
    }
    ```

1. <span data-ttu-id="84174-106">将以下属性添加到 **NewEventPage** 类。</span><span class="sxs-lookup"><span data-stu-id="84174-106">Add the following properties to the **NewEventPage** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="PropertiesSnippet":::

1. <span data-ttu-id="84174-107">添加以下代码以创建事件。</span><span class="sxs-lookup"><span data-stu-id="84174-107">Add the following code to create the event.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="CreateEventSnippet":::

1. <span data-ttu-id="84174-108">保存更改并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="84174-108">Save your changes and run the app.</span></span> <span data-ttu-id="84174-109">登录，选择"**新建事件**"菜单项，填写表单，然后选择"创建"将事件添加到用户日历。</span><span class="sxs-lookup"><span data-stu-id="84174-109">Sign in, select the **New event** menu item, fill in the form, and select **Create** to add an event to the user's calendar.</span></span>

    ![新事件页面的屏幕截图](images/new-event-page.png)
