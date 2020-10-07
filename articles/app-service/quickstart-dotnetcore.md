---
title: 快速入門：建立 C# ASP.NET Core 應用程式
description: 了解如何藉由部署您的第一個 ASP.NET Core 應用程式，在 Azure App Service 中執行 Web 應用程式。
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 09/24/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperfq1
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: aa8eb945ba77e1a4ac5215acf3bdbc12cac0c4c9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91661120"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>快速入門：在 Azure 中建立 ASP.NET Core Web 應用程式

::: zone pivot="platform-windows"  

在本快速入門中，您將了解如何建立第一個 ASP.NET Core Web 應用程式，並將其部署至 [Azure App Service](overview.md)。 

當您完成時，您會有一個 Azure 資源群組，其中包含 App Service 主控方案和已部署 Web 應用程式的 App Service。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/dotnet/)。
- 本快速入門會將應用程式部署至 Windows 上的 App Service。 若要部署至 _Linux_ 上的 App Service，請參閱[在 App Service 中建立 NET Core Web 應用程式](./quickstart-dotnetcore.md)。
- 安裝包含 **ASP.NET 和 Web 開發**工作負載的 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>。

  若您已安裝 Visual Studio 2019：

  - 選取 [說明]   > [檢查更新]  以安裝最新的 Visual Studio 更新。
  - 選取 **[工具]**  >  **[取得工具及功能]** 。


## <a name="create-an-aspnet-core-web-app"></a>建立 ASP.NET Core Web 應用程式

請遵循下列步驟在 Visual Studio 中建立 ASP.NET Core Web 應用程式：

1. 開啟 Visual Studio，然後選取 [建立新專案]  。

1. 在 [建立新專案]  中選取 [ASP.NET Core Web 應用程式]  ，並確認 **C#** 已列在該選擇的語言中，然後選取 [下一步]  。

