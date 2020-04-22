# <a name="how-to-run-the-completed-project"></a>如何运行已完成的项目

## <a name="prerequisites"></a>先决条件

若要在此文件夹中运行已完成的项目，您需要以下各项：

- 在开发计算机上安装的[Visual Studio](https://visualstudio.microsoft.com/vs/) 。 （**注意：** 本教程是使用 visual studio 2019 版本16.5.2 和 visual Studio for Mac 版本8.5.1 编写的。 本指南中的步骤可能适用于其他版本，但尚未经过测试。
- 安装在 Visual Studio 安装中的 Xamarin。 有关安装和配置 Xamarin 的说明，请参阅[安装 Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation) 。
- 使用 Outlook.com 上的邮箱的个人 Microsoft 帐户，或者是 Microsoft 工作或学校帐户。

如果你没有 Microsoft 帐户，可以使用以下几种方法获取免费帐户：

- 你可以[注册新的个人 Microsoft 帐户](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。
- 你可以[注册 office 365 开发人员计划](https://developer.microsoft.com/office/dev-program)以获取免费的 office 365 订阅。

## <a name="register-an-application-with-the-azure-portal"></a>在 Azure 门户中注册应用程序

1. 打开浏览器，并转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。 使用**个人帐户**（亦称为“Microsoft 帐户”）或**工作或学校帐户**登录。

1. 选择“新注册”****。 在“注册应用”**** 页上，按如下方式设置值。

    - 将“名称”**** 设置为“`Xamarin Graph Tutorial`”。
    - 将“受支持的帐户类型”**** 设置为“任何组织目录中的帐户和个人 Microsoft 帐户”****。
    - 在 "**重定向 URI （可选）**" 下，将 dropdown 更改为 "**公用客户端（移动 & 桌面）**"，并将值设置为`msauth://com.companyname.GraphTutorial`。

    !["注册应用程序" 页的屏幕截图](../../tutorial/images/aad-register-an-app.png)

1. 选择“注册”****。 在 " **Xamarin Graph 教程**" 页上，复制**应用程序（客户端） ID**的值并保存它，下一步将需要它。

    ![新应用注册的应用程序 ID 的屏幕截图](../../tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a>配置示例

1. 将`./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example`文件重命名`OAuthSettings.cs`为。
1. 编辑`OAuthSettings.cs`文件并进行以下更改。
    1. 将`YOUR_APP_ID_HERE`替换为你从 Azure 门户获取的**应用程序 Id** 。

## <a name="run-the-sample"></a>运行示例

在 Visual Studio 中，按**F5**或选择 "**调试" > "开始调试**"。
