<!-- markdownlint-disable MD002 MD041 -->

本教程指导你如何生成使用 Microsoft Graph API 检索用户的日历信息的 Xamarin 应用。

> [!TIP]
> 如果只想下载已完成的教程，可以下载或克隆GitHub[存储库](https://github.com/microsoftgraph/msgraph-training-xamarin)。

## <a name="prerequisites"></a>先决条件

在开始本教程之前，应该先在Visual Studio开发人员[](https://visualstudio.microsoft.com/vs/)模式Windows 10运行的计算机上安装[。](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) 如果尚未安装Visual Studio，请访问上一链接，查看下载选项。

你还应在安装 Xamarin 时安装 Xamarin Visual Studio安装。 有关 [安装和配置 Xamarin](/xamarin/cross-platform/get-started/installation) 的说明，请参阅安装 Xamarin。

（可选）你还应具有对已安装Visual Studio for Mac Mac 的访问权限。 如果没有访问权限，仍可以完成本教程，但无法完成特定于 iOS 的部分。

您还应该有一个在 Outlook.com 上拥有邮箱的个人 Microsoft 帐户，或者一个 Microsoft 工作或学校帐户。 如果你没有 Microsoft 帐户，则有几个选项可以获取免费帐户：

- 你可以 [注册新的个人 Microsoft 帐户](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。
- 你可以[注册开发人员计划Office 365](https://developer.microsoft.com/office/dev-program)免费订阅Office 365订阅。

> [!NOTE]
> 本教程是使用 Visual Studio 2019 版本 16.10.3 和 Visual Studio for Mac 8.5.1 编写的。 两台计算机都安装了 Android SDK 平台 28。 本指南中的步骤可能与其他版本一起运行，但尚未经过测试。

## <a name="feedback"></a>反馈

Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-xamarin).
