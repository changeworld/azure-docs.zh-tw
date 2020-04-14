---
title: 啟用診斷記錄
description: 了解如何啟用診斷記錄，並在您的應用程式中加入診斷工具，以及如何存取 Azure 所記錄的資訊。
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: 6f22d5b2140e42f5f4b8ef5787d22b4be399c7e8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272520"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>在 Azure App Service 中針對應用程式啟用診斷記錄
## <a name="overview"></a>概觀
Azure 提供內建診斷功能，可協助對 [App Service 應用程式](overview.md) \(英文\) 進行偵錯。 您會在本文中了解如何啟用診斷記錄，並在您的應用程式中加入檢測，以及如何存取 Azure 所記錄的資訊。

本文使用 [Azure 入口網站](https://portal.azure.com)和 Azure CLI 來處理診斷記錄。 如需使用 Visual Studio 來處理診斷記錄的詳細資訊，請參閱 [在 Visual Studio 中疑難排解 Azure](troubleshoot-dotnet-visual-studio.md)。

> [!NOTE]
> 除了本文中的日誌記錄說明外,還有新的 Azure 監視集成日誌記錄功能。 您可以在「[將日誌傳送到 Azure 監視器(預覽)」](#send-logs-to-azure-monitor-preview)部分中找到有關此功能的更多資訊。 
>
>

|類型|平台|Location|描述|
|-|-|-|-|
| 應用程式記錄檔 | Windows、Linux | 套用服務檔案系統與/或 Azure 儲存 Blob | 記錄應用程式代碼生成的消息。 消息可以由您選擇的 Web 框架生成,也可以直接從應用程式代碼中使用語言的標準日誌記錄模式生成。 每條訊息被配置為以下類別之一:**嚴重**、**錯誤**,**警告**,**資訊**,**除錯**與**追蹤**。 通過在啟用應用程式日誌記錄時設置嚴重性級別,可以選擇希望日誌記錄的詳細程度。|
| Web 伺服器記錄| Windows | 套用服務檔案系統或 Azure 儲存 Blob| 原始 HTTP 要求[W3C 延伸紀錄檔格式的資料](/windows/desktop/Http/w3c-logging)。 每個日誌訊息包括 HTTP 方法、資源 URI、用戶端 IP、用戶端埠、使用者代理、回應代碼等數據。 |
| 詳細的錯誤訊息| Windows | 套用服務檔案系統 | 將發送到客戶端瀏覽器的 *.htm*錯誤頁的複本。 出於安全原因,不應將詳細的錯誤頁發送到生產中的用戶端,但每次發生具有 HTTP 代碼 400 或更高的應用程式錯誤時,App Service 都可以保存錯誤頁。 該頁可能包含有助於確定伺服器返回錯誤代碼的原因的資訊。 |
| 失敗的要求追蹤 | Windows | 套用服務檔案系統 | 有關失敗請求的詳細跟蹤資訊,包括用於處理請求的IIS元件的追蹤以及每個元件所佔用的時間。 如果您想要改善網站效能或隔離特定的 HTTP 錯誤，這會相當有用。 為每個失敗的請求生成一個資料夾,其中包含 XML 日誌檔和用於查看日誌檔的 XSL 樣式表。 |
| 部署記錄記錄 | Windows、Linux | 套用服務檔案系統 | 在將內容發佈到應用時登錄。 部署日誌記錄會自動發生,並且沒有可配置的部署日誌記錄設置。 它可以説明您確定部署失敗的原因。 例如,如果使用[自定義部署腳本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script),則可以使用部署日誌記錄來確定腳本失敗的原因。 |

> [!NOTE]
> 應用服務提供專用的互動式診斷工具,可説明您解決應用程式故障。 有關詳細資訊,請參閱[Azure 應用服務診斷概述](overview-diagnostics.md)。
>
> 此外,可以使用其他 Azure 服務來改進應用的紀錄記錄和監視功能,例如[Azure 監視器](../azure-monitor/app/azure-web-apps.md)。
>

## <a name="enable-application-logging-windows"></a>開啟應用程式紀錄記錄(Windows)

在[Azure 門戶](https://portal.azure.com)中為 Windows 應用程式啟用應用程式紀錄記錄,請瀏覽到應用程式並選擇**套用服務紀錄**。

為**應用程式記錄(檔案系統)** 或**應用程式記錄(Blob)** 或兩者選擇 **「打開**」。 

**檔系統**選項用於臨時調試,並在 12 小時內自行關閉。 **Blob**選項用於長期日誌記錄,需要 Blob 儲存容器來寫入日誌。  **Blob**選項還包括紀錄訊息中的其他資訊,例如紀錄訊息 (`InstanceId`)、執行緒代碼 ( ) 與更`Tid`精細[`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)的時間戳 () 的原來源 VM 實體的識別碼 。

> [!NOTE]
> 目前只能將 .NET 應用程式記錄寫入至 Blob 儲存體。 Java、PHP、Node.js、Python 應用程式日誌只能存儲在應用程式服務檔案系統上(無需修改代碼即可將日誌寫入外部存儲)。
>
> 此外,如果[重新生成存儲帳戶的訪問密鑰](../storage/common/storage-create-storage-account.md),則必須重置相應的日誌記錄配置才能使用更新的訪問密鑰。 作法：
>
> 1. 在 [設定]**** 索引標籤上，將個別的記錄功能設定為 [關閉]****。 儲存您的設定。
> 2. 重新啟用記錄至儲存體帳戶 Blob。 儲存您的設定。
>
>

選擇要記錄**的級別**或詳細資訊級別。 下表顯示每個等級中包含的紀錄類別:

| 層級 | 包括類別 |
|-|-|
|**已停用** | None |
|**錯誤** | 錯誤、嚴重 |
|**警告** | 警告、錯誤、嚴重|
|**資訊** | 資訊、警告、錯誤、嚴重|
|**詳細資訊** | 追蹤、偵錯、資訊、警告、錯誤、嚴重 (所有類別) |

完成後,選擇 **"保存**"。

## <a name="enable-application-logging-linuxcontainer"></a>開啟應用程式紀錄記錄(Linux/容器)

在[Azure 門戶](https://portal.azure.com)中為 Linux 應用程式或自訂容器應用程式啟用應用程式紀錄記錄,請瀏覽到應用程式並選擇**套用服務日誌**。

在**應用程式紀錄紀錄**中,選擇**檔案系統**。

在**配額 (MB)** 中,指定應用程式日誌的磁碟配額。 在**保留期(天)** 中,設置日誌應保留的天數。

完成後,選擇 **"保存**"。

## <a name="enable-web-server-logging"></a>啟用 Web 伺服器記錄

在[Azure 門戶](https://portal.azure.com)中為 Windows 應用程式啟用 Web 伺服器紀錄記錄,請瀏覽到你的應用程式並選擇**套用服務紀錄**。

對於**Web 伺服器紀錄記錄**,選擇 **「儲存」** 以在 blob 儲存儲存紀錄,或選擇**檔案系統**以在應用服務檔案系統上儲存紀錄。 

在**保留期(天)** 中,設置日誌應保留的天數。

> [!NOTE]
> 如果您 [重新產生儲存體帳戶的存取金鑰](../storage/common/storage-create-storage-account.md)，您必須重設個別的記錄組態，才能使用更新的金鑰。 作法：
>
> 1. 在 [設定]**** 索引標籤上，將個別的記錄功能設定為 [關閉]****。 儲存您的設定。
> 2. 重新啟用記錄至儲存體帳戶 Blob。 儲存您的設定。
>
>

完成後,選擇 **"保存**"。

## <a name="log-detailed-errors"></a>記錄詳細錯誤

在[Azure 門戶](https://portal.azure.com)中儲存 Windows 應用程式的錯誤頁或失敗的請求追蹤,請瀏覽到套用並選擇**套用服務紀錄**。

在 **「詳細錯誤紀錄紀錄**」 或 **「失敗的請求追蹤**」下,選擇「**打開**」,然後選擇「**儲存**」 。

這兩種類型的日誌都存儲在應用服務文件系統中。 最多保留 50 個錯誤(檔案/ 資料夾)。 當 HTML 檔案數超過 50 時,最舊的 26 個錯誤將自動刪除。

## <a name="add-log-messages-in-code"></a>新增紀錄訊息

在應用程式代碼中,使用通常的日誌記錄工具將日誌訊息發送到應用程式日誌。 例如：

- ASP.NET 應用程式會使用 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) 類別將資訊記錄到應用程式診斷記錄。 例如：

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- 默認情況下,ASP.NET核心使用[Microsoft.擴展.日誌記錄.AzureApp 服務](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices)日誌記錄提供程式。 如需詳細資訊，請參閱 [Azure 中的 ASP.NET Core 記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging/)。

## <a name="stream-logs"></a>串流記錄

在即時流式傳輸日誌之前,請啟用所需的日誌類型。 寫入以 .txt、.log 或 .htm 結尾的檔的任何資訊都存儲在 */LogFiles*目錄 (d:/home/日誌檔) 中,由應用服務進行流式傳輸。

> [!NOTE]
> 某些記錄緩衝區類型會寫入記錄檔中，進而造成串流中的事件順序錯亂。 例如，使用者造訪某個網頁所產生的應用程式記錄項目，可能會比頁面要求的對應 HTTP 記錄項目優先顯示在串流中。
>

### <a name="in-azure-portal"></a>Azure 入口網站

要在 Azure[門戶](https://portal.azure.com)中流式傳輸日誌,請導航到你的應用並選擇 **「日誌流**」。 

### <a name="in-cloud-shell"></a>在雲殼中

要在[雲殼](../cloud-shell/overview.md)中即時流式傳輸日誌,請使用以下命令:

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

### <a name="in-local-terminal"></a>在本地端終端機

在本地控制台中流式傳輸紀錄,[請安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)並[登入您的帳戶](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)。 登錄后,按照[雲外殼的說明操作](#in-cloud-shell)

## <a name="access-log-files"></a>存取記錄檔

如果為日誌類型配置 Azure 儲存 Blob 選項,則需要一個與 Azure 儲存一起工作的用戶端工具。 有關詳細資訊,請參閱[Azure 儲存用戶端工具](../storage/common/storage-explorers.md)。

對於儲存在應用服務檔案系統中的紀錄,最簡單的方法是在瀏覽器中下載 ZIP 檔案,網址為:

- Linux/容器應用程式:`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- 視窗應用:`https://<app-name>.scm.azurewebsites.net/api/dump`

對於 Linux/容器應用,ZIP 檔包含 Docker 主機和 Docker 容器的控制台輸出日誌。 對於橫向擴展應用,ZIP 檔包含每個實例的一組日誌。 在應用服務檔案系統中,這些日誌檔是 */home/LogFiles*目錄的內容。

對於 Windows 應用,ZIP 檔包含應用服務檔案系統中的*D:\Home_LogFiles*目錄的內容。 其結構如下：

| 記錄類型 | 目錄 | 描述 |
|-|-|-|
| **應用程式記錄** |*/紀錄檔/應用程式/* | 包含一個或多個文字檔。 紀錄訊息的格式取決於您使用的紀錄記錄提供者。 |
| **失敗的要求追蹤** | */日誌檔/W3SVC_____/* | 包含 XML 檔和 XSL 檔。 您可以在瀏覽器中查看格式化的 XML 檔。 |
| **詳細的錯誤紀錄** | */紀錄檔/詳細錯誤/* | 包含 HTM 錯誤檔。 您可以在瀏覽器中查看 HTM 檔。<br/>查看失敗的請求跟蹤的另一種方法是在門戶中導航到應用頁面。 從左側功能表中,選擇 **「診斷並解決問題**」,然後搜尋 **「失敗的請求跟蹤日誌**」,然後單擊該圖示以瀏覽和查看所需的跟蹤。 |
| **Web 伺服器記錄** | */紀錄檔/HTTP/原始紀錄/* | 包含使用[W3C 延伸紀錄檔格式](/windows/desktop/Http/w3c-logging)格式化的文本檔。 此資訊可以使用文字編輯器或實用程式(如[日誌解析器](https://go.microsoft.com/fwlink/?LinkId=246619))讀取。<br/>應用服務不支援`s-computername``s-ip`,`cs-version`或 欄位。 |
| **部署紀錄** | */紀錄檔/Git/* 和 */部署/* | 包含內部部署過程生成的日誌以及 Git 部署的日誌。 |

## <a name="send-logs-to-azure-monitor-preview"></a>將日誌傳送到 Azure 監視器(預覽)

使用新的[Azure 監視器整合](https://aka.ms/appsvcblog-azmon),您可以建立[診斷設定(預覽)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting)以將日誌發送到儲存帳戶、事件中心和日誌分析。 

> [!div class="mx-imgBorder"]
> ![診斷設定 (預覽版)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>支援的紀錄類型

下表顯示支援的紀錄類型和說明: 

| 記錄類型 | Windows 支援 | Linux(Docker)支援 | 描述 |
|-|-|-|
| AppServiceConsoleLogs | TBA | 是 | 標準輸出和標準誤差 |
| 套用服務HTTPLogs | 是 | 是 | Web 伺服器記錄 |
| 套用服務環境平台日誌 | 是 | 是 | 套用服務環境:縮放、設定變更和狀態紀錄|
| 套用服務稽核紀錄 | 是 | 是 | 透過 FTP 和函式庫杜登入活動 |
| 套用服務檔案稽核紀錄 | 是 | TBD | 透過 FTP 和函式庫杜的檔案變更 |
| AppServiceAppLogs | TBA | Java SE & 湯姆卡特 | 應用程式記錄 |

## <a name="next-steps"></a><a name="nextsteps"></a> 後續步驟
* [使用 Azure 監視器查詢紀錄](../azure-monitor/log-query/log-query-overview.md)
* [如何監視 Azure App Service](web-sites-monitor.md)
* [在 Visual Studio 中進行 Azure App Service 的疑難排解](troubleshoot-dotnet-visual-studio.md)
* [在 HDInsight 中分析應用程式記錄](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413) \(英文\)
