<!-- markdownlint-disable MD002 MD041 -->

在此部分中，您将添加在用户日历上创建事件的能力。

1. 为新事件视图添加新页面。 在解决方案资源管理器中右键 **单击 GraphTu一l** 项目，**然后选择>新项目...** 选择 **"空白页**"， `NewEventPage.xaml` 在"名称"字段中输入，然后选择"**添加"。**

1. 打开 **NewEventPage.xaml** 并将其内容替换为以下内容。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml" id="NewEventPageXamlSnippet":::

1. 打开 **NewEventPage.xaml.cs，** 将以下 `using` 语句添加到文件顶部。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="UsingStatementsSnippet":::

1. 将 **INotifyPropertyChange 接口** 添加到 **NewEventPage** 类。 将现有的类声明替换为以下内容。

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

1. 将以下属性添加到 **NewEventPage** 类。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="PropertiesSnippet":::

1. 添加以下代码以创建事件。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="CreateEventSnippet":::

1. 保存更改并运行该应用程序。 登录，选择 **"新建** 事件"菜单项，填写表单，然后选择"创建"将事件添加到用户日历。

    ![新事件页面的屏幕截图](images/new-event-page.png)
