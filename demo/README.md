# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="a0cbf-101">如何运行已完成的项目</span><span class="sxs-lookup"><span data-stu-id="a0cbf-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a0cbf-102">先决条件</span><span class="sxs-lookup"><span data-stu-id="a0cbf-102">Prerequisites</span></span>

<span data-ttu-id="a0cbf-103">若要在此文件夹中运行已完成的项目，您需要以下各项：</span><span class="sxs-lookup"><span data-stu-id="a0cbf-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="a0cbf-104">在开发计算机上安装的[Visual Studio](https://visualstudio.microsoft.com/vs/) 。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-104">[Visual Studio](https://visualstudio.microsoft.com/vs/) installed on your development machine.</span></span> <span data-ttu-id="a0cbf-105">（**注意：** 本教程是使用 visual studio 2019 版本16.5.2 和 visual Studio for Mac 版本8.5.1 编写的。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-105">(**Note:** This tutorial was written with Visual Studio 2019 version 16.5.2 and Visual Studio for Mac version 8.5.1.</span></span> <span data-ttu-id="a0cbf-106">本指南中的步骤可能适用于其他版本，但尚未经过测试。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-106">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="a0cbf-107">安装在 Visual Studio 安装中的 Xamarin。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-107">Xamarin installed as part of your Visual Studio installation.</span></span> <span data-ttu-id="a0cbf-108">有关安装和配置 Xamarin 的说明，请参阅[安装 Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation) 。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-108">See [Installing Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation) for instructions on installing and configuring Xamarin.</span></span>
- <span data-ttu-id="a0cbf-109">使用 Outlook.com 上的邮箱的个人 Microsoft 帐户，或者是 Microsoft 工作或学校帐户。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-109">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="a0cbf-110">如果你没有 Microsoft 帐户，可以使用以下几种方法获取免费帐户：</span><span class="sxs-lookup"><span data-stu-id="a0cbf-110">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="a0cbf-111">你可以[注册新的个人 Microsoft 帐户](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-111">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="a0cbf-112">你可以[注册 office 365 开发人员计划](https://developer.microsoft.com/office/dev-program)以获取免费的 office 365 订阅。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-112">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-an-application-with-the-azure-portal"></a><span data-ttu-id="a0cbf-113">在 Azure 门户中注册应用程序</span><span class="sxs-lookup"><span data-stu-id="a0cbf-113">Register an application with the Azure Portal</span></span>

1. <span data-ttu-id="a0cbf-114">打开浏览器，并转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-114">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="a0cbf-115">使用**个人帐户**（亦称为“Microsoft 帐户”）或**工作或学校帐户**登录。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-115">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="a0cbf-116">选择“新注册”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-116">Select **New registration**.</span></span> <span data-ttu-id="a0cbf-117">在“注册应用”\*\*\*\* 页上，按如下方式设置值。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-117">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="a0cbf-118">将“名称”\*\*\*\* 设置为“`Xamarin Graph Tutorial`”。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-118">Set **Name** to `Xamarin Graph Tutorial`.</span></span>
    - <span data-ttu-id="a0cbf-119">将“受支持的帐户类型”\*\*\*\* 设置为“任何组织目录中的帐户和个人 Microsoft 帐户”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-119">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="a0cbf-120">在 "**重定向 URI （可选）**" 下，将 dropdown 更改为 "**公用客户端（移动 & 桌面）**"，并将值设置为`msauth://com.companyname.GraphTutorial`。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-120">Under **Redirect URI (optional)**, change the dropdown to **Public client (mobile & desktop)**, and set the value to `msauth://com.companyname.GraphTutorial`.</span></span>

    !["注册应用程序" 页的屏幕截图](../../tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="a0cbf-122">选择“注册”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-122">Choose **Register**.</span></span> <span data-ttu-id="a0cbf-123">在 " **Xamarin Graph 教程**" 页上，复制**应用程序（客户端） ID**的值并保存它，下一步将需要它。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-123">On the **Xamarin Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新应用注册的应用程序 ID 的屏幕截图](../../tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a><span data-ttu-id="a0cbf-125">配置示例</span><span class="sxs-lookup"><span data-stu-id="a0cbf-125">Configure the sample</span></span>

1. <span data-ttu-id="a0cbf-126">将`./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example`文件重命名`OAuthSettings.cs`为。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-126">Rename the `./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example` file to `OAuthSettings.cs`.</span></span>
1. <span data-ttu-id="a0cbf-127">编辑`OAuthSettings.cs`文件并进行以下更改。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-127">Edit the `OAuthSettings.cs` file and make the following changes.</span></span>
    1. <span data-ttu-id="a0cbf-128">将`YOUR_APP_ID_HERE`替换为你从 Azure 门户获取的**应用程序 Id** 。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-128">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the Azure portal.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="a0cbf-129">运行示例</span><span class="sxs-lookup"><span data-stu-id="a0cbf-129">Run the sample</span></span>

<span data-ttu-id="a0cbf-130">在 Visual Studio 中，按**F5**或选择 "**调试" > "开始调试**"。</span><span class="sxs-lookup"><span data-stu-id="a0cbf-130">In Visual Studio, press **F5** or choose **Debug > Start Debugging**.</span></span>
