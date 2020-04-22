<!-- markdownlint-disable MD002 MD041 -->

本教程向您介绍如何构建使用 Microsoft Graph API 检索用户的日历信息的 Xamarin 应用程序。

> [!TIP]
> 如果您只想下载已完成的教程，可以下载或克隆[GitHub 存储库](https://github.com/microsoftgraph/msgraph-training-xamarin)。

## <a name="prerequisites"></a>先决条件

在开始本教程之前，应在运行 Windows 10 的计算机上安装[Visual Studio](https://visualstudio.microsoft.com/vs/) ，并[启用开发人员模式](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)。 如果没有 Visual Studio，请访问 "下载选项" 的上一个链接。

此外，还应将 Xamarin 安装为 Visual Studio 安装的一部分。 有关安装和配置 Xamarin 的说明，请参阅[安装 Xamarin](/xamarin/cross-platform/get-started/installation) 。

（可选）还应具有对安装了 Visual Studio for Mac 的 Mac 的访问权限。 如果您没有访问权限，您仍可以完成本教程，但不能完成特定于 iOS 的分区。

您还应具有一个个人的 Microsoft 帐户，其中包含 Outlook.com 上的邮箱，或 Microsoft 工作或学校帐户。 如果你没有 Microsoft 帐户，可以使用以下几种方法获取免费帐户：

- 你可以[注册新的个人 Microsoft 帐户](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。
- 你可以[注册 office 365 开发人员计划](https://developer.microsoft.com/office/dev-program)以获取免费的 office 365 订阅。

> [!NOTE]
> 本教程是使用 Visual Studio 2019 版本16.5.2 和 Visual Studio for Mac 版本8.5.1 编写的。 两台计算机都安装了 Android SDK 平台28。 本指南中的步骤可能适用于其他版本，但尚未经过测试。

## <a name="feedback"></a>反馈

请在[GitHub 存储库](https://github.com/microsoftgraph/msgraph-training-xamarin)中提供有关本教程的任何反馈。
