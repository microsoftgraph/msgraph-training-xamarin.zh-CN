<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="08571-101">在本练习中，将把 Microsoft Graph 合并到应用程序中。</span><span class="sxs-lookup"><span data-stu-id="08571-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="08571-102">对于此应用程序，您将使用[Microsoft Graph 客户端库进行 .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet)以调用 microsoft graph。</span><span class="sxs-lookup"><span data-stu-id="08571-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="08571-103">从 Outlook 获取日历事件</span><span class="sxs-lookup"><span data-stu-id="08571-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="08571-104">在**GraphTutorial**项目中打开**CalendarPage** ，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="08571-104">Open **CalendarPage.xaml** in the **GraphTutorial** project and replace its contents with the following.</span></span>

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 Title="Calendar"
                 x:Class="GraphTutorial.CalendarPage">
        <ContentPage.Content>
            <StackLayout>
                <Editor x:Name="JSONResponse"
                        IsSpellCheckEnabled="False"
                        HorizontalOptions="FillAndExpand"
                        VerticalOptions="FillAndExpand"/>
            </StackLayout>
        </ContentPage.Content>
    </ContentPage>
    ```

1. <span data-ttu-id="08571-105">打开**CalendarPage.xaml.cs** ，并在文件`using`顶部添加以下语句。</span><span class="sxs-lookup"><span data-stu-id="08571-105">Open **CalendarPage.xaml.cs** and add the following `using` statements at the top of the file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. <span data-ttu-id="08571-106">将以下函数添加到`CalendarPage`类，以获取用户的事件并显示 JSON 响应。</span><span class="sxs-lookup"><span data-stu-id="08571-106">Add the following function to the `CalendarPage` class to get the user's events and display the JSON response.</span></span>

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Get the events
        var events = await App.GraphClient.Me.Events.Request()
            .Select(e => new
            {
                e.Subject,
                e.Organizer,
                e.Start,
                e.End
            })
            .OrderBy("createdDateTime DESC")
            .GetAsync();

        // Temporary
        JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    }
    ```

    <span data-ttu-id="08571-107">考虑中`OnAppearing`的代码执行的操作。</span><span class="sxs-lookup"><span data-stu-id="08571-107">Consider what the code in `OnAppearing` is doing.</span></span>

    - <span data-ttu-id="08571-108">将调用的 URL 为`/v1.0/me/events`。</span><span class="sxs-lookup"><span data-stu-id="08571-108">The URL that will be called is `/v1.0/me/events`.</span></span>
    - <span data-ttu-id="08571-109">`Select`函数将为每个事件返回的字段限制为仅供视图实际使用的字段。</span><span class="sxs-lookup"><span data-stu-id="08571-109">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="08571-110">`OrderBy`函数按其创建日期和时间对结果进行排序，最新项目最先开始。</span><span class="sxs-lookup"><span data-stu-id="08571-110">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="08571-111">运行应用程序，登录，然后单击菜单中的 "**日历**" 导航项。</span><span class="sxs-lookup"><span data-stu-id="08571-111">Run the app, sign in, and click the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="08571-112">您应该会看到用户日历上的事件的 JSON 转储。</span><span class="sxs-lookup"><span data-stu-id="08571-112">You should see a JSON dump of the events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="08571-113">显示结果</span><span class="sxs-lookup"><span data-stu-id="08571-113">Display the results</span></span>

<span data-ttu-id="08571-114">现在，您可以将 JSON 转储替换为以用户友好的方式显示结果的内容。</span><span class="sxs-lookup"><span data-stu-id="08571-114">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span>

<span data-ttu-id="08571-115">首先创建[绑定值转换器](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters)，将 Microsoft Graph 返回的[datetimetimezone type](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)值转换为用户预期的日期和时间格式。</span><span class="sxs-lookup"><span data-stu-id="08571-115">Start by creating a [binding value converter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) to convert the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) values returned by Microsoft Graph into the date and time formats the user expects.</span></span>

1. <span data-ttu-id="08571-116">右键单击 " **GraphTutorial** " 项目中的 "**模型**" 文件夹，然后依次选择 "**添加**"、"**类 ...**"。命名该类`GraphDateTimeTimeZoneConverter`并选择 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="08571-116">Right-click the **Models** folder in the **GraphTutorial** project and select **Add**, then **Class...**. Name the class `GraphDateTimeTimeZoneConverter` and select **Add**.</span></span>

1. <span data-ttu-id="08571-117">将文件的全部内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="08571-117">Replace the entire contents of the file with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. <span data-ttu-id="08571-118">将**CalendarPage**的全部内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="08571-118">Replace the entire contents of **CalendarPage.xaml** with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml":::

    <span data-ttu-id="08571-119">这会将`Editor`替换替换`ListView`为。</span><span class="sxs-lookup"><span data-stu-id="08571-119">This replaces the `Editor` with a `ListView`.</span></span> <span data-ttu-id="08571-120">`DataTemplate`用于呈现每个项`GraphDateTimeTimeZoneConverter`的使用将事件的`Start`和`End`属性转换为字符串。</span><span class="sxs-lookup"><span data-stu-id="08571-120">The `DataTemplate` used to render each item uses the `GraphDateTimeTimeZoneConverter` to convert the `Start` and `End` properties of the event to a string.</span></span>

1. <span data-ttu-id="08571-121">打开**CalendarPage.xaml.cs** ，并从`OnAppearing`函数中删除以下行。</span><span class="sxs-lookup"><span data-stu-id="08571-121">Open **CalendarPage.xaml.cs** and remove the following lines from the `OnAppearing` function.</span></span>

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. <span data-ttu-id="08571-122">在其位置添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="08571-122">In their place, add the following code.</span></span>

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. <span data-ttu-id="08571-123">运行应用程序，登录，然后单击 "**日历**" 导航项。</span><span class="sxs-lookup"><span data-stu-id="08571-123">Run the app, sign in, and click the **Calendar** navigation item.</span></span> <span data-ttu-id="08571-124">您应该会看到 "**开始**" 和 "**结束**" 值格式化的事件的列表。</span><span class="sxs-lookup"><span data-stu-id="08571-124">You should see the list of events with the **Start** and **End** values formatted.</span></span>

    ![事件表的屏幕截图](./images/calendar-page.png)
