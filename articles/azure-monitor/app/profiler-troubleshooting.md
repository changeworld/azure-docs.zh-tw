---
title: 針對 Azure 應用程式 Insights Profiler 的問題進行疑難排解
description: 本文提供疑難排解步驟和資訊，以協助開發人員啟用和使用 Application Insights Profiler。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 47a452377c8fed9808957f45fcc4ec686fcef87d
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561030"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>針對啟用或檢視 Application Insights Profiler 的問題進行疑難排解

> [!CAUTION]
> 在 Azure App Service 上執行 ASP.NET Core 應用程式的 profiler 時發生錯誤。 我們有一個修正程式，但需要幾周的時間來部署 world wide。 您可以使用 [此處](./asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio)的指示，將 Application Insights SDK 新增至應用程式，以解決此錯誤。

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>一般疑難排解

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>如果在 Profiler 執行時有對應用程式提出的要求，才會上傳設定檔

Azure 應用程式 Insights Profiler 會每小時收集兩分鐘的資料。 當您在 [**設定 Application Insights Profiler** ] 窗格中選取 [**立即設定檔**] 按鈕時，也可以收集資料。

> [!NOTE]
> 只有當分析資料可以附加至 Profiler 執行時所發生的要求時，才會上傳程式碼剖析資料。 

Profiler 會將追蹤訊息和自訂事件寫入至您的 Application Insights 資源。 您可以使用這些事件來查看 Profiler 的執行方式：

1. 搜尋 Profiler 傳送給 Application Insights 資源的追蹤訊息和自訂事件。 您可以使用此搜尋字串來尋找相關資料：

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    下圖顯示從兩個 AI 資源進行搜尋的兩個範例： 
    
   * 在左側，應用程式在 Profiler 執行時並未接收要求。 訊息指出上傳已因沒有活動而取消。 

   * 在右側，Profiler 已啟動，並在偵測到於 Profiler 執行期間有要求提出時傳送自訂事件。 如果 `ServiceProfilerSample` 顯示自訂事件，則表示已在 **Application Insights [效能** ] 窗格中捕捉到設定檔，且其可供使用。

     如果沒有顯示任何記錄，Profiler 就不會執行。 若要進行疑難排解，請參閱本文後續與您的應用程式類型有關的疑難排解章節。  

     ![搜尋 Profiler 遙測資料][profiler-search-telemetry]

### <a name="other-things-to-check"></a>需要檢查的其他事項
* 確定應用程式執行於 .NET Framework 4.6 上。
* 如果 Web 應用程式是 ASP.NET Core 應用程式，則必須至少執行 ASP.NET Core 2.0。
* 如果您嘗試檢視的資料已存在好幾週，請試著限縮時間篩選條件，然後再試一次。 追蹤會在七天後刪除。
* 請確定 proxy 或防火牆未封鎖存取 https://gateway.azureserviceprofiler.net 。
* 免費或共用的 app service 方案不支援 Profiler。 如果您使用的是其中一個方案，請嘗試相應增加至其中一個基本方案，分析工具應該會開始運作。

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>平行執行緒重複計算

在某些情況下，堆疊檢視器中的總時間計量會比要求的持續時間還長。

當有兩個或多個平行線程與要求相關聯時，就可能發生這種情況。 在此情況下，執行緒的總時間就會超出經過時間。

一個執行緒可能會等候另一個執行緒完成。 檢視器會嘗試偵測這種情況，並省略不重要的等候。 此時，它會寧可顯示過多資訊，以免省略可能具重要性的資訊。

當您在追蹤中看到平行線程時，請判斷正在等候的執行緒，讓您可以識別要求的最忙碌路徑。

通常，快速進入等候狀態的執行緒就只是在等候其他執行緒。 請專注在其他執行緒上，並忽略等候中執行緒的時間。

### <a name="error-report-in-the-profile-viewer"></a>設定檔檢視器中的錯誤報表
您可以在入口網站中提交支援票證。 請務必包含錯誤訊息中的相互關聯識別碼。

## <a name="troubleshoot-profiler-on-azure-app-service"></a>對 Azure App Service 上的 Profiler 進行疑難排解
若要讓 Profiler 正常運作：
* Web 應用程式服務方案必須至少是「基本」層。
* 您的 Web 應用程式必須已啟用 Application Insights。
* 您的 web 應用程式必須具有下列應用程式設定：

    |應用程式設定    | 值    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | 適用於您 Application Insights 資源的 iKey    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* **ApplicationInsightsProfiler3** WebJob 必須正在執行。 若要檢查 WebJob：
   1. 移至 [Kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret)。
   1. 在 [工具] 功能表中，選取 [WebJob 儀表板]。  
      [WebJob] 窗格隨即開啟。 
   
      ![螢幕擷取畫面顯示 [Webjob] 窗格，其中顯示工作的名稱、狀態和上次執行時間。][profiler-webjob]   
   
   1. 若要查看 webjob 的詳細資料，包括記錄檔，請選取 [ **ApplicationInsightsProfiler3** ] 連結。  
     [連續 WebJob 詳細資料] 窗格隨即開啟。

      ![螢幕擷取畫面：顯示 [連續 WebJob 詳細資料] 窗格。][profiler-webjob-log]

如果 Profiler 無法正常運作，您可以下載記錄，並將它傳送給我們的小組以取得協助 serviceprofilerhelp@microsoft.com 。

### <a name="check-the-diagnostic-services-site-extension-status-page"></a>檢查診斷服務網站延伸模組的狀態頁面
如果已透過入口網站中的 [ [Application Insights] 窗格](profiler.md) 啟用 Profiler，則是由診斷服務網站延伸模組啟用。

