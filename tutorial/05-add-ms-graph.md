<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d5bf0-101">在此练习中，你将 Microsoft Graph 合并到应用程序中。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="d5bf0-102">对于此应用程序，你将使用 [适用于 .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) 的 Microsoft Graph 客户端库调用 Microsoft Graph。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="d5bf0-103">从 Outlook 获取日历事件</span><span class="sxs-lookup"><span data-stu-id="d5bf0-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="d5bf0-104">打开 **GraphTu一** l 项目中的 **CalendarPage.xaml，** 并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-104">Open **CalendarPage.xaml** in the **GraphTutorial** project and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="d5bf0-105">打开 **CalendarPage.xaml.cs** 并添加 `using` 文件顶部的以下语句。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-105">Open **CalendarPage.xaml.cs** and add the following `using` statements at the top of the file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. <span data-ttu-id="d5bf0-106">将以下函数添加到类，以在用户的时区获取当前 `CalendarPage` 一周的开始。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-106">Add the following function to the `CalendarPage` class to get the start of the current week in the user's time zone.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml.cs" id="GetStartOfWeekSnippet":::

1. <span data-ttu-id="d5bf0-107">将以下函数 `CalendarPage` 添加到类，获取用户的事件并显示 JSON 响应。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-107">Add the following function to the `CalendarPage` class to get the user's events and display the JSON response.</span></span>

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

        // Get the events
        var events = await App.GraphClient.Me.CalendarView.Request(queryOptions)
            .Header("Prefer", $"outlook.timezone=\"{App.UserTimeZone.DisplayName}\"")
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
        JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    }
    ```

    <span data-ttu-id="d5bf0-108">考虑代码正在 `OnAppearing` 执行哪些工作。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-108">Consider what the code in `OnAppearing` is doing.</span></span>

    - <span data-ttu-id="d5bf0-109">将调用的 URL 为 `/v1.0/me/calendarview` 。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-109">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
        - <span data-ttu-id="d5bf0-110">和 `startDateTime` `endDateTime` 参数定义日历视图的开始和结束。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-110">The `startDateTime` and `endDateTime` parameters define the start and end of the calendar view.</span></span>
        - <span data-ttu-id="d5bf0-111">标头将导致在用户的时区中返回事件 `Prefer: outlook.timezone` `start` `end` 和事件。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-111">The `Prefer: outlook.timezone` header causes the `start` and `end` of the events to be returned in the user's time zone.</span></span>
        - <span data-ttu-id="d5bf0-112">`Select`该函数将每个事件返回的字段限制为仅应用将实际使用的字段。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-112">The `Select` function limits the fields returned for each event to just those the app will actually use.</span></span>
        - <span data-ttu-id="d5bf0-113">`OrderBy`该函数按开始日期和时间对结果进行排序。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-113">The `OrderBy` function sorts the results by the start date and time.</span></span>
        - <span data-ttu-id="d5bf0-114">`Top`函数最多请求 50 个事件。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-114">The `Top` function requests at most 50 events.</span></span>

1. <span data-ttu-id="d5bf0-115">运行应用，登录，然后单击菜单中的 **"** 日历"导航项。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-115">Run the app, sign in, and click the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="d5bf0-116">您应该在用户的日历上看到事件的 JSON 转储。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-116">You should see a JSON dump of the events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="d5bf0-117">显示结果</span><span class="sxs-lookup"><span data-stu-id="d5bf0-117">Display the results</span></span>

<span data-ttu-id="d5bf0-118">现在，可以将 JSON 转储替换为某些内容，以用户友好的方式显示结果。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-118">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span>

<span data-ttu-id="d5bf0-119">首先创建绑定 [值转换器](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) ，将 Microsoft Graph 返回 [的 dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) 值转换为用户期望的日期和时间格式。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-119">Start by creating a [binding value converter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) to convert the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) values returned by Microsoft Graph into the date and time formats the user expects.</span></span>

1. <span data-ttu-id="d5bf0-120">右键单击 **GraphTu一l** 项目中的 Models 文件夹，然后选择 **"添加**"，然后选择"**类..."。** 命名类 `GraphDateTimeTimeZoneConverter` ，然后选择"**添加"。**</span><span class="sxs-lookup"><span data-stu-id="d5bf0-120">Right-click the **Models** folder in the **GraphTutorial** project and select **Add**, then **Class...**. Name the class `GraphDateTimeTimeZoneConverter` and select **Add**.</span></span>

1. <span data-ttu-id="d5bf0-121">将文件的全部内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-121">Replace the entire contents of the file with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. <span data-ttu-id="d5bf0-122">将 **CalendarPage.xaml** 的全部内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-122">Replace the entire contents of **CalendarPage.xaml** with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml" id="CalendarPageXamlSnippet":::

    <span data-ttu-id="d5bf0-123">这会将 `Editor` . `ListView`</span><span class="sxs-lookup"><span data-stu-id="d5bf0-123">This replaces the `Editor` with a `ListView`.</span></span> <span data-ttu-id="d5bf0-124">用于 `DataTemplate` 呈现每个项目的项使用 `GraphDateTimeTimeZoneConverter` 将 `Start` 事件的 and `End` 属性转换为字符串。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-124">The `DataTemplate` used to render each item uses the `GraphDateTimeTimeZoneConverter` to convert the `Start` and `End` properties of the event to a string.</span></span>

1. <span data-ttu-id="d5bf0-125">打开 **CalendarPage.xaml.cs** 并从函数中删除以下 `OnAppearing` 行。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-125">Open **CalendarPage.xaml.cs** and remove the following lines from the `OnAppearing` function.</span></span>

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. <span data-ttu-id="d5bf0-126">在它们的位置，添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-126">In their place, add the following code.</span></span>

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. <span data-ttu-id="d5bf0-127">运行应用、登录并单击 **"日历"** 导航项。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-127">Run the app, sign in, and click the **Calendar** navigation item.</span></span> <span data-ttu-id="d5bf0-128">应该会看到设置"开始"和" **结束"值** 格式 **的事件列表** 。</span><span class="sxs-lookup"><span data-stu-id="d5bf0-128">You should see the list of events with the **Start** and **End** values formatted.</span></span>

    ![事件表的屏幕截图](./images/calendar-page.png)
