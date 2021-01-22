<!-- markdownlint-disable MD002 MD041 -->

在此练习中，你将 Microsoft Graph 合并到应用程序中。 对于此应用程序，你将使用 [适用于 .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) 的 Microsoft Graph 客户端库调用 Microsoft Graph。

## <a name="get-calendar-events-from-outlook"></a>从 Outlook 获取日历事件

1. 打开 **GraphTu一** l 项目中的 **CalendarPage.xaml，** 并将其内容替换为以下内容。

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

1. 打开 **CalendarPage.xaml.cs** 并添加 `using` 文件顶部的以下语句。

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. 将以下函数 `CalendarPage` 添加到类，获取用户的事件并显示 JSON 响应。

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

    考虑代码正在 `OnAppearing` 执行哪些工作。

    - 将调用的 URL 为 `/v1.0/me/calendarview` 。
        - 和 `startDateTime` `endDateTime` 参数定义日历视图的开始和结束。
        - 标头将导致在用户的时区中返回事件 `Prefer: outlook.timezone` `start` `end` 和事件。
        - `Select`该函数将每个事件返回的字段限制为仅应用将实际使用的字段。
        - `OrderBy`该函数按开始日期和时间对结果进行排序。
        - `Top`函数最多请求 50 个事件。

1. 运行应用，登录，然后单击菜单中的 **"** 日历"导航项。 您应该在用户的日历上看到事件的 JSON 转储。

## <a name="display-the-results"></a>显示结果

现在，可以将 JSON 转储替换为某些内容，以用户友好的方式显示结果。

首先创建绑定 [值转换器](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) ，将 Microsoft Graph 返回 [的 dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) 值转换为用户期望的日期和时间格式。

1. 右键单击 **GraphTu一l** 项目中的 Models 文件夹，然后选择 **"添加**"，然后选择"**类..."。** 命名类 `GraphDateTimeTimeZoneConverter` ，然后选择"**添加"。**

1. 将文件的全部内容替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. 将 **CalendarPage.xaml** 的全部内容替换为以下内容。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml" id="CalendarPageXamlSnippet":::

    这会将 `Editor` . `ListView` 用于 `DataTemplate` 呈现每个项目的项使用 `GraphDateTimeTimeZoneConverter` 将 `Start` 事件的 and `End` 属性转换为字符串。

1. 打开 **CalendarPage.xaml.cs** 并从函数中删除以下 `OnAppearing` 行。

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. 在它们的位置，添加以下代码。

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. 运行应用，登录，然后单击 **"日历"** 导航项。 应该会看到设置"开始"和" **结束"值** 格式 **的事件列表** 。

    ![事件表的屏幕截图](./images/calendar-page.png)