您可以前往下列 url 來檢查此延伸模組的 [狀態] 頁面： `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> 狀態頁面連結的網域會依雲端而有所不同。
此網域將與 App Service 的 Kudu 管理網站相同。

此狀態頁面會顯示 Profiler 和 Snapshot Collector 代理程式的安裝狀態。 如果發生未預期的錯誤，則會顯示並顯示如何修正此問題。

您可以使用 App Service 的 Kudu 管理網站取得此狀態頁面的基底 url：
1. 在 Azure 入口網站中開啟您的 App Service 應用程式。
2. 選取 [ **Advanced Tools**] 或 [搜尋 **Kudu**]。
3. 選取 [執行]。
4. 當您在 Kudu 管理網站上時，請在 URL 中 **附加下列各項， `/DiagnosticServices` 然後按 enter 鍵**。
 它會以下面的方式結束： `https://<kudu-url>/DiagnosticServices`

它會顯示類似如下的狀態頁面： [ ![ 診斷服務狀態] 頁面](./media/diagnostic-services-site-extension/status-page.png)
    
### <a name="manual-installation"></a>手動安裝

當您設定 Profiler 時，系統會對 Web 應用程式的設定進行更新。 您可以視環境需要手動套用這些更新。 可能的範例為您的應用程式在 PowerApps 的 Web Apps 環境中執行。 若要手動套用更新：

1. 在 [ **Web 應用** 程式控制] 窗格中，開啟 [ **設定**]。

1. 將 **.NET Framework 版本** 設定為 **4.6**。

1. 將 [一律開啟] 設定為 [開啟]。
1. 建立這些應用程式設定：

    |應用程式設定    | 值    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | 適用於您 Application Insights 資源的 iKey    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>太多個使用中分析工作階段

您最多可以在相同服務方案中執行的四個 Web Apps 上啟用 Profiler。 如果您有四個以上的，Profiler 可能會擲回 *ServiceProfiler。 TooManyETWSessionException*。 若要解決此問題，請將一些 web 應用程式移至不同的服務方案。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>部署錯誤：目錄尚有資料 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

如果您要將 Web 應用程式重新部署到已啟用 Profiler 的 Web Apps 資源，您可能會看到下列訊息：

*目錄不是空的：主要 \\ \\ 網站 \\ wwwroot \\ App_Data \\ 作業」*

如果您從腳本或從 Azure Pipelines 執行 Web Deploy，就會發生這個錯誤。 解決方法是將下列額外的部署參數新增至 Web Deploy 工作：

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

這些參數會刪除 Application Insights Profiler 所使用的資料夾，並將重新部署程序解除封鎖。 它們並不會影響目前正在執行的 Profiler 執行個體。

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>如何判斷 Application Insights Profiler 是否有在執行？

Profiler 會在 Web 應用程式中以連續 WebJob 的形式執行。 您可以在 [Azure 入口網站](https://portal.azure.com)中開啟 web 應用程式資源。 在 [WebJobs] 窗格中，檢查 **ApplicationInsightsProfiler** 的狀態。 如果它並沒有在執行，請開啟 [記錄] 以取得詳細資訊。

## <a name="troubleshoot-vms-and-cloud-services"></a>針對 Vm 和雲端服務進行疑難排解

>**已修正在 WAD for 雲端服務中隨附的程式碼剖析工具中的 bug。** 適用于雲端服務的最新版 WAD (1.12.2.0) 適用于所有最近的 App Insights SDK 版本。 雲端服務主機會自動升級 WAD，但不會立即升級。 若要強制進行升級，您可以重新部署服務或重新開機節點。

若要查看 Azure 診斷是否已正確設定 Profiler，請遵循下列步驟： 
1. 確認部署的 Azure 診斷設定內容是您預期的。 

1. 其次，請確定 Azure 診斷在 Profiler 命令列上傳遞了正確的 iKey。 

1. 最後，請檢查 Profiler 記錄檔，以確認 Profiler 是否已執行但傳回錯誤。 

若要檢查用來設定 Azure 診斷的設定：

1. 登入 (VM) 的虛擬機器，然後在此位置開啟記錄檔。 您電腦上的外掛程式版本可能較新。
    
    針對 Vm：
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    對於雲端服務：
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. 在該檔案中，您可以搜尋 **WadCfg** 字串，以找出傳至 VM 用以設定 Azure 診斷的設定。 您可以確認 Profiler 接收所使用的 iKey 是否正確。

1. 檢查用來啟動 Profiler 的命令列。 用來啟動 Profiler 的引數位於下列檔案中。  (磁片磁碟機可能是 c：或 d：而且可能隱藏目錄。 ) 

    針對 Vm：
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    針對雲端服務：
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. 確定 Profiler 命令列上的 iKey 正確無誤。 

1. 使用上述 *config.js* 檔案中找到的路徑，檢查分析工具記錄檔，稱為 **BootstrapN .log**。 其中會顯示偵錯資訊，指出 Profiler 所使用的設定。 此外也會顯示來自 Profiler 的狀態和錯誤訊息。  

    針對 Vm，檔案如下：
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    對於雲端服務：
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    如果在您的應用程式接收要求時，Profiler 正在執行，則會顯示下列訊息： *從 iKey 偵測到活動*。 

    上傳追蹤時，會顯示下列訊息： *開始上傳追蹤*。 


## <a name="edit-network-proxy-or-firewall-rules"></a>編輯網路 Proxy 或防火牆規則

如果您的應用程式透過 proxy 或防火牆連接到網際網路，您可能需要更新規則，以與分析工具服務進行通訊。

Application Insights Profiler 所使用的 Ip 會包含在 Azure 監視器服務標記中。 如需詳細資訊，請參閱 [服務標記檔](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)。


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png
