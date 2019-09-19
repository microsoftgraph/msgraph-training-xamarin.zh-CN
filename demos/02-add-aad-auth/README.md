# <a name="completed-module-add-azure-ad-authentication"></a>已完成模块：添加 Azure AD 身份验证

此目录中的项目版本反映了通过[添加 AZURE AD 身份验证](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=3)完成教程。 如果您使用此版本的项目，您需要完成教程的其余部分（从[获取日历数据](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=4)开始）。

> **注意：** 假定您已在 Azure 门户中注册了应用程序，如在[门户中注册应用](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=2)程序中所指定的那样。 您需要配置此示例版本，如下所示：
>
> 1. 将`./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example`文件重命名`OAuthSettings.cs`为。
> 1. 编辑`OAuthSettings.cs`文件并进行以下更改。
>     1. 将`YOUR_APP_ID_HERE`替换为你从 Azure 门户获取的**应用程序 Id** 。
> 1. 在 " **GraphTutorial** " 项目中，打开`Info.plist`文件。
>    1. 在 "**高级**" 选项卡上，找到 " **URL 类型**" 部分。 在 " **URL 架构**" 值中`YOUR_APP_ID_HERE` ，将替换为您从 Azure 门户获取的**应用程序 Id** 。
> 1. 在 " **GraphTutorial** " 项目中，打开`Properties/AndroidManifest.xml`文件。
>     1. 将`YOUR_APP_ID_HERE`替换为你从 Azure 门户获取的**应用程序 Id** 。
