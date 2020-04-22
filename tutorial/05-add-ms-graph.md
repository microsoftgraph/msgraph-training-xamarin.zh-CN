<!-- markdownlint-disable MD002 MD041 -->

在本练习中，将把 Microsoft Graph 合并到应用程序中。 对于此应用程序，您将使用[Microsoft Graph 客户端库进行 .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet)以调用 microsoft graph。

## <a name="get-calendar-events-from-outlook"></a>从 Outlook 获取日历事件

1. 在**GraphTutorial**项目中打开**CalendarPage** ，并将其内容替换为以下内容。

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

1. 打开**CalendarPage.xaml.cs** ，并在文件`using`顶部添加以下语句。

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. 将以下函数添加到`CalendarPage`类，以获取用户的事件并显示 JSON 响应。

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

    考虑中`OnAppearing`的代码执行的操作。

    - 将调用的 URL 为`/v1.0/me/events`。
    - `Select`函数将为每个事件返回的字段限制为仅供视图实际使用的字段。
    - `OrderBy`函数按其创建日期和时间对结果进行排序，最新项目最先开始。

1. 运行应用程序，登录，然后单击菜单中的 "**日历**" 导航项。 您应该会看到用户日历上的事件的 JSON 转储。

## <a name="display-the-results"></a>显示结果

现在，您可以将 JSON 转储替换为以用户友好的方式显示结果的内容。

首先创建[绑定值转换器](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters)，将 Microsoft Graph 返回的[datetimetimezone type](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)值转换为用户预期的日期和时间格式。

1. 右键单击 " **GraphTutorial** " 项目中的 "**模型**" 文件夹，然后依次选择 "**添加**"、"**类 ...**"。命名该类`GraphDateTimeTimeZoneConverter`并选择 "**添加**"。

1. 将文件的全部内容替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. 将**CalendarPage**的全部内容替换为以下内容。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml":::

    这会将`Editor`替换替换`ListView`为。 `DataTemplate`用于呈现每个项`GraphDateTimeTimeZoneConverter`的使用将事件的`Start`和`End`属性转换为字符串。

1. 打开**CalendarPage.xaml.cs** ，并从`OnAppearing`函数中删除以下行。

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. 在其位置添加以下代码。

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. 运行应用程序，登录，然后单击 "**日历**" 导航项。 您应该会看到 "**开始**" 和 "**结束**" 值格式化的事件的列表。

    ![事件表的屏幕截图](./images/calendar-page.png)
