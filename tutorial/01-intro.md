<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="10340-101">本教程向您介绍如何构建使用 Microsoft Graph API 检索用户的日历信息的 Xamarin 应用程序。</span><span class="sxs-lookup"><span data-stu-id="10340-101">This tutorial teaches you how to build a Xamarin app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="10340-102">如果您只想下载已完成的教程, 可以下载或克隆[GitHub 存储库](https://github.com/microsoftgraph/msgraph-training-xamarin)。</span><span class="sxs-lookup"><span data-stu-id="10340-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="10340-103">先决条件</span><span class="sxs-lookup"><span data-stu-id="10340-103">Prerequisites</span></span>

<span data-ttu-id="10340-104">在开始本教程之前, 应在运行 Windows 10 的计算机上安装[Visual Studio](https://visualstudio.microsoft.com/vs/) , 并[启用开发人员模式](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)。</span><span class="sxs-lookup"><span data-stu-id="10340-104">Before you start this tutorial, you should have [Visual Studio](https://visualstudio.microsoft.com/vs/) installed on a computer running Windows 10 with [Developer mode turned on](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).</span></span> <span data-ttu-id="10340-105">如果没有 Visual Studio, 请访问 "下载选项" 的上一个链接。</span><span class="sxs-lookup"><span data-stu-id="10340-105">If you do not have Visual Studio, visit the previous link for download options.</span></span>

<span data-ttu-id="10340-106">此外, 还应将 Xamarin 安装为 Visual Studio 安装的一部分。</span><span class="sxs-lookup"><span data-stu-id="10340-106">You should also have Xamarin installed as part of your Visual Studio installation.</span></span> <span data-ttu-id="10340-107">有关安装和配置 Xamarin 的说明, 请参阅[安装 Xamarin](/xamarin/cross-platform/get-started/installation) 。</span><span class="sxs-lookup"><span data-stu-id="10340-107">See [Installing Xamarin](/xamarin/cross-platform/get-started/installation) for instructions on installing and configuring Xamarin.</span></span>

<span data-ttu-id="10340-108">(可选) 还应具有对安装了 Visual Studio for Mac 的 Mac 的访问权限。</span><span class="sxs-lookup"><span data-stu-id="10340-108">Optionally you should also have access to a Mac with Visual Studio for Mac installed.</span></span> <span data-ttu-id="10340-109">如果您没有访问权限, 您仍可以完成本教程, 但不能完成特定于 iOS 的分区。</span><span class="sxs-lookup"><span data-stu-id="10340-109">If you do not have access, you can still complete this tutorial, but will be unable to complete the iOS-specific sections.</span></span>

> [!NOTE]
> <span data-ttu-id="10340-110">本教程是使用 Visual Studio 2019 版本16.1.0 和 Visual Studio for Mac 版本7.7.4 编写的。</span><span class="sxs-lookup"><span data-stu-id="10340-110">This tutorial was written with Visual Studio 2019 version 16.1.0 and Visual Studio for Mac version 7.7.4.</span></span> <span data-ttu-id="10340-111">两台计算机都安装了 Android SDK 平台28。</span><span class="sxs-lookup"><span data-stu-id="10340-111">Both machines have the Android SDK Platform 28 installed.</span></span> <span data-ttu-id="10340-112">本指南中的步骤可能适用于其他版本, 但尚未经过测试。</span><span class="sxs-lookup"><span data-stu-id="10340-112">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="10340-113">反馈</span><span class="sxs-lookup"><span data-stu-id="10340-113">Feedback</span></span>

<span data-ttu-id="10340-114">请在[GitHub 存储库](https://github.com/microsoftgraph/msgraph-training-xamarin)中提供有关本教程的任何反馈。</span><span class="sxs-lookup"><span data-stu-id="10340-114">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-xamarin).</span></span>
