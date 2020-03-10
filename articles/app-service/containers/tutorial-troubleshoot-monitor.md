---
title: 教學課程：使用 Azure 監視器進行疑難排解
description: 了解 Azure 監視器和 Log Analytics 如何協助您監視 App Service Web 應用程式。 Azure 監視器藉由提供可用於監視環境的全方位解決方案，將可用性最大化。
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 2/28/2020
ms.openlocfilehash: c4aee7c7e78c6799874194697fb3bc9c4aa33b38
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227990"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>教學課程：使用 Azure 監視器針對 App Service 應用程式進行疑難排解

> [!NOTE]
> Azure 監視器與 App Service 的整合目前處於[預覽](https://aka.ms/appsvcblog-azmon)狀態。
>

[Linux 上的 App Service](app-service-linux-intro.md) 使用 Linux 作業系統提供可高度擴充、自我修復的 Web 主機服務。 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)可藉由提供全方位的解決方案，以便收集、分析及處理來自雲端和內部部署環境的遙測資料，進而將應用程式與服務的可用性和效能最大化。

本教學課程說明如何使用 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)針對應用程式進行疑難排解。 範例應用程式包含用來耗盡記憶體並造成 HTTP 500 錯誤的程式碼，因此您可使用 Azure 監視器來診斷和修正問題。

完成後，將會有一個範例應用程式在 Linux 上與 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)整合的 App Service 上執行。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用 Azure 監視器設定 Web 應用程式
> * 將主控台記錄傳送至 Log Analytics
> * 使用記錄查詢來識別 Web 應用程式錯誤並進行疑難排解

您可以在 macOS、Linux、Windows 上依照本教學課程中的步驟進行。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

