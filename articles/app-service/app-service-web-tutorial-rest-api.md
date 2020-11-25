---
title: 教學課程：裝載具有 CORS 的 RESTful API
description: 了解 Azure App Service 如何協助您裝載具有 CORS 支援的 RESTful API。 App Service 可裝載前端 Web 應用程式和後端 API。
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/28/2020
ms.custom: devx-track-csharp, mvc, devcenter, seo-javascript-september2019, seo-javascript-october2019, seodec18
ms.openlocfilehash: 9481b6d2740d27b8c3d1309e205edda6017868fa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005739"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>教學課程：在 Azure App Service 中裝載具有 CORS 支援的 RESTful API

[Azure App Service](overview.md) 可提供可高度擴充、自我修復的 Web 主控服務。 此外，App Service 有 RESTful API 的內建[跨原始資源共用 (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) 支援。 本教學課程示範如何將 ASP.NET Core API 應用程式部署至具有 CORS 支援的 App Service。 您可使用命令列工具來設定應用程式，以及使用 Git 部署應用程式。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用 Azure CLI 建立 App Service 資源
> * 使用 Git 將 RESTful API 部署到 Azure
> * 啟用 App Service CORS 支援

您可以在 macOS、Linux、Windows 上依照本教學課程中的步驟進行。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程：

* <a href="https://git-scm.com/" target="_blank">安裝 Git</a>
 * <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">安裝最新的 .NET Core 3.1 SDK</a>

## <a name="create-local-aspnet-core-app"></a>建立本機 ASP.NET Core 應用程式

在此步驟中，您要設定本機 ASP.NET Core 專案。 App Service 會對以其他語言撰寫的 API 支援相同的工作流程。

### <a name="clone-the-sample-application"></a>複製範例應用程式

在終端機視窗中，使用 `cd` 移至工作目錄。  

執行下列命令來複製範例存放庫。 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

此存放庫包含根據下列教學課程建立的應用程式：[使用 Swagger 的 ASP.NET Core Web API 說明頁面](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio)。 它會使用 Swagger 產生器來提供 [Swagger UI](https://swagger.io/swagger-ui/)和 Swagger JSON 端點。

### <a name="run-the-application"></a>執行應用程式

執行下列命令以安裝必要的套件、執行資料庫移轉，然後啟動應用程式。

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

在瀏覽器中瀏覽至 `http://localhost:5000/swagger` 以使用 Swagger UI 播放。

![在本機執行的 ASP.NET Core API](./media/app-service-web-tutorial-rest-api/azure-app-service-local-swagger-ui.png)

瀏覽至 `http://localhost:5000/api/todo` 並查看 ToDo JSON 項目清單。

瀏覽至 `http://localhost:5000` 並使用瀏覽器應用程式播放。 稍後，您會將瀏覽器應用程式指向 App Service 中的遠端 API，以測試 CORS 功能。 瀏覽器應用程式的程式碼位於存放庫的 wwwroot  目錄中。

如需隨時停止 ASP.NET Core，請在終端機上按下 `Ctrl+C`。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>將應用程式部署到 Azure

在此步驟中，您會將與 SQL Database 連線的 .NET Core 應用程式部署至 App Service。

### <a name="configure-local-git-deployment"></a>設定本機 git 部署

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>建立 Web 應用程式

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 83, done.
Counting objects: 100% (83/83), done.
Delta compression using up to 8 threads
Compressing objects: 100% (78/78), done.
Writing objects: 100% (83/83), 22.15 KiB | 3.69 MiB/s, done.
Total 83 (delta 26), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '509236e13d'.
remote: Generating deployment script.
remote: Project file path: .\TodoApi.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
To https://&lt;app_name&gt;.scm.azurewebsites.net/&lt;app_name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>瀏覽至 Azure 應用程式

在瀏覽器中瀏覽至 `http://<app_name>.azurewebsites.net/swagger` 並使用 Swagger UI 播放。

![在 Azure App Service 中執行的 ASP.NET Core API](./media/app-service-web-tutorial-rest-api/azure-app-service-browse-app.png)

瀏覽至 `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` 以查看您已部署 API 的 swagger.json  。

瀏覽至 `http://<app_name>.azurewebsites.net/api/todo` 以查看您已部署的 API 是否能運作。

## <a name="add-cors-functionality"></a>新增 CORS 功能

接著，您會在 App Service 中為 API 啟用內建 CORS 支援。

### <a name="test-cors-in-sample-app"></a>在範例應用程式中測試 CORS

在您的本機存放庫中，開啟 wwwroot/index.html  。

在第 51 行中，將 `apiEndpoint` 變數設定為已部署 API 的 URL (`http://<app_name>.azurewebsites.net`)。 以您在 App Service 中的應用程式名稱取代 _\<appname>_ 。

在您的本機終端機視窗中，再次執行範例應用程式。

```bash
dotnet run
```

瀏覽至位於 `http://localhost:5000` 的瀏覽器應用程式。 在瀏覽器中開啟 [開發人員工具] 視窗 (在適用於 Windows 的 Chrome 中按 `Ctrl`+`Shift`+`i`)，並檢查 [主控台]  索引標籤。您現在會看到錯誤訊息：`No 'Access-Control-Allow-Origin' header is present on the requested resource`。

![瀏覽器用戶端中的 CORS 錯誤](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-error.png)

因為瀏覽器應用程式 (`http://localhost:5000`) 與遠端資源 (`http://<app_name>.azurewebsites.net`) 之間的網域不相符，加上 App Service 中您的 API 並未傳送 `Access-Control-Allow-Origin` 標頭的這個事實，以致瀏覽器無法將跨網域內容載入瀏覽器應用程式中。

在生產環境中，瀏覽器應用程式會有公用 URL (而非 localhost URL)，但是對 localhost URL 啟用 CORS 的方式與公用 URL 相同。

### <a name="enable-cors"></a>啟用 CORS 

在 Cloud Shell 中，使用 [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) 命令，對您的用戶端 URL 啟用 CORS。 取代 _&lt;app-name>_ 預留位置。

```azurecli-interactive
az webapp cors add --resource-group myResourceGroup --name <app-name> --allowed-origins 'http://localhost:5000'
```

您可以在 `properties.cors.allowedOrigins` (`"['URL1','URL2',...]"`) 中設定多個用戶端 URL。 您也可以使用 `"['*']"` 來啟用所有的用戶端 URL。

> [!NOTE]
> 如果您的應用程式需要傳送認證，例如 Cookie 或驗證權杖，則瀏覽器可能會在回應上要求 `ACCESS-CONTROL-ALLOW-CREDENTIALS` 標頭。 若要在 App Service 中啟用此作業，請在 CORS 組態中將 `properties.cors.supportCredentials` 設定為 `true`。當 `allowedOrigins` 包含 `'*'` 時，無法啟用此作業。

### <a name="test-cors-again"></a>再次測試 CORS

重新整理位於 `http://localhost:5000` 的瀏覽器應用程式。 [主控台]  視窗中的錯誤訊息現已消失，而且您可看到來自已部署 API 的資料並與其互動。 您的遠端 API 現在對在本機執行的瀏覽器應用程式支援 CORS。 

![瀏覽器用戶端中的 CORS 成功](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-success.png)

恭喜，您正在 Azure App Service 中執行具有 CORS 支援的 API。

## <a name="app-service-cors-vs-your-cors"></a>App Service CORS 與您的 CORS

您可使用自己的 CORS 公用程式，而不是使用 App Service CORS，以獲得更大彈性。 例如，您可以針對不同的路由或方法，指定不同的允許來源。 因為 App Service CORS 可讓您針對所有的 API 路由和方法指定一組可接受的來源，所以您可使用自己的 CORS 程式碼。 在[啟用跨源要求 (CORS)](/aspnet/core/security/cors)中了解 ASP.NET Core 如何執行此作業。

> [!NOTE]
> 請勿嘗試同時使用 App Service CORS 與您自己的 CORS 程式碼。 一起使用時，App Service CORS 具有優先權，而您自己的 CORS 程式碼沒有任何作用。
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>後續步驟

您已了解如何︰

> [!div class="checklist"]
> * 使用 Azure CLI 建立 App Service 資源
> * 使用 Git 將 RESTful API 部署到 Azure
> * 啟用 App Service CORS 支援

移至下一個教學課程，以了解如何為使用者進行驗證和授權。

> [!div class="nextstepaction"]
> [教學課程：端對端驗證和授權使用者](tutorial-auth-aad.md)