1. 在 [設定新專案]  中，將 Web 應用程式專案命名為 myFirstAzureWebApp  ，然後選取 [建立]  。

   ![設定 Web 應用程式專案](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. 您可以將任何類型的 ASP.NET Core Web 應用程式部署至 Azure，但在本快速入門中，請選擇 [Web 應用程式]  範本。 請確定 [驗證]  已設定為 [不需要驗證]  ，且未選取其他選項。 然後，選取 [Create]  \(建立\)。

   ![建立新的 ASP.NET Core Web 應用程式](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. 從 Visual Studio 功能表中，選取 **[偵錯]**  >  **[啟動但不偵錯]** 以在本機執行您的 Web 應用程式。

   ![本機執行的 Web 應用程式](./media/quickstart-dotnetcore/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>發佈 Web 應用程式

若要發佈您的 Web 應用程式，您必須先建立並設定新的 App Service，讓您可以將應用程式發佈到其中。 

在設定 App Service 的過程中，您將建立：

- 新的[資源群組](../azure-resource-manager/management/overview.md#terminology)，以包含此服務的所有 Azure 資源。
- 新的[主控方案](./overview-hosting-plans.md)，以指定用來裝載應用程式的 Web 伺服器陣列位置、大小和功能。

請遵循下列步驟來建立您的 App Service，然後發佈您的 Web 應用程式：

1. 在 [方案總管]  中，以滑鼠右鍵按一下 **myFirstAzureWebApp** 專案，然後選取 [發佈]  。 如果您尚未從 Visual Studio 登入您的 Azure 帳戶，請選取 [新增帳戶]  或 [登入]  。 您也可以建立免費 Azure 帳戶。

1. 在 [挑選發佈目標]  對話方塊中，選擇 [App Service]  ，選取 [新建]  ，然後選取 [建立設定檔]  。

   ![挑選發行目標](./media/quickstart-dotnetcore/pick-publish-target-vs2019.png)

1. 在 [App Service:  新建] 對話方塊中，藉由接受預設名稱或輸入新名稱，為您的應用程式提供全域唯一的**名稱**。 有效字元為：`a-z`、`A-Z`、`0-9` 和 `-`。 此**名稱**會用來作為 Web 應用程式的 URL 前置詞，格式為 `http://<app_name>.azurewebsites.net`。

1. 針對 [訂用帳戶]  ，請接受列出的訂用帳戶，或從下拉式清單中選取一個新的訂用帳戶。

1. 在 [資源群組]  中選取 [新增]  。 在 [新增資源群組名稱]  中，輸入 myResourceGroup  ，然後選取 [確定]  。 

1. 在 [主控方案]  中選取 [新增]  。 

1. 在 [主控方案：  新建] 對話方塊中，輸入下表中指定的值：

   | 設定  | 建議的值 | 描述 |
   | -------- | --------------- | ----------- |
   | **主控方案**  | *myFirstAzureWebAppPlan* | App Service 方案的名稱。 |
   | **位置**      | 歐洲西部 | 裝載 Web 應用程式的資料中心。 |
   | **大小**          | *免費* | [定價層](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)可決定裝載功能。 |
   
   ![建立新的主控方案](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. 將 **Application Insights** 設定為 [無]  。

1. 在 [App Service:  新建] 對話方塊中，選取 [建立]  以開始建立 Azure 資源。

   ![建立新的 App Service](./media/quickstart-dotnetcore/create-new-app-service-vs2019.png)

1. 完成精靈的引導後，請選取 [發佈]  。

   ![將 Web 應用程式發佈至 Azure](./media/quickstart-dotnetcore/publish-web-app-vs2019.png)

   Visual Studio 會將您的 ASP.NET Core Web 應用程式發佈至 Azure，並在預設瀏覽器中啟動應用程式。 

   ![在 Azure 中執行的已發佈 ASP.NET Web 應用程式](./media/quickstart-dotnetcore/web-app-running-live.png)

**恭喜！** ASP.NET Core Web 應用程式已在 Azure App Service 中即時執行。

## <a name="update-the-app-and-redeploy"></a>更新應用程式並重新部署

請遵循下列步驟來更新和重新部署您的 Web 應用程式：

1. 在 [方案總管]  的專案底下，開啟 [頁面]   > [Index.cshtml]  。

1. 將整個 `<div>` 標記取代為下列程式碼：

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. 若要重新部署至 Azure，請在 [方案總管]  中，以滑鼠右鍵按一下 **myFirstAzureWebApp** 專案，然後選取 [發佈]  。

1. 在 [發佈]  摘要頁面中，選取 [發佈]  。

   ![將更新發佈至 Web 應用程式](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png)

發佈完成時，Visual Studio 會啟動瀏覽器以前往 Web 應用程式的 URL。

![Azure 中執行的已更新 ASP.NET Web 應用程式](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>管理 Azure 應用程式

若要管理 Web 應用程式，請移至 [Azure 入口網站](https://portal.azure.com)，然後搜尋並選取 [應用程式服務]  。

![選取 [應用程式服務]](./media/quickstart-dotnetcore/app-services.png)

在 [應用程式服務]  頁面上，選取您的 Web 應用程式名稱。

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="[應用程式服務] 頁面的螢幕擷取畫面，其中已選取範例 web 應用程式。":::

Web 應用程式的**概觀**頁面，其中包含瀏覽、停止、啟動、重新啟動和刪除等基本管理選項。 左側功能表提供的頁面可用來進一步設定您的應用程式。

![Azure 入口網站中的 App Service](./media/quickstart-dotnetcore/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Visual Studio 來建立 ASP.NET Core Web 應用程式，並將其部署至 Azure App Service。

請前往下一篇文章，了解如何建立 .NET Core 應用程式，並將其連線到 SQL Database：

> [!div class="nextstepaction"]
> [ASP.NET Core 搭配 SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [設定 ASP.NET Core 應用程式](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
[Linux 上的 App Service](overview.md#app-service-on-linux) 使用 Linux 作業系統提供可高度擴充、自我修復的 Web 主機服務。 本快速入門示範如何在 Linux 上的 App Service上建立 [.NET Core](/aspnet/core/) 應用程式。 您可使用 [Azure CLI](/cli/azure/get-started-with-azure-cli) 建立應用程式，而且使用 Git 將 .NET Core 程式碼部署至應用程式。

![在 Azure 中執行的範例應用程式](media/quickstart-dotnetcore/dotnet-browse-azure.png)

您可以使用 Mac、Windows 或 Linux 機器，依照本文中的步驟操作。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門：

* <a href="https://git-scm.com/" target="_blank">安裝 Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">安裝最新的 .NET Core 3.1 SDK</a>

[有任何問題嗎？請告訴我們。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="create-the-app-locally"></a>在本機建立應用程式

在您電腦上的終端機視窗中，建立名為 `hellodotnetcore` 的目錄，並將目前的目錄變更為該目錄。

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

建立新的 .NET Core 應用程式。

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>在本機執行應用程式

在本機執行應用程式，以便您查看它在部署至 Azure 時的樣貌。 

還原 NuGet 套件，並執行應用程式。

```bash
dotnet run
```

開啟網頁瀏覽器，然後巡覽至位於 `http://localhost:5000` 的應用程式。

您會看到來自範例應用程式的 **Hello World** 訊息顯示在網頁中。

![使用瀏覽器測試](media/quickstart-dotnetcore/dotnet-browse-local.png)

在終端機視窗中，按 **Ctrl+C** 結束 web 伺服器。 初始化 .NET Core 專案的 Git 存放庫。

```bash
git init
git add .
git commit -m "first commit"
```

[有任何問題嗎？請告訴我們。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[有任何問題嗎？請告訴我們。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[有任何問題嗎？請告訴我們。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[有任何問題嗎？請告訴我們。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

[有任何問題嗎？請告訴我們。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="create-a-web-app"></a>建立 Web 應用程式

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

瀏覽至您剛建立的應用程式。 以您的應用程式名稱取代 _&lt;app-name>_ 。

```bash
https://<app-name>.azurewebsites.net
```

新的應用程式看起來應該像這樣：

![空白應用程式頁面](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net:443/&lt;app-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

[有任何問題嗎？請告訴我們。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="browse-to-the-app"></a>瀏覽至應用程式

使用 web 瀏覽器瀏覽至已部署的應用程式。

```bash
http://<app_name>.azurewebsites.net
```

具有內建映像的 .NET Core 程式碼範例正在 Linux 上的 App Service 中執行。

![在 Azure 中執行的範例應用程式](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**恭喜！** 您已將第一個 .NET Core 應用程式部署至 Linux 上的 App Service。

[有任何問題嗎？請告訴我們。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="update-and-redeploy-the-code"></a>更新和重新部署程式碼

在本機目錄中，開啟 _Startup.cs_ 檔案。 在方法呼叫 `context.Response.WriteAsync` 中對文字進行小幅變更：

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

在 Git 中認可您的變更，然後將程式碼變更推送至 Azure。

```bash
git commit -am "updated output"
git push azure master
```

部署完成後，切換回在「瀏覽至應用程式」  步驟中開啟的瀏覽器視窗，然後按 [重新整理]。

![在 Azure 中執行的已更新範例應用程式](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[有任何問題嗎？請告訴我們。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="manage-your-new-azure-app"></a>管理新的 Azure 應用程式

移至 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>，以管理您所建立的應用程式。

按一下左側功能表中的 [應用程式服務]****，然後按一下 Azure 應用程式的名稱。

:::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list.png" alt-text="[應用程式服務] 頁面的螢幕擷取畫面，其中已選取範例 web 應用程式。":::

您會看到應用程式的 [概觀] 頁面。 您可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 

![Azure 入口網站中的 App Service 頁面](media/quickstart-dotnetcore/portal-app-overview.png)

左側功能表提供不同的頁面來設定您的應用程式。 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

[有任何問題嗎？請告訴我們。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：ASP.NET Core 應用程式搭配 SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [設定 ASP.NET Core 應用程式](configure-language-dotnetcore.md)

::: zone-end