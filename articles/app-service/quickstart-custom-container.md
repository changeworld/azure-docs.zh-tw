---
title: 快速入門：在 App Service 上執行自訂容器
description: 藉由部署您的第一個自訂容器，開始在 Azure App Service 上使用容器。
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 4c95e345255b28ba43e474087cdb80fcab493394
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356412"
---
# <a name="run-a-custom-container-in-azure"></a>在 Azure 中執行自訂容器

::: zone pivot="container-windows"
[Azure App Service](overview.md) 會在 Windows 上提供預先定義的應用程式堆疊 (例如 ASP.NET 或 Node.js)，執行於 IIS 上。 預先設定的 Windows 容器 (預覽) 環境會鎖定作業系統的系統管理存取、軟體安裝、對全域組件快取的變更等作業。 如需詳細資訊，請參閱 [Azure App Service 上的作業系統功能](operating-system-functionality.md)。 如果您的應用程式需要的存取超出預先設定的環境所允許的，您可以改為部署自訂 Windows 容器。

此快速入門說明如何將 Windows 映像中的 ASP.NET 應用程式從 Visual Studio 部署到至 [Docker Hub](https://hub.docker.com/)。 您可以在 Azure App Service 中於自訂容器內執行應用程式。

> [!NOTE]
> Windows 容器上的 App Service 處於預覽狀態。
>

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

- <a href="https://hub.docker.com/" target="_blank">註冊 Docker Hub 帳戶</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">安裝適用於 Windows 的 Docker</a>。
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">切換 Docker 以執行 Windows 容器</a>。
- <a href="https://www.visualstudio.com/downloads/" target="_blank">安裝 Visual Studio 2019</a>，記得包含 **ASP.NET 與網頁程式開發**與 **Azure 開發**工作負載。 若您已安裝 Visual Studio 2019：

    - 選取 [說明] > [檢查更新] 以安裝最新的 Visual Studio 更新。
    - 選取 [工具] > [取得工具與功能] 以在 Visual Studio 中新增工作負載。

## <a name="create-an-aspnet-web-app"></a>建立 ASP.NET Web 應用程式

請遵循下列步驟來建立 ASP.NET Web 應用程式：

1. 開啟 Visual Studio，然後選取 [建立新專案]。

1. 在 [建立新專案] 中，針對 C# 尋找並選擇 [ASP.NET Web 應用程式 (.NET Framework)]，然後選取 [下一步]。

1. 在 [設定新專案] 中，將應用程式命名為 myfirstazurewebapp，然後選取 [建立]。

   ![設定 Web 應用程式專案](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. 您可以將任何類型的 ASP.NET web 應用程式部署至 Azure。 在本快速入門中，選擇 [MVC] 範本。

1. 選取 [Docker 支援]，並確定驗證已設定為 [不需要驗證]。 選取 [建立]。

   ![建立 ASP.NET Web 應用程式](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. 如果 _Dockerfile_ 檔案沒有自動開啟，請從 [方案總管] 開啟它。

1. 您需要[支援的父映像](configure-custom-container.md#supported-parent-images)。 請使用下列程式碼取代 `FROM` 這一行以變更父映像，然後儲存檔案：

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. 從 Visual Studio 功能表中，選取 **[偵錯]**  >  **[啟動但不偵錯]** 以在本機執行 Web 應用程式。

   ![在本機執行應用程式](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>發佈至 Docker Hub

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **myfirstazurewebapp** 專案，然後選取 [發佈]。

1. 選擇 [App Service]，然後選取 [發佈]。

1. 在 [挑選發佈目標] 中，選取 [Container Registry] 和 [Docker Hub]，然後按一下 [發佈]。

   ![從專案概觀頁面發佈](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. 提供 Docker Hub 帳戶認證並選取 [儲存]。

   等待部署完成。 [發佈] 頁面現在會顯示可供稍後使用的存放庫名稱。

   ![從專案概觀頁面發佈](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. 複製此存放庫名稱以便稍後使用。

## <a name="create-a-windows-container-app"></a>建立 Windows 容器應用程式

1. 登入 [Azure 入口網站]( https://portal.azure.com)。

1. 選擇 Azure 入口網站左上角的 [建立資源]。

1. 在 Azure Marketplace 資源清單上方的搜尋方塊中，搜尋 [用於容器的 Web App]，然後選取 [建立]。

1. 在 [Web 應用程式建立] 中，選擇您的訂用帳戶和 [資源群組]。 如有需要，您可以建立新的資源群組。

1. 提供應用程式名稱 (例如 win-container-demo)，然後選擇 [Windows] 作為 [作業系統]。 完成時，選取 [下一步:Docker] 以繼續。

   ![建立用於容器的 Web App](media/quickstart-custom-container/create-web-app-continer.png)

1. 針對 [映像來源] 選擇 [Docker Hub]，然後針對 [映像和標籤] 輸入您在[發佈至 Docker Hub](#publish-to-docker-hub) 中複製的存放庫名稱。

   ![設定用於容器的 Web App](media/quickstart-custom-container/configure-web-app-continer.png)

    如果您在他處有用於 Web 應用程式的自訂映像 (例如，在 [Azure Container Registry](../container-registry/index.yml) 中或任何其他私人存放庫中)，您可以在此處加以設定。

1. 選取 [檢閱並建立]，然後 [建立] 並等候 Azure 建立所需的資源。

## <a name="browse-to-the-container-app"></a>瀏覽至容器應用程式

Azure 作業完成時，會顯示通知方塊。

![部署成功](media/quickstart-custom-container/portal-create-finished.png)

1. 按一下 [前往資源]。

1. 在此資源的概觀中，遵循 [URL] 旁的連結。

新的瀏覽器頁面隨即開啟，並顯示下列頁面：

![Windows 容器應用程式啟動中](media/quickstart-custom-container/app-starting.png)

在幾分鐘後再試一次，直到您看到預設的 ASP.NET 首頁：

![Windows 容器應用程式執行中](media/quickstart-custom-container/app-running-vs.png)

**恭喜！** 您正在 Azure App Service 中執行您的第一個自訂 Windows 容器。

## <a name="see-container-start-up-logs"></a>檢視容器啟動記錄

Windows 容器載入可能需要一些時間。 若要查看進度，請將 *\<app_name>* 取代為您的應用程式名稱，以瀏覽至下列 URL。
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

串流處理的記錄會如下所示：

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>在本機更新和重新部署

1. 在 Visual Studio 的 [方案總管] 中，開啟 [檢視] > [主資料夾] > [Index.cshtml]。

1. 尋找頂端附近的 `<div class="jumbotron">` HTML 標籤，並以下列程式碼取代整個元素︰

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. 若要重新部署至 Azure，請在 [方案總管] 中，以滑鼠右鍵按一下 **myfirstazurewebapp** 專案，然後選擇 [發佈]。

1. 在 [發佈] 頁面上，選取 [發佈] 並等候發佈完成。

1. 若要告知 App Service 從 Docker Hub 提取新映像，請重新啟動應用程式。 返回入口網站的應用程式頁面，按一下 [重新啟動] > [是]。

   ![在 Azure 中重新啟動 Web 應用程式](./media/quickstart-custom-container/portal-restart-app.png)

再次[瀏覽至容器應用程式](#browse-to-the-container-app)。 當您重新整理網頁時，應用程式應該會先還原成 [啟動] 頁面，接著在幾分鐘後再次顯示更新的網頁。

![在 Azure 中更新 Web 應用程式](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 Azure 中移轉至 Windows 容器](tutorial-custom-container.md)

或者，查看其他資源：

> [!div class="nextstepaction"]
> [設定自訂容器](configure-custom-container.md)

::: zone-end  

::: zone pivot="container-linux"
Linux 上的 App Service 提供 Linux 上的預先定義應用程式堆疊，且支援 .NET、PHP、Node.js 等其他語言。 您也可以使用自訂 Docker 映像，在尚未於 Azure 中定義的應用程式堆疊上執行 Web 應用程式。 本快速入門說明如何將映像從 [Azure Container Registry](../container-registry/index.yml) (ACR) 部署至 App Service。

## <a name="prerequisites"></a>必要條件

* [Azure 帳戶](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* [適用於 VS Code 的 Azure App Service 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。 您也可以使用此擴充功能在 Azure 平台即服務 (PaaS) 上建立、管理和部署 Linux Web Apps。
* [適用於 VS Code 的 Docker 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)。 您可以使用此擴充功能來簡化本機 Docker 映像和命令的管理，以及將組建應用程式映像部署至 Azure。

## <a name="create-an-image"></a>建立映像

若要完成本快速入門，您將需要一個儲存在 [Azure Container Registry](../container-registry/index.yml) 中的適當 Web 應用程式映像。 遵循[快速入門：使用 Azure 入口網站建立私人容器登錄](../container-registry/container-registry-get-started-portal.md)中的指示，但使用 `mcr.microsoft.com/azuredocs/go` 映像，而不是 `hello-world` 映像。 如需參考，[在 Azure 範例存放庫中可找到範例 Dockerfile](https://github.com/Azure-Samples/go-docs-hello-world)。

> [!IMPORTANT]
> 當您建立容器登錄時，請務必將 [管理使用者]**** 選項設定為 [啟用]****。 您也可以在 Azure 入口網站中，從登錄頁面的 [存取金鑰]**** 區段擷取它。 這是 App Service 存取的必要設定。

## <a name="sign-in"></a>登入

接下來，啟動 VS Code 並使用 App Service 擴充功能登入您的 Azure 帳戶。 若要這麼做，請在活動列中選取 Azure 標誌，瀏覽至 [APP SERVICE]**** 總管，然後選取 [Sign in to Azure]**** \(登入 Azure\)，並遵循指示操作。

![登入 Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>檢查必要條件

現在您可以檢查是否已正確安裝及設定所有必要條件。

在 VS Code 中，您應該會在狀態列中看到您的 Azure 電子郵件地址，並在 [APP SERVICE]**** 總管中看到您的訂用帳戶。

接下來，確認您已安裝且正在執行 Docker。 下列命令會顯示 Docker 版本 (如果它正在執行中)。

```bash
docker --version
```

最後，請確定您的 Azure Container Registry 已連線。 若要這麼做，請在活動列中選取 Docker 標誌，然後瀏覽至 [REGISTRIES]**** \(登錄\)。

![此螢幕擷取畫面顯示已擴充 Azure 的登錄值，以及具有 .io 副檔名的檔案。](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>將映像部署到 Azure App Service

設定好所有項目之後，您就可以直接從 Docker 擴充功能總管將您的映像部署到 [Azure App Service](https://azure.microsoft.com/services/app-service/)。

在 **DOCKER** 總管的 [Registries]**** \(登錄\) 節點底下尋找映像，並展開它以顯示其標籤。 以滑鼠右鍵按一下標籤，然後選取 [Deploy Image to Azure App Service]**** \(將映像部署至 Azure App Service\)。

從這裡，遵循提示來選擇訂用帳戶、全域唯一應用程式名稱、資源群組和 App Service 方案。 針對定價層和區域選擇 [B1 Basic]**** \(B1 基本\)。

部署之後，您的應用程式可以在 `http://<app name>.azurewebsites.net` 取得。

**資源群組**是 Azure 中所有應用程式資源的具名集合。 例如，資源群組可以包含網站、資料庫和 Azure 函式的參考。

**App Service 方案**定義用來裝載您網站的實體資源。 本快速入門使用 **Linux** 基礎結構上的**基本**主控方案，這表示網站會與其他網站一起裝載在 Linux 機器上。 如果您開始使用**基本**方案，可以使用 Azure 入口網站來相應增加，讓您的網站是在機器上執行的唯一網站。

## <a name="browse-the-website"></a>瀏覽網站

[輸出]**** 面板會在部署期間開啟，以指出作業的狀態。 當作業完成時，尋找您在 [APP SERVICE]**** 總管中建立的應用程式，並以滑鼠右鍵按一下，然後選取 [Browse Website]**** \(瀏覽網站\)，以在瀏覽器中開啟網站。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>後續步驟

恭喜，您已成功完成本快速入門！

接下來，請參閱其他 Azure 擴充功能。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLI 工具](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) \(英文\)

或藉由安裝 [Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 擴充功能套件來取得以上所有擴充功能。

查看其他資源：

> [!div class="nextstepaction"]
> [設定自訂容器](configure-custom-container.md)

::: zone-end