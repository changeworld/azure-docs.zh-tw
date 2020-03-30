---
title: 啟用診斷記錄
description: 了解如何啟用診斷記錄，並在您的應用程式中加入診斷工具，以及如何存取 Azure 所記錄的資訊。
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: 433f8fa36f17f7cb145261273586a684658acda5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280465"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>在 Azure App Service 中針對應用程式啟用診斷記錄
## <a name="overview"></a>總覽
Azure 提供內建診斷功能，可協助對 [App Service 應用程式](overview.md) \(英文\) 進行偵錯。 您會在本文中了解如何啟用診斷記錄，並在您的應用程式中加入檢測，以及如何存取 Azure 所記錄的資訊。

本文使用 [Azure 入口網站](https://portal.azure.com)和 Azure CLI 來處理診斷記錄。 如需使用 Visual Studio 來處理診斷記錄的詳細資訊，請參閱 [在 Visual Studio 中疑難排解 Azure](troubleshoot-dotnet-visual-studio.md)。

> [!NOTE]
> 除了本文中的日誌記錄說明外，還有新的 Azure 監視集成日誌記錄功能。 您可以在"[將日誌發送到 Azure 監視器（預覽）"](#send-logs-to-azure-monitor-preview)部分中找到有關此功能的更多資訊。 
>
>

|類型|Platform|Location|描述|
|-|-|-|-|
| 應用程式記錄檔 | Windows、Linux | 應用服務檔案系統和/或 Azure 存儲 Blob | 記錄應用程式代碼生成的消息。 消息可以由您選擇的 Web 框架生成，也可以直接從應用程式代碼中使用語言的標準日誌記錄模式生成。 每條消息被分配為以下類別之一：**嚴重**、**錯誤**、**警告**、**資訊**、**調試**和**跟蹤**。 通過在啟用應用程式日誌記錄時設置嚴重性級別，可以選擇希望日誌記錄的詳細程度。|
| Web 服務器日誌記錄| Windows | 應用服務檔案系統或 Azure 存儲 Blob| 原始 HTTP 要求[W3C 擴展日誌檔案格式的資料](/windows/desktop/Http/w3c-logging)。 每個日誌消息包括 HTTP 方法、資源 URI、用戶端 IP、用戶端埠、使用者代理、回應代碼等資料。 |
| 詳細的錯誤訊息| Windows | 應用服務檔案系統 | 將發送到用戶端瀏覽器的 *.htm*錯誤頁的副本。 出於安全原因，不應將詳細的錯誤頁發送到生產中的用戶端，但每次發生具有 HTTP 代碼 400 或更高的應用程式錯誤時，App Service 都可以保存錯誤頁。 該頁可能包含有助於確定伺服器返回錯誤代碼的原因的資訊。 |
| 失敗的要求追蹤 | Windows | 應用服務檔案系統 | 有關失敗請求的詳細跟蹤資訊，包括用於處理請求的 IIS 元件的跟蹤以及每個元件所佔用的時間。 如果您想要改善網站效能或隔離特定的 HTTP 錯誤，這會相當有用。 為每個失敗的請求生成一個資料夾，其中包含 XML 日誌檔和用於查看日誌檔的 XSL 樣式表。 |
| 部署日誌記錄 | Windows、Linux | 應用服務檔案系統 | 在將內容發佈到應用時登錄。 部署日誌記錄會自動發生，並且沒有可配置的部署日誌記錄設置。 它可以説明您確定部署失敗的原因。 例如，如果使用[自訂部署腳本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)，則可以使用部署日誌記錄來確定腳本失敗的原因。 |

> [!NOTE]
> 應用服務提供專用的互動式診斷工具，可説明您解決應用程式故障。 有關詳細資訊，請參閱[Azure 應用服務診斷概述](overview-diagnostics.md)。
>
> 此外，可以使用其他 Azure 服務來改進應用的日誌記錄和監視功能，例如[Azure 監視器](../azure-monitor/app/azure-web-apps.md)。
>

## <a name="enable-application-logging-windows"></a>啟用應用程式日誌記錄（Windows）

要在[Azure 門戶](https://portal.azure.com)中為 Windows 應用啟用應用程式日誌記錄，請導航到應用並選擇**應用服務日誌**。

為**應用程式日誌記錄（檔案系統）** 或**應用程式日誌記錄（Blob）** 或兩者選擇 **"打開**"。 

**檔案系統**選項用於臨時調試，並在 12 小時內自行關閉。 **Blob**選項用於長期日誌記錄，需要 Blob 存儲容器來寫入日誌。  **Blob**選項還包括日誌消息中的其他資訊，例如日誌消息 （`InstanceId`）、執行緒 ID （ ） 和更精細的時間戳記`Tid`（）[`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)的原源 VM 實例的 ID 。

> [!NOTE]
> 目前只能將 .NET 應用程式記錄寫入至 Blob 儲存體。 JAVA、PHP、Node.js、Python 應用程式日誌只能存儲在應用程式服務檔案系統上（無需修改代碼即可將日誌寫入外部存儲）。
>
> 此外，如果[重新生成存儲帳戶的訪問金鑰](../storage/common/storage-create-storage-account.md)，則必須重置相應的日誌記錄配置才能使用更新的訪問金鑰。 作法：
>
> 1. 在 [設定]**** 索引標籤上，將個別的記錄功能設定為 [關閉]****。 儲存您的設定。
> 2. 重新啟用記錄至儲存體帳戶 Blob。 儲存您的設定。
>
>

選擇要記錄**的級別**或詳細資訊級別。 下表顯示了每個級別中包含的日誌類別：

| 層級 | 包括類別 |
|-|-|
|**禁用** | None |
|**錯誤** | 錯誤、嚴重 |
|**警告** | 警告、錯誤、嚴重|
|**資訊** | 資訊、警告、錯誤、嚴重|
|**詳細** | 追蹤、偵錯、資訊、警告、錯誤、嚴重 (所有類別) |

完成後，選擇 **"保存**"。

## <a name="enable-application-logging-linuxcontainer"></a>啟用應用程式日誌記錄（Linux/容器）

要在[Azure 門戶](https://portal.azure.com)中為 Linux 應用或自訂容器應用啟用應用程式日誌記錄，請導航到應用並選擇**應用服務日誌**。

在**應用程式日誌記錄**中，選擇**檔案系統**。

在**配額 （MB）** 中，指定應用程式日誌的磁片配額。 在**保留期（天）** 中，設置日誌應保留的天數。

完成後，選擇 **"保存**"。

## <a name="enable-web-server-logging"></a>啟用 Web 伺服器記錄

要在[Azure 門戶](https://portal.azure.com)中為 Windows 應用啟用 Web 服務器日誌記錄，請導航到你的應用並選擇**應用服務日誌**。

對於**Web 服務器日誌記錄**，選擇 **"存儲"** 以在 blob 存儲上存儲日誌，或選擇**檔案系統**以在應用服務檔案系統上存儲日誌。 

在**保留期（天）** 中，設置日誌應保留的天數。

> [!NOTE]
> 如果您 [重新產生儲存體帳戶的存取金鑰](../storage/common/storage-create-storage-account.md)，您必須重設個別的記錄組態，才能使用更新的金鑰。 作法：
>
> 1. 在 [設定]**** 索引標籤上，將個別的記錄功能設定為 [關閉]****。 儲存您的設定。
> 2. 重新啟用記錄至儲存體帳戶 Blob。 儲存您的設定。
>
>

完成後，選擇 **"保存**"。

## <a name="log-detailed-errors"></a>記錄詳細錯誤

要在[Azure 門戶](https://portal.azure.com)中保存 Windows 應用的錯誤頁或失敗的請求跟蹤，請導航到應用並選擇**應用服務日誌**。

在 **"詳細錯誤日誌記錄**"或 **"失敗的請求跟蹤**"下，選擇"**打開**"，然後選擇"**保存**"。

這兩種類型的日誌都存儲在應用服務檔案系統中。 最多保留 50 個錯誤（檔/資料夾）。 當 HTML 檔案數超過 50 時，最舊的 26 個錯誤將自動刪除。

## <a name="add-log-messages-in-code"></a>在代碼中添加日誌消息

在應用程式代碼中，使用通常的日誌記錄工具將日誌消息發送到應用程式日誌。 例如：

- ASP.NET 應用程式會使用 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) 類別將資訊記錄到應用程式診斷記錄。 例如：

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- 預設情況下，ASP.NET核心使用[Microsoft.擴展.日誌記錄.AzureApp 服務](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices)日誌記錄提供程式。 如需詳細資訊，請參閱 [Azure 中的 ASP.NET Core 記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging/)。

## <a name="stream-logs"></a>串流記錄

在即時資料流日誌之前，請啟用所需的日誌類型。 寫入以 .txt、.log 或 .htm 結尾的檔的任何資訊都存儲在 */LogFiles*目錄 （d：/home/日誌檔） 中，由應用服務進行資料流。

> [!NOTE]
> 某些記錄緩衝區類型會寫入記錄檔中，進而造成串流中的事件順序錯亂。 例如，使用者造訪某個網頁所產生的應用程式記錄項目，可能會比頁面要求的對應 HTTP 記錄項目優先顯示在串流中。
>

### <a name="in-azure-portal"></a>Azure 入口網站

要在 Azure[門戶](https://portal.azure.com)中資料流日誌，請導航到你的應用並選擇 **"日誌流**"。 

### <a name="in-cloud-shell"></a>在雲殼中

要在[雲殼](../cloud-shell/overview.md)中即時資料流日誌，請使用以下命令：

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

若要篩選特定事件，例如錯誤，請使用 **--Filter** 參數。 例如：

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
若要篩選特定記錄類型，例如 HTTP，請使用 **--Path** 參數。 例如：

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>在本地終端

要在本地主控台中資料流日誌，[請安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)並[登錄到您的帳戶](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)。 登錄後，按照[雲外殼的說明操作](#in-cloud-shell)

## <a name="access-log-files"></a>存取記錄檔

如果為日誌類型配置 Azure 存儲 Blob 選項，則需要一個與 Azure 存儲一起工作的用戶端工具。 有關詳細資訊，請參閱[Azure 存儲用戶端工具](../storage/common/storage-explorers.md)。

對於存儲在應用服務檔案系統中的日誌，最簡單的方法是在瀏覽器中下載 ZIP 檔，網址為：

- Linux/容器應用程式：`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- 視窗應用：`https://<app-name>.scm.azurewebsites.net/api/dump`

對於 Linux/容器應用，ZIP 檔包含 Docker 主機和 Docker 容器的主控台輸出日誌。 對於橫向擴展應用，ZIP 檔包含每個實例的一組日誌。 在應用服務檔案系統中，這些日誌檔是 */home/LogFiles*目錄的內容。

對於 Windows 應用，ZIP 檔包含應用服務檔案系統中的*D：\Home_LogFiles*目錄的內容。 其結構如下：

| 記錄類型 | 目錄 | 描述 |
|-|-|-|
| **應用程式日誌** |*/日誌檔/應用程式/* | 包含一個或多個文字檔。 日誌消息的格式取決於您使用的日誌記錄提供程式。 |
| **失敗的請求跟蹤** | */日誌檔/W3SVC_____/* | 包含 XML 檔和 XSL 檔。 您可以在瀏覽器中查看格式化的 XML 檔。 |
| **詳細的錯誤日誌** | */日誌檔/詳細錯誤/* | 包含 HTM 錯誤檔。 您可以在瀏覽器中查看 HTM 檔。<br/>查看失敗的請求跟蹤的另一種方法是在門戶中導航到應用頁面。 從左側功能表中，選擇 **"診斷並解決問題**"，然後搜索 **"失敗的請求跟蹤日誌**"，然後按一下該圖示以流覽和查看所需的跟蹤。 |
| **Web 服務器日誌** | */日誌檔/HTTP/原始日誌/* | 包含使用[W3C 擴展日誌檔案格式](/windows/desktop/Http/w3c-logging)格式化的文字檔。 此資訊可以使用文字編輯器或實用程式（如[日誌解析器](https://go.microsoft.com/fwlink/?LinkId=246619)）讀取。<br/>應用服務不支援`s-computername`，`s-ip`或`cs-version`欄位。 |
| **部署日誌** | */日誌檔/Git/* 和 */部署/* | 包含內部部署過程生成的日誌以及 Git 部署的日誌。 |

## <a name="send-logs-to-azure-monitor-preview"></a>將日誌發送到 Azure 監視器（預覽）

使用新的[Azure 監視器集成](https://aka.ms/appsvcblog-azmon)，您可以創建[診斷設置（預覽）](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting)以將日誌發送到存儲帳戶、事件中心和日誌分析。 

> [!div class="mx-imgBorder"]
> ![診斷設置（預覽版）](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>支援的日誌類型

下表顯示了支援的日誌類型和說明： 

| 記錄類型 | Windows 支援 | Linux（Docker）支援 | 描述 |
|-|-|-|
| AppServiceConsoleLogs | TBA | 是 | 標準輸出和標準誤差 |
| 應用服務HTTPLogs | 是 | 是 | Web 伺服器記錄 |
| 應用服務環境平臺日誌 | 是 | 是 | 應用服務環境：縮放、配置更改和狀態日誌|
| 應用服務稽核記錄 | 是 | 是 | 通過 FTP 和庫杜登錄活動 |
| 應用服務檔稽核記錄 | TBA | 是 | 通過 FTP 和庫杜的檔更改 |
| AppServiceAppLogs | TBA | JAVA SE & 湯姆卡特 | 應用程式記錄 |

## <a name="next-steps"></a><a name="nextsteps"></a> 後續步驟
* [使用 Azure 監視器查詢日誌](../azure-monitor/log-query/log-query-overview.md)
* [如何監視 Azure App Service](web-sites-monitor.md)
* [在 Visual Studio 中進行 Azure App Service 的疑難排解](troubleshoot-dotnet-visual-studio.md)
* [在 HDInsight 中分析應用程式記錄](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413) \(英文\)