- [Azure 訂用帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>建立 Azure 資源

首先，在本機執行數個命令，以設定要用於本教學課程的範例應用程式。 這些命令會複製範例應用程式、建立 Azure 資源、建立部署使用者，並將應用程式部署至 Azure。 系統會提示您輸入在建立部署使用者時所提供的密碼。 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure master
```

## <a name="configure-azure-monitor-preview"></a>設定 Azure 監視器 (預覽)

### <a name="create-a-log-analytics-workspace"></a>建立 Log Analytics 工作區

您現已將範例應用程式部署至 Azure App Service，您將設定監視功能，以便在發生問題時對應用程式進行疑難排解。 Azure 監視器會將記錄資料儲存在 Log Analytics 工作區中。 工作區是包含資料和組態資訊的容器。

在此步驟中，您會建立 Log Analytics 工作區，以設定 Azure 監視器與您的應用程式。

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [針對 Azure 監視器 Log Analytics，您需支付資料擷取和資料保留費用。](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>建立診斷設定

診斷設定可用來將特定 Azure 服務的計量收集到 Azure 監視器記錄中，以便使用記錄查詢進行其他監視資料的分析。 在本教學課程中，您會啟用 Web 伺服器和標準輸出/錯誤記錄。 如需記錄類型和描述的完整清單，請參閱[支援的記錄類型](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types)。

您會執行下列命令，以建立 AppServiceConsoleLogs (標準輸出/錯誤) 和 AppServiceHTTPLogs (Web 伺服器記錄) 的診斷設定。 以您的值取代 _\<app-name>_ 和 _\<workspace-name>_ 。 

> [!NOTE]
> 前兩個命令 (`resourceID` 和 `workspaceID`) 是 `az monitor diagnostic-settings create` 命令中所要使用的變數。 如需此命令的詳細資訊，請參閱[使用 Azure CLI 建立診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-using-azure-cli)。
>

```bash

resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'

```

## <a name="troubleshoot-the-app"></a>針對應用程式進行疑難排解

瀏覽至 `http://<app-name>.azurewebsites.net`。

範例應用程式 ImageConverter 會將包含的影像從 `JPG` 轉換成 `PNG`。 本教學課程的程式碼中已故意放入錯誤 (bug)。 如果您選取足夠的影像，應用程式會在影像轉換期間產生 HTTP 500 錯誤。 想像一下，在開發階段並未考慮這種情況。 您將使用 Azure 監視器來針對錯誤進行疑難排解。

### <a name="verify-the-app-is-works"></a>確認應用程式可以運作

若要轉換影像，請按一下 `Tools` 並選取 `Convert to PNG`。

![按一下 [工具]，然後選取 [轉換成 PNG]。](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

選取前兩個影像，然後按一下 `convert`。 這會成功轉換。

![選取前兩個影像](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>中斷應用程式

既然您已藉由成功轉換兩個影像來驗證應用程式，我們會嘗試轉換前五個影像。

![轉換前五個影像](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

此動作會失敗並產生未在開發期間測試的 `HTTP 500` 錯誤。

![轉換會導致 HTTP 500 錯誤](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>使用記錄查詢來檢視 Azure 監視器記錄

讓我們看看 Log Analytics 工作區中有哪些記錄可供使用。 

按一下此 [Log Analytics 工作區連結](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces)，以存取 Azure 入口網站中的工作區。

在 Azure 入口網站中，選取 Log Analytics 工作區。

### <a name="log-queries"></a>記錄查詢

記錄查詢可協助您充分利用 Azure 監視器記錄中收集的資料值。 您可以使用記錄查詢來識別 AppServiceHTTPLogs 和 AppServiceConsoleLogs 中的記錄。 如需記錄查詢的詳細資訊，請參閱[記錄查詢概觀](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

### <a name="view-appservicehttplogs-with-log-query"></a>使用記錄查詢來檢視 AppServiceHTTPLogs

既然我們已存取應用程式，讓我們來檢視與 HTTP 要求相關聯的資料 (可在 `AppServiceHTTPLogs` 中找到)。

1. 按一下左側導覽中的 `Logs`。

![記錄 Anlytics Worksace 記錄](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. 搜尋 `appservice`，然後按兩下 `AppServiceHTTPLogs`。

![Log Analytics 工作區資料表](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. 按一下 [ `Run`]。

![Log Analytics 工作區 App Service HTTP 記錄](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

`AppServiceHTTPLogs` 查詢會傳回過去24小時內的所有要求。 `ScStatus` 資料行包含 HTTP 狀態。 若要診斷 `HTTP 500` 錯誤，請將 `ScStatus` 限制為 500 並執行查詢，如下所示：

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>使用記錄查詢來檢視 AppServiceConsoleLogs

您現已確認 HTTP 500，讓我們來看看應用程式的標準輸出/錯誤。 在 'AppServiceConsoleLogs' 中可找到這些記錄。

(1) 按一下 `+` 以建立新查詢。 

(2) 按兩下 `AppServiceConsoleLogs` 資料表，然後按一下 `Run`。 

由於轉換五個影像會導致伺服器錯誤，因此您可藉由篩選 `ResultDescription` 中的 error 來查看應用程式是否也會寫入錯誤，如下所示：

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

在 `ResultDescription` 資料行中，您會看到下列錯誤：

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>聯結 AppServiceHTTPLogs 和 AppServiceConsoleLogs

既然您已識別 HTTP 500 和標準錯誤，您必須確認這些訊息之間是否有關聯性。 接下來，您會根據時間戳記 `TimeGenerated`，將資料表聯結在一起。

> [!NOTE]
> 系統已為您備妥查詢，其會執行下列動作：
>
> - 篩選 HTTPLogs 中的 500 錯誤
> - 查詢主控台記錄
> - 根據 `TimeGenerated` 聯結資料表
>

執行下列查詢：

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

在 `ResultDescription` 資料行中，您會在 Web 伺服器發生錯誤的同時看到下列錯誤：

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

此訊息指出記憶體已在 `process.php` 的第 20 行耗盡。 您現在已確認應用程式在 HTTP 500 錯誤期間產生了一個錯誤。 讓我們看一下程式碼來找出問題。

## <a name="identify-the-error"></a>找出錯誤

在本機目錄中，開啟 `process.php` 並查看第 20 行。 

```php
imagepng($imgArray[$x], $filename);
```

第一個引數 (`$imgArray[$x]`) 是一個變數，其中保有所有需要轉換的 JPG (記憶體內部)。 不過，`imagepng` 只需要正在轉換的影像，而非所有的影像。 預先載入影像並非必要並可能會造成記憶體耗盡，因而導致 HTTP 500。 讓我們更新程式碼來視需要載入影像，以查看其否能解決問題。 接下來，您會改進程式碼以解決記憶體問題。

## <a name="fix-the-app"></a>修正應用程式

### <a name="update-locally-and-redeploy-the-code"></a>在本機更新和重新部署程式碼

您會對 `process.php` 進行下列變更，以處理記憶體耗盡：

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

在 Git 中認可您的變更，然後將程式碼變更推送至 Azure。

```bash
git commit -am "Load images on-demand in process.php"
git push azure master
```

### <a name="browse-to-the-azure-app"></a>瀏覽至 Azure 應用程式

瀏覽至 `http://<app-name>.azurewebsites.net`。 

轉換影像不應再產生 HTTP 500 錯誤。

![在 Azure App Service 中執行的 PHP 應用程式](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

使用下列命令刪除診斷設定：

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
您已了解如何︰

> [!div class="checklist"]
> * 使用 Azure 監視器設定 Web 應用程式
> * 將記錄傳送至 Log Analytics
> * 使用記錄查詢來識別 Web 應用程式錯誤並進行疑難排解

## <a name="nextsteps"></a> 後續步驟
* [使用 Azure 監視器來查詢記錄](../../azure-monitor/log-query/log-query-overview.md)
* [在 Visual Studio 中進行 Azure App Service 的疑難排解](../troubleshoot-dotnet-visual-studio.md)
* [在 HDInsight 中分析應用程式記錄](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413) \(英文\)
