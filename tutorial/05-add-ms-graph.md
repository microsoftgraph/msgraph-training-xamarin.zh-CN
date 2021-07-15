<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b4bc4-101">在此练习中，你将将 Microsoft Graph合并到应用程序中。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="b4bc4-102">对于此应用程序，你将使用[适用于 .NET 的 Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-dotnet)客户端库调用 Microsoft Graph。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="b4bc4-103">从 Outlook 获取日历事件</span><span class="sxs-lookup"><span data-stu-id="b4bc4-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="b4bc4-104">打开 **GraphTu一** l 项目中的 **CalendarPage.xaml，** 并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-104">Open **CalendarPage.xaml** in the **GraphTutorial** project and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="b4bc4-105">打开 **CalendarPage.xaml.cs，** 在文件顶部 `using` 添加以下语句。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-105">Open **CalendarPage.xaml.cs** and add the following `using` statements at the top of the file.</span></span>

    ```csharp
    using Microsoft.Graph;

    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. <span data-ttu-id="b4bc4-106">将以下函数添加到 类，获取用户时区中本周 `CalendarPage` 的开始。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-106">Add the following function to the `CalendarPage` class to get the start of the current week in the user's time zone.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml.cs" id="GetStartOfWeekSnippet":::

1. <span data-ttu-id="b4bc4-107">将以下函数添加到 `CalendarPage` 类，获取用户的事件并显示 JSON 响应。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-107">Add the following function to the `CalendarPage` class to get the user's events and display the JSON response.</span></span>

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Get start and end of week in user's time zone
        var startOfWeek = GetUtcStartOfWeekInTimeZone(DateTime.Today, App.UserTimeZone);
        var endOfWeek = startOfWeek.AddDays(7);

        var queryOptions = new List<QueryOption>
        {
            new QueryOption("startDateTime", startOfWeek.ToString("o")),
            new QueryOption("endDateTime", endOfWeek.ToString("o"))
        };

        var timeZoneString =
            Xamarin.Forms.Device.RuntimePlatform == Xamarin.Forms.Device.UWP ?
                App.UserTimeZone.StandardName : App.UserTimeZone.DisplayName;

        // Get the events
        var events = await App.GraphClient.Me.CalendarView.Request(queryOptions)
            .Header("Prefer", $"outlook.timezone=\"{timeZoneString}\"")
            .Select(e => new
            {
                e.Subject,
                e.Organizer,
                e.Start,
                e.End
            })
            .OrderBy("start/DateTime")
            .Top(50)
            .GetAsync();

        // Temporary
        JSONResponse.Text = App.GraphClient.HttpProvider.Serializer.SerializeObject(events.CurrentPage);
    }
    ```

    <span data-ttu-id="b4bc4-108">考虑代码正在 `OnAppearing` 执行哪些工作。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-108">Consider what the code in `OnAppearing` is doing.</span></span>

    - <span data-ttu-id="b4bc4-109">将调用的 URL 为 `/v1.0/me/calendarview`。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-109">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
        - <span data-ttu-id="b4bc4-110">`startDateTime`和 `endDateTime` 参数定义日历视图的起始和结束。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-110">The `startDateTime` and `endDateTime` parameters define the start and end of the calendar view.</span></span>
        - <span data-ttu-id="b4bc4-111">`Prefer: outlook.timezone`标头将导致 `start` 在用户的时区中返回事件的 和 `end` 。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-111">The `Prefer: outlook.timezone` header causes the `start` and `end` of the events to be returned in the user's time zone.</span></span>
        - <span data-ttu-id="b4bc4-112">`Select`函数将每个事件返回的字段限定为应用将实际使用的字段。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-112">The `Select` function limits the fields returned for each event to just those the app will actually use.</span></span>
        - <span data-ttu-id="b4bc4-113">`OrderBy`函数按开始日期和时间对结果进行排序。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-113">The `OrderBy` function sorts the results by the start date and time.</span></span>
        - <span data-ttu-id="b4bc4-114">`Top`函数最多请求 50 个事件。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-114">The `Top` function requests at most 50 events.</span></span>

1. <span data-ttu-id="b4bc4-115">运行应用、登录，然后单击菜单中的 **"** 日历"导航项。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-115">Run the app, sign in, and click the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="b4bc4-116">你应该在用户日历上看到事件的 JSON 转储。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-116">You should see a JSON dump of the events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="b4bc4-117">显示结果</span><span class="sxs-lookup"><span data-stu-id="b4bc4-117">Display the results</span></span>

<span data-ttu-id="b4bc4-118">现在，可以将 JSON 转储替换为某些内容，以用户友好的方式显示结果。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-118">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span>

<span data-ttu-id="b4bc4-119">首先创建绑定[值转换器](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters)，将 Microsoft Graph 的[dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)值转换为用户期望的日期和时间格式。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-119">Start by creating a [binding value converter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) to convert the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) values returned by Microsoft Graph into the date and time formats the user expects.</span></span>

1. <span data-ttu-id="b4bc4-120">右键单击 **GraphTutorial** 项目中的 **"模型**"文件夹，然后选择"**添加**"，然后选择"**类..."。** 将类命名 `GraphDateTimeTimeZoneConverter` ，然后选择"添加 **"。**</span><span class="sxs-lookup"><span data-stu-id="b4bc4-120">Right-click the **Models** folder in the **GraphTutorial** project and select **Add**, then **Class...**. Name the class `GraphDateTimeTimeZoneConverter` and select **Add**.</span></span>

1. <span data-ttu-id="b4bc4-121">将文件的全部内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-121">Replace the entire contents of the file with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. <span data-ttu-id="b4bc4-122">将 **CalendarPage.xaml 的全部** 内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-122">Replace the entire contents of **CalendarPage.xaml** with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml":::

    <span data-ttu-id="b4bc4-123">这会将 替换为 `Editor` `ListView` 。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-123">This replaces the `Editor` with a `ListView`.</span></span> <span data-ttu-id="b4bc4-124">`DataTemplate`用于呈现每个项目的 使用 将 `GraphDateTimeTimeZoneConverter` 事件的 `Start` 和 属性转换为 `End` 字符串。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-124">The `DataTemplate` used to render each item uses the `GraphDateTimeTimeZoneConverter` to convert the `Start` and `End` properties of the event to a string.</span></span>

1. <span data-ttu-id="b4bc4-125">打开 **CalendarPage.xaml.cs** 并从 函数中删除以下 `OnAppearing` 行。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-125">Open **CalendarPage.xaml.cs** and remove the following lines from the `OnAppearing` function.</span></span>

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. <span data-ttu-id="b4bc4-126">在它们的位置，添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-126">In their place, add the following code.</span></span>

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. <span data-ttu-id="b4bc4-127">运行应用、登录，然后单击 **"日历"** 导航项。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-127">Run the app, sign in, and click the **Calendar** navigation item.</span></span> <span data-ttu-id="b4bc4-128">你应该会看到格式化的 **Start** 和 **End** 值的事件列表。</span><span class="sxs-lookup"><span data-stu-id="b4bc4-128">You should see the list of events with the **Start** and **End** values formatted.</span></span>

    ![事件表的屏幕截图](./images/calendar-page.png)
