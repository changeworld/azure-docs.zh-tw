---
title: 啟用診斷記錄
description: 了解如何啟用診斷記錄，並在您的應用程式中加入診斷工具，以及如何存取 Azure 所記錄的資訊。
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 89162a0b8ca20e59319802f9e2359c2f27ff163f
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962174"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>在 Azure App Service 中針對應用程式啟用診斷記錄
## <a name="overview"></a>概觀
Azure 提供內建診斷功能，可協助對 [App Service 應用程式](overview.md) \(英文\) 進行偵錯。 您會在本文中了解如何啟用診斷記錄，並在您的應用程式中加入檢測，以及如何存取 Azure 所記錄的資訊。

本文使用 [Azure 入口網站](https://portal.azure.com)和 Azure CLI 來處理診斷記錄。 如需使用 Visual Studio 來處理診斷記錄的詳細資訊，請參閱 [在 Visual Studio 中疑難排解 Azure](troubleshoot-dotnet-visual-studio.md)。

> [!NOTE]
> 除了本文中的記錄指示之外，還有新的整合式記錄功能與 Azure 監視。 您可以在將 [記錄檔傳送給 Azure 監視器 (preview) ](#send-logs-to-azure-monitor-preview) 一節中找到這項功能的詳細資訊。 
>
>

|類型|平台|Location|描述|
|-|-|-|-|
| 應用程式記錄檔 | Windows、Linux | App Service 檔案系統及/或 Azure 儲存體 blob | 記錄您的應用程式程式碼所產生的訊息。 這些訊息可由您所選擇的 web 架構，或使用您的語言標準記錄模式，直接從您的應用程式程式碼產生。 每則訊息都會被指派下列其中一個類別： **Critical**、 **Error**、 **Warning**、 **Info**、 **Debug**和 **Trace**。 您可以在啟用應用程式記錄時，藉由設定嚴重性層級，來選取您想要記錄的詳細資訊。|
| Web 服務器記錄| Windows | App Service 檔案系統或 Azure 儲存體 blob| [W3C 擴充記錄檔格式](/windows/desktop/Http/w3c-logging)的原始 HTTP 要求資料。 每個記錄訊息都包含 HTTP 方法、資源 URI、用戶端 IP、用戶端埠、使用者代理程式、回應碼等資料。 |
| 詳細的錯誤訊息| Windows | App Service 檔案系統 | 已傳送至用戶端瀏覽器之 *.htm* 錯誤頁面的複本。 基於安全性理由，不應該將詳細的錯誤頁面傳送到生產環境中的用戶端，但 App Service 可以在每次發生 HTTP 代碼400或更高的應用程式錯誤時儲存錯誤頁面。 此頁面可能包含有助於判斷伺服器傳回錯誤碼之原因的資訊。 |
| 失敗的要求追蹤 | Windows | App Service 檔案系統 | 失敗要求的詳細追蹤資訊，包括用於處理要求的 IIS 元件追蹤，以及每個元件所花費的時間。 如果您想要改善網站效能或隔離特定的 HTTP 錯誤，這會相當有用。 每個失敗的要求都會產生一個資料夾，其中包含 XML 記錄檔，以及用來查看記錄檔的 XSL 樣式表單。 |
| 部署記錄 | Windows、Linux | App Service 檔案系統 | 當您將內容發佈至應用程式時的記錄。 部署記錄會自動執行，而且沒有部署記錄的可設定設定。 它可協助您判斷部署失敗的原因。 例如，如果您使用 [自訂部署腳本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)，您可以使用部署記錄來判斷腳本失敗的原因。 |

> [!NOTE]
> App Service 提供專屬的互動式診斷工具，可協助您針對應用程式進行疑難排解。 如需詳細資訊，請參閱 [Azure App Service 診斷概觀](overview-diagnostics.md)。
>
> 此外，您可以使用其他 Azure 服務來改善應用程式的記錄和監視功能，例如 [Azure 監視器](../azure-monitor/app/azure-web-apps.md)。
>

## <a name="enable-application-logging-windows"></a>啟用 (Windows) 的應用程式記錄

> [!NOTE]
> Blob 儲存體的應用程式記錄只能在與 App Service 相同的區域中使用儲存體帳戶

若要為 [Azure 入口網站](https://portal.azure.com)中的 Windows 應用程式啟用應用程式記錄，請流覽至您的應用程式，然後選取 **App Service 記錄**檔。

針對 [**應用程式記錄] (檔案系統) **或**應用程式記錄 (Blob) **或兩者上選取 [**開啟**]。 

**Filesystem**選項適用于暫時的偵錯工具，並會在12小時內自行關閉。 **Blob**選項適用于長期記錄，並需要 blob 儲存體容器來寫入記錄。  **Blob**選項也會在記錄訊息中包含其他資訊，例如記錄訊息的原始 VM 實例識別碼 (`InstanceId`) 、執行緒識別碼 (`Tid`) ，以及更細微的時間戳記 ([`EventTickCount`](/dotnet/api/system.datetime.ticks)) 。

> [!NOTE]
> 目前只能將 .NET 應用程式記錄寫入至 Blob 儲存體。 JAVA、PHP、Node.js、Python 應用程式記錄檔只能儲存在 App Service 檔案系統 (不需要修改程式碼，即可將記錄寫入外部儲存體) 。
>
> 此外，如果您 [重新產生儲存體帳戶的存取金鑰](../storage/common/storage-account-create.md)，您必須重設各自的記錄設定，以使用更新的存取金鑰。 若要這樣做：
>
> 1. 在 [設定]**** 索引標籤上，將個別的記錄功能設定為 [關閉]****。 儲存您的設定。
> 2. 重新啟用記錄至儲存體帳戶 Blob。 儲存您的設定。
>
>

選取 **層級**，或要記錄的詳細資料層級。 下表顯示每個層級中包含的記錄類別：

| 層級 | 包含的類別 |
|-|-|
|**Disabled** | 無 |
|**錯誤** | 錯誤、嚴重 |
|**警告** | 警告、錯誤、嚴重|
|**資訊** | 資訊、警告、錯誤、嚴重|
|**詳細資訊** | 追蹤、偵錯、資訊、警告、錯誤、嚴重 (所有類別) |

完成後，選取 [儲存]。

## <a name="enable-application-logging-linuxcontainer"></a>啟用 (Linux/容器) 的應用程式記錄

若要為 [Azure 入口網站](https://portal.azure.com)中的 Linux 應用程式或自訂容器應用程式啟用應用程式記錄，請流覽至您的應用程式，然後選取 **App Service 記錄**檔。

在 [ **應用程式記錄**] 中，選取 [ **檔案系統**]。

在 [ **配額 (MB]) **中，指定應用程式記錄檔的磁片配額。 在 [ **保留期間] ([天數]) **中，設定應保留記錄的天數。

完成後，選取 [儲存]。

## <a name="enable-web-server-logging"></a>啟用 Web 伺服器記錄

若要在 [Azure 入口網站](https://portal.azure.com)中啟用 Windows 應用程式的 web 伺服器記錄，請流覽至您的應用程式，然後選取 **App Service 記錄**檔。

針對 [ **Web 服務器記錄**]，選取 [ **儲存體** ] 以將記錄儲存在 blob 儲存體上，或選取 [ **檔案系統** ] 以將記錄儲存在 App Service 檔案系統上。 

在 [ **保留期間] ([天數]) **中，設定應保留記錄的天數。

> [!NOTE]
> 如果您 [重新產生儲存體帳戶的存取金鑰](../storage/common/storage-account-create.md)，您必須重設個別的記錄組態，才能使用更新的金鑰。 若要這樣做：
>
> 1. 在 [設定]**** 索引標籤上，將個別的記錄功能設定為 [關閉]****。 儲存您的設定。
> 2. 重新啟用記錄至儲存體帳戶 Blob。 儲存您的設定。
>
>

完成後，選取 [儲存]。

## <a name="log-detailed-errors"></a>記錄詳細錯誤

若要為 [Azure 入口網站](https://portal.azure.com)中的 Windows 應用程式儲存錯誤頁面或失敗要求的追蹤，請流覽至您的應用程式，然後選取 **App Service 記錄**檔。

在 [ **詳細錯誤記錄** ] 或 [ **失敗要求追蹤**] 底下，選取 [ **開啟**]，然後選取 [ **儲存**]。

這兩種記錄類型都會儲存在 App Service 檔案系統中。 最多50個錯誤 (會保留) 的檔案/資料夾。 當 HTML 檔案數目超過50時，會自動刪除最舊的26個錯誤。

## <a name="add-log-messages-in-code"></a>在程式碼中新增記錄訊息

在您的應用程式程式碼中，您可以使用一般記錄功能將記錄檔訊息傳送至應用程式記錄檔。 例如：

- ASP.NET 應用程式會使用 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) 類別將資訊記錄到應用程式診斷記錄。 例如：

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- 根據預設，ASP.NET Core 會使用 [microsoft.extensions.logging.azureappservices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 記錄提供者。 如需詳細資訊，請參閱 [Azure 中的 ASP.NET Core 記錄](/aspnet/core/fundamentals/logging/)。

## <a name="stream-logs"></a>串流記錄

即時串流記錄之前，請先啟用您想要的記錄類型。 寫入以 .txt、.log 或 .htm 結尾之檔案的任何資訊（儲存在 */LogFiles* 目錄中， (d：/home/記錄檔) 都會由 App Service 進行串流處理。

> [!NOTE]
> 某些記錄緩衝區類型會寫入記錄檔中，進而造成串流中的事件順序錯亂。 例如，使用者造訪某個網頁所產生的應用程式記錄項目，可能會比頁面要求的對應 HTTP 記錄項目優先顯示在串流中。
>

### <a name="in-azure-portal"></a>Azure 入口網站

若要串流 [Azure 入口網站](https://portal.azure.com)中的記錄，請流覽至您的應用程式，然後選取 [ **記錄資料流程**]。 

### <a name="in-cloud-shell"></a>在 Cloud Shell

若要在 [Cloud Shell](../cloud-shell/overview.md)中即時串流記錄，請使用下列命令：

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

### <a name="in-local-terminal"></a>在本機終端機

若要在本機主控台中串流記錄，請 [安裝 Azure CLI](/cli/azure/install-azure-cli) 並登 [入您的帳戶](/cli/azure/authenticate-azure-cli)。 登入後，請遵循 [Cloud Shell 的指示。](#in-cloud-shell)

## <a name="access-log-files"></a>存取記錄檔

如果您針對記錄類型設定 Azure 儲存體 blob 選項，就需要可搭配 Azure 儲存體使用的用戶端工具。 如需詳細資訊，請參閱 [Azure 儲存體用戶端工具](../storage/common/storage-explorers.md)。

針對儲存在 App Service 檔案系統中的記錄，最簡單的方式是在瀏覽器中下載 ZIP 檔案，網址為：

- Linux/容器應用程式： `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows 應用程式： `https://<app-name>.scm.azurewebsites.net/api/dump`

針對 Linux/容器應用程式，ZIP 檔案包含 docker 主機和 docker 容器的主控台輸出記錄。 針對相應放大的應用程式，ZIP 檔案包含每個實例的一組記錄。 在 App Service 檔案系統中，這些記錄檔是 */home/LogFiles* 目錄的內容。

針對 Windows 應用程式，ZIP 檔案包含 App Service 檔案系統中 *D:\Home\LogFiles* 目錄的內容。 其結構如下：

| 記錄類型 | 目錄 | 描述 |
|-|-|-|
| **應用程式記錄** |*/LogFiles/Application/* | 包含一或多個文字檔。 記錄檔訊息的格式取決於您所使用的記錄提供者。 |
| **失敗的要求追蹤** | */LogFiles/W3SVC # # # # # # # # #/* | 包含 XML 檔案和 XSL 檔案。 您可以在瀏覽器中查看格式化的 XML 檔案。 |
| **詳細的錯誤記錄檔** | */LogFiles/DetailedErrors/* | 包含 HTM 錯誤檔。 您可以在瀏覽器中查看 HTM 檔案。<br/>另一個查看失敗要求追蹤的方法是在入口網站中流覽至您的應用程式頁面。 從左側功能表中，選取 [ **診斷並解決問題**]，然後搜尋 **失敗的要求追蹤記錄**，然後按一下圖示來流覽並查看您想要的追蹤。 |
| **Web 服務器記錄檔** | */LogFiles/HTTP/RawLogs/* | 包含使用 [W3C 擴充記錄檔格式](/windows/desktop/Http/w3c-logging)格式化的文字檔。 您可以使用文字編輯器或 [記錄](https://go.microsoft.com/fwlink/?LinkId=246619)剖析器之類的公用程式來讀取這類資訊。<br/>App Service 不支援 `s-computername` 、 `s-ip` 或 `cs-version` 欄位。 |
| **部署記錄檔** | */LogFiles/Git/* 和 */deployments/* | 包含內部部署程式所產生的記錄，以及適用于 Git 部署的記錄。 |

## <a name="send-logs-to-azure-monitor-preview"></a>將記錄傳送至 Azure 監視器 (預覽) 

您可以使用新的 [Azure 監視器整合](https://aka.ms/appsvcblog-azmon)， [ (Preview) 建立診斷設定 ](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) ，以將記錄傳送至儲存體帳戶、事件中樞和 Log Analytics。 

> [!div class="mx-imgBorder"]
> ![ (預覽) 的診斷設定 ](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>支援的記錄類型

下表顯示支援的記錄類型和描述： 

| 記錄類型 | Windows 支援 | Linux (Docker) 支援 | 描述 |
|-|-|-|
| AppServiceConsoleLogs | TBA | 是 | 標準輸出和標準錯誤 |
| AppServiceHTTPLogs | 是 | 是 | Web 伺服器記錄 |
| AppServiceEnvironmentPlatformLogs | 是 | 是 | App Service 環境：調整、設定變更和狀態記錄|
| AppServiceAuditLogs | 是 | 是 | 透過 FTP 和 Kudu 的登入活動 |
| AppServiceFileAuditLogs | 是 | TBD | 透過 FTP 和 Kudu 的檔案變更 |
| AppServiceAppLogs | TBA | JAVA SE & Tomcat | 應用程式記錄 |
| AppServiceIPSecAuditLogs  | 是 | 是 | IP 規則的要求 |
| AppServicePlatformLogs  | TBA | 是 | 容器記錄 |

## <a name="next-steps"></a><a name="nextsteps"></a> 後續步驟
* [使用 Azure 監視器來查詢記錄](../azure-monitor/log-query/log-query-overview.md)
* [如何監視 Azure App Service](web-sites-monitor.md)
* [在 Visual Studio 中進行 Azure App Service 的疑難排解](troubleshoot-dotnet-visual-studio.md)
* [在 HDInsight 中分析應用程式記錄](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413) \(英文\)