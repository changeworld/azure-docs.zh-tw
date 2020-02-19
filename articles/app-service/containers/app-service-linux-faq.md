---
title: 執行內建容器常見問題
description: 尋找 Azure App Service 中內建 Linux 容器的常見問題解答。
keywords: azure app service, web 應用程式, 常見問題集, linux, oss, 用於容器的 Web App, 多重容器
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c590a27f61c1a555ae30828332e4140a6116f95f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443851"
---
# <a name="azure-app-service-on-linux-faq"></a>Linux 上的 Azure App Service 常見問題集

隨著 Linux 上的 App Service 推出，我們正著手在我們的平台上新增功能和進行改善。 本文章提供我們的客戶最近向我們詢問之問題的回答。

如果您有問題，請在這篇文章留言。

## <a name="built-in-images"></a>內建映像

**我想要將平臺所提供的內建 Docker 容器派生。哪裡可以找到這些檔案？**

您可以在 [GitHub](https://github.com/azure-app-service) 上找到所有 Docker 檔案。 您可以在 [Docker Hub](https://hub.docker.com/u/appsvc/) 上找到所有 Docker 容器。

<a id="#startup-file"></a>

**設定執行階段堆疊時，在 [啟動檔案] 區段應該使用哪些值？**

| Stack           | 預期的值                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | 用來啟動 JAR 應用程式的命令（例如，`java -jar /home/site/wwwroot/app.jar --server.port=80`） |
| Tomcat          | 執行任何必要設定的腳本位置（例如，`/home/site/deployments/tools/startup_script.sh`）          |
| Node.js         | PM2 設定檔或您的腳本檔案                                |
| .Net Core       | 已編譯的 DLL 名稱，做為 `dotnet <myapp>.dll`                                 |
| Ruby            | 您想要用來初始化應用程式的 Ruby 腳本                     |

這些命令或腳本會在內建的 Docker 容器啟動後，但在您的應用程式程式碼啟動之前執行。

## <a name="management"></a>管理性

**當我按下 Azure 入口網站中的 [重新啟動] 按鈕時，會發生什麼事？**

這個動作與 Docker 重新啟動相同。

**我是否可以使用「安全殼層」(SSH) 來連線到應用程式容器虛擬機器 (VM)？**

是的，您可以透過原始檔控制管理 (SCM) 網站執行該動作。

> [!NOTE]
> 您也可以使用 SSH、SFTP 或 Visual Studio Code (適用於 Node.js 應用程式即時偵錯)，直接從本機開發電腦連線到應用程式容器。 如需詳細資訊，請參閱 [Linux 上 App Service 中的遠端偵錯和 SSH](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html)。
>

**我如何透過 SDK 或 Azure Resource Manager 範本建立 Linux App Service 方案？**

您應該將 App Service 的 **reserved** 欄位設定為 true。

## <a name="continuous-integration-and-deployment"></a>持續整合及部署

**我的 web 應用程式在我已更新 Docker Hub 上的映射之後，仍會使用舊的 Docker 容器映射。您是否支援自訂容器的持續整合和部署？**

是，若要設定 Azure Container Registry 或 DockerHub 的持續整合/部署，請依照[使用用於容器的 Web 應用程式進行持續部署](./app-service-linux-ci-cd.md)操作。 針對私人登錄，您可以將 Web 應用程式先停止再啟動，來重新整理容器。 您也可以變更或新增虛擬應用程式設定，來強制重新整理您的容器。

**是否支援預備環境？**

是。

**我可以使用 *WebDeploy/MSDeploy* 來部署我的 Web 應用程式嗎？**

是，您需要將稱為 `WEBSITE_WEBDEPLOY_USE_SCM` 的應用程式設定設定為 *false*。

**當您使用 Linux web 應用程式時，應用程式的 Git 部署失敗。如何解決此問題？**

如果您的 Linux Web 應用程式無法部署 Git，請選擇下列其中一個選項來部署應用程式程式碼：

- 使用持續傳遞（預覽）功能：您可以將應用程式的原始程式碼儲存在 Azure DevOps 的 Git 存放庫或 GitHub 存放庫中，以使用 Azure 持續傳遞。 如需詳細資訊，請參閱[如何設定 Linux Web 應用程式的持續傳遞](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/)。

- 使用 [ZIP 部署 API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)：若要使用此應用程式開發介面，[SSH 到您的 Web 應用程式](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support)，然後移至您要部署程式碼的資料夾。 執行下列程式碼：

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   如果您收到錯誤：找不到 `curl` 命令，請確定您在執行先前的 `apt-get install curl` 命令之前，使用 `curl` 來安裝 curl。

## <a name="language-support"></a>語言支援

**我想要在我的 Node.js 應用程式中使用 websocket，是否需要設定任何特殊設定或組態？**

是，在您的伺服器端 Node.js 程式碼中停用 `perMessageDeflate`。 例如，如果您使用 socket.io，請使用下列程式碼：

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**是否支援未編譯的 .NET Core 應用程式？**

是。

**您是否支援以 Composer 做為 PHP 應用程式的相依性管理程式？**

是，在 Git 部署期間，Kudu 應該會偵測到您要部署 PHP 應用程式 (這點受惠於 composer.lock 檔案)，然後 Kudu 會觸發編輯器安裝。

## <a name="custom-containers"></a>自訂容器

**我使用自己的自訂容器。我希望平臺將 SMB 共用掛接到 `/home/` 目錄。**

如果**未指定**`WEBSITES_ENABLE_APP_SERVICE_STORAGE` 設定或將其設定為*true*，`/home/` 目錄將會跨級別實例**共用**，而且寫入的檔案會在重新開機**時保存**。 將 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 明確設定為*false*將會停用掛接。

**我的自訂容器需要很長時間才能啟動，而平台會在它完成啟動之前將容器重新啟動。**

您可以設定在重新啟動容器之前，平台所要等待的時間量。 若要這樣做，請將 `WEBSITES_CONTAINER_START_TIME_LIMIT` 應用程式設定設定成您要的值。 預設值為 230 秒，而最大值為 1800 秒。

**私人登錄伺服器 URL 的格式為何？**

提供包括 `http://` 或 `https://` 的完整登錄 URL。

**私人登錄選項中的映像名稱格式為何？**

新增包括私人登錄 URL 的完整映像名稱 (例如 myacr.azurecr.io/dotnet:latest)。 使用自訂連接埠的映像名稱[無法透過入口網站輸入](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650)。 若要設定 `docker-custom-image-name`，請使用 [`az` 命令列工具](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set)。

**我可以在我的自訂容器映像上公開多個連接埠嗎？**

我們不支援公開一個以上的埠。

**我可以攜帶自己的儲存體嗎？**

是，[自備儲存體](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage)處於預覽狀態。

**為什麼我無法從 SCM 網站瀏覽自訂容器的檔案系統或執行中處理序？**

SCM 網站是在個別的容器中執行。 您無法檢查應用程式容器的檔案系統或執行中的處理程序。

**我的自訂容器會接聽埠80以外的埠。如何設定我的應用程式將要求路由至該埠？**

我們有自動連接埠偵測。 您也可以指定一個叫做 *WEBSITES_PORT* 的應用程式設定，為它提供一個預期的連接埠號碼值。 之前，平台是使用 *PORT* 應用程式設定。 我們計劃淘汰此應用程式設定，並專門使用 *WEBSITES_PORT*。

**我是否需要在我的自訂容器中實作 HTTPS？**

不需要，平台會在共用的前端處理 HTTPS 終止。

## <a name="multi-container-with-docker-compose"></a>具有 Docker Compose 的多容器

**如何設定 Azure 容器登錄 (ACR) 與多重容器一起使用？**

為了將 ACR 與多重容器一起使用，**所有容器映像**都必須裝載在相同的 ACR 登錄伺服器上。 一旦它們位於相同的登錄伺服器上，您將需要建立應用程式設定，然後更新 Docker Compose 的設定檔，以包含 ACR 映射名稱。

建立下列應用程式設定：

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL （完整 URL，例如： `https://<server-name>.azurecr.io`）
- DOCKER_REGISTRY_SERVER_PASSWORD (啟用 ACR 設定中的系統管理員存取權)

在組態檔中，參考您的 ACR 映像，如下列範例所示：

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**我如何知道哪些容器可存取網際網路？**

- 只有一個容器可以開啟以供存取
- 只有連接埠 80 和 8080 可以存取 (公開連接埠)

以下是判斷哪些容器可存取的規則 - 依優先順序排列：

- 應用程式設定 `WEBSITES_WEB_CONTAINER_NAME` 設為容器名稱
- 第一個容器定義連接埠 80 或 8080
- 如果上述任一項皆不為 true，那麼檔案中定義的第一個容器將是可存取的 (公開)

## <a name="pricing-and-sla"></a>價格和 SLA

**既然服務已正式推出，服務的定價為何？**

我們會以標準 Azure App Service 定價，向您收取應用程式執行時數的費用。

## <a name="other-questions"></a>其他問題

**應用程式設定名稱所支援的字元為何？**

對於應用程式設定，您僅可以使用字母 (A-Z、a-z)、數字 (0-9) 和底線字元 (_)。

**我可以在何處提出新功能的要求？**

您可以在 [Web Apps 意見反應論壇](https://aka.ms/webapps-uservoice) (英文) 提交您的想法。 將 "[Linux]" 新增至您想法的標題。

## <a name="next-steps"></a>後續步驟

- [何謂 Linux 上的 Azure App Service？](app-service-linux-intro.md)
- [在 Azure App Service 中設定預備環境](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [使用用於容器的 Web 應用程式進行持續部署](./app-service-linux-ci-cd.md)
