---
title: 使用 Azure Application Insights 監視即時 ASP.NET Web 應用程式 | Microsoft Docs
description: 監視網站的效能而不重新部署網站。 使用裝載於內部部署或 VM 中的 ASP.NET Web 應用程式。
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: 6c27c78bf8e3f3b8af342a14a38c9be3821d781a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91758658"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>使用 Application Insights 無程式碼附加在執行時間檢測 web 應用程式

> [!IMPORTANT]
> 不再建議使用狀態監視器， **從6月 2021 1 日起** ，將不支援此版本的狀態監視器。 它已取代為先前名為狀態監視器 v2) 的 Azure 監視器 Application Insights 代理程式 (。 請參閱我們的 [內部部署伺服器部署](./status-monitor-v2-overview.md) 或 [Azure 虛擬機器和虛擬機器擴展集部署](./azure-vm-vmss-apps.md)的檔。

您可以使用 Azure Application Insights 檢測即時 Web 應用程式，而不需修改或重新部署您的程式碼。 您需要 [Microsoft Azure](https://azure.com) 訂用帳戶。

狀態監視器用於檢測 IIS 內部或 VM 中裝載的 .NET 應用程式。

- 如果您的應用程式已部署至 Azure VM 或 Azure 虛擬機器擴展集，請遵循下列 [指示](azure-vm-vmss-apps.md)。
- 如果您的應用程式已部署到 Azure 應用程式服務中，請遵循[這些指示](azure-web-apps.md)。
- 如果您的應用程式部署在 Azure VM 中，您可以從 Azure 控制台切換為 Application Insights 監視。
-  (也有不同的檢測 [Azure 雲端服務](./cloudservices.md)相關文章。 ) 


![App Insights 概觀圖表的螢幕擷取畫面，包含失敗的要求、伺服器回應時間和伺服器要求的相關資訊](./media/monitor-performance-live-website-now/overview-graphs.png)

下列兩種途徑可讓您將 Application Insights 套用至 .NET Web 應用程式：

* **建置階段：** [新增 Application Insights SDK][greenbrown] 至您的 Web 應用程式程式碼。
* **執行階段︰** 如下所述，檢測伺服器上的 Web 應用程式，而不需重建並重新部署程式碼。

> [!NOTE]
> 如果您使用組建時間檢測，即使開啟，執行時間檢測仍無法運作。

以下是您可在每種途徑中取得的優勢摘要︰

|  | 建置階段 | 執行階段 |
| --- | --- | --- |
| **要求 & 例外狀況** |是 |是 |
| **[更詳細的例外狀況](./asp-net-exceptions.md)** | |是 |
| **[相依性診斷](./asp-net-dependencies.md)** |在 .Net 4.6 + 上，但較少細節 |是，完整詳細資料︰結果碼、SQL 命令文字、HTTP 指令動詞|
| **[系統效能計數器](./performance-counters.md)** |是 |是 |
| **[自訂遙測的 API][api]** |是 |否 |
| **[追蹤記錄整合](./asp-net-trace-logs.md)** |是 |否 |
| **[頁面流覽 & 使用者資料](./javascript.md)** |是 |否 |
| **需要重新建置程式碼** |是 | 否 |



## <a name="monitor-a-live-iis-web-app"></a>監視即時 IIS Web 應用程式

如果您的應用程式裝載於 IIS 伺服器上，請使用狀態監視器來啟用 Application Insights。

1. 在 IIS Web 伺服器上，以系統管理員認證登入。
2. 如果尚未安裝 Application Insights 狀態監視器，請[下載並執行安裝程式](#download)
3. 在狀態監視器中，選取您想要監視的已安裝 Web 應用程式或網站。 利用您的 Azure 認證登入。

    在 Application Insights 入口網站中設定您要在其中檢視結果的資源。 (通常最好建立新的資源。 如果您已經為此應用程式設定 [Web 測試][availability]或[用戶端監視][client]，請選取現有的資源。) 

    ![選擇應用程式和資源。](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. 重新啟動 IIS。

    ![選擇對話方塊頂端的 [重新啟動]。](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    您的 Web 服務會中斷一小段時間。

## <a name="customize-monitoring-options"></a>自訂監視選項

啟用 Application Insights 可將 DLL 和 ApplicationInsights.config 新增至您的 Web 應用程式。 您可以[編輯 .config 檔案](./configuration-with-applicationinsights-config.md)﹐以變更某些選項。

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>當您重新發佈您的應用程式時，請重新啟用 Application Insights。

重新發佈您的應用程式之前，請考慮[將 Application Insights 新增至 Visual Studio 中的程式碼][greenbrown]。 您將取得更詳細的遙測，並且能夠撰寫自訂遙測。

如果您想要重新發行，但不將 Application Insights 新增至程式碼，請留意部署程序可能會從已發佈的網站中刪除 DLL 和 ApplicationInsights.config。 因此：

1. 如果您已編輯 ApplicationInsights.config，請在重新發佈應用程式前複製一份。
2. 重新發佈您的應用程式。
3. 重新啟用 Application Insights 監視功能。 (使用適當的方法︰Azure Web 應用程式控制台或 IIS 主機上的狀態監視器。)
4. 恢復您在 .config 檔案上執行的所有編輯。


## <a name="troubleshooting"></a><a name="troubleshoot"></a>疑難排解

### <a name="confirm-a-valid-installation"></a>確認有效安裝 

這些是您可以執行的一些步驟，以確認您的安裝是否成功。

- 確認 applicationInsights.config 檔案在您的目標應用程式目錄中，並且包含您的 ikey。

- 如果您懷疑資料遺失，您可以在 [Analytics](../log-query/get-started-portal.md) 中執行查詢，以列出目前正在傳送遙測的所有雲端角色。
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- 如果您需要確認已成功附加 Application Insights，您可以在命令視窗中執行 [Sysinternals 控制碼](/sysinternals/downloads/handle) ，確認 IIS 已載入 applicationinsights.dll。

  ```console
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>無法連接？ 沒有遙測資料？

* 在伺服器的防火牆中開啟[必要的傳出連接埠](./ip-addresses.md#outgoing-ports)，以允許狀態監視器運作。

### <a name="unable-to-login"></a>無法登入

如果無法登入狀態監視器，請改用命令列進行安裝。 狀態監視器嘗試登入以收集您的 ikey，但您可以使用下列命令以手動方式提供：

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>無法載入檔案或組件 'System.Diagnostics.DiagnosticSource'

啟用 Application Insights 後，您可能會發生此錯誤。 這是因為安裝程式會在 bin 目錄中取代此 dll。
若要修正更新您的 web.config：

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

我們正在[此處](https://github.com/MohanGsk/ApplicationInsights-Home)追蹤此問題。


### <a name="application-diagnostic-messages"></a>應用程式診斷訊息

* 開啟狀態監視器，然後選取左窗格中的應用程式。 檢查 [設定通知] 區段中是否有任何關於此應用程式的診斷訊息：

  ![開啟 [效能] 刀鋒視窗，即可查看要求、回應時間、相依性和其他資料](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>詳細記錄

* 根據預設，狀態監視器將於下列位置輸出診斷記錄：`C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* 若要輸出詳細資訊記錄，請修改組態檔 `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` 並將 `<add key="TraceLevel" value="All" />` 新增至 `appsettings`。
然後重新啟動狀態監視器。

* 當狀態監視器是 .NET 應用程式時，您也可以藉 [由將適當的診斷新增至設定檔](/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element)，來啟用 .net 追蹤。 例如，在某些情況下，您可以藉由設定[網路追蹤](/dotnet/framework/network-programming/how-to-configure-network-tracing)來查看網路層級的情況

### <a name="insufficient-permissions"></a>權限不足
  
* 如果您在伺服器上看到有關「權限不足」的訊息，請嘗試下列操作：
  * 在 IIS 管理員中，選取應用程式集區，開啟 [進階設定]****，並記下 [處理序模型]**** 下的身分識別。
  * 在電腦的管理控制台中，將此身分識別加入至效能監試器使用者群組。

### <a name="conflict-with-systems-center-operations-manager"></a>與 Systems Center Operations Manager 衝突

* 如果您的伺服器上已安裝 MMA/SCOM (Microsoft Operations Management Suite)，某些版本可能會發生衝突。 解除安裝 SCOM 和狀態監視器，重新安裝最新版本。

### <a name="failed-or-incomplete-installation"></a>安裝失敗或不完整

如果狀態監視器在安裝期間失敗，則可能會出現狀態監視器無法從中復原的不完整的安裝。 這將需要手動重設。

刪除應用程式目錄中找到的任何檔案：
- bin 目錄中以 "Microsoft.AI." 或 "Microsoft.ApplicationInsights." 開頭的任何 Dll。
- bin 目錄中的此 DLL "Microsoft.Web.Infrastructure.dll"
- bin 目錄中的此 DLL "System.Diagnostics.DiagnosticSource.dll"
- 在您的應用程式目錄中移除 "App_Data\packages"
- 在您的應用程式目錄中移除 "applicationinsights.config"


### <a name="additional-troubleshooting"></a>其他疑難排解

* 請參閱[其他疑難排解][qna]。

## <a name="system-requirements"></a>系統需求
支援伺服器上 Application Insights 狀態監視器的作業系統：

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

使用最新的 SP 和 .NET Framework 4.5 (狀態監視器是以這個版本的 Framework 為基礎) 

在用戶端 Windows 7、8、8.1 和 10 上，一樣需含有 .NET Framework 4.5

IIS 支援：IIS 7、7.5、8、8.5 (需要有 IIS)

## <a name="automation-with-powershell"></a>使用 PowerShell 進行自動化
您可以在 IIS 伺服器上使用 PowerShell 啟動和停止監視。

先匯入 Application Insights 模組︰

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
```

找出受監視的應用程式︰

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (選擇性) Web 應用程式的名稱。
* 顯示此 IIS 伺服器中每個 Web 應用程式 (或具名應用程式) 的 Application Insights 監視狀態。
* 傳回每個應用程式的 `ApplicationInsightsApplication`︰

  * `SdkState==EnabledAfterDeployment`︰應用程式正受到監視，並已在執行階段透過「狀態監視器」工具或 `Start-ApplicationInsightsMonitoring` 進行檢測。
  * `SdkState==Disabled`︰不會針對 Application insights 檢測應用程式。 應用程式從未接受檢測，或「狀態監視器」工具或 `Stop-ApplicationInsightsMonitoring`已停用執行階段監視。
  * `SdkState==EnabledByCodeInstrumentation`︰已透過將 SDK 加入至原始程式碼來檢測應用程式。 其 SDK 無法更新或停止。
  * `SdkVersion` 會顯示正用來監視此應用程式的版本。
  * `LatestAvailableSdkVersion`會顯示 NuGet 資源庫上目前可用的版本。 若要將應用程式升級至此版本，請使用 `Update-ApplicationInsightsMonitoring`。

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` 應用程式在 IIS 中的名稱
* `-InstrumentationKey` 您想要在其中顯示結果之 Application Insights 資源的 ikey。
* 這個 Cmdlet 只會影響尚未檢測的應用程式，也就是 SdkState==NotInstrumented。

    這個 Cmdlet 不會影響已經過檢測的應用程式。 不論是在建置階段透過將 SDK 新增至程式碼，還是在執行階段透過先前使用的這個 Cmdlet，都沒有影響。

    用來檢測應用程式的 SDK 版本是最常下載到此伺服器的版本。

    若要下載最新版本，請使用 Update-ApplicationInsightsVersion。
* 成功時會傳回 `ApplicationInsightsApplication` 。 如果失敗，則會在 stderr 記錄追蹤。

   ```output
   Name                      : Default Web Site/WebApp1
   InstrumentationKey        : 00000000-0000-0000-0000-000000000000
   ProfilerState             : ApplicationInsights
   SdkState                  : EnabledAfterDeployment
   SdkVersion                : 1.2.1
   LatestAvailableSdkVersion : 1.2.3
   ```

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` 應用程式在 IIS 中的名稱
* `-All` 停止監視此 IIS 伺服器中所有 `SdkState==EnabledAfterDeployment` 的應用程式
* 停止監視指定應用程式並移除檢測。 它僅適用於已在執行階段使用「狀態監視器」工具或 Start-ApplicationInsightsApplication 進行檢測的應用程式。 (`SdkState==EnabledAfterDeployment`)
* 傳回 ApplicationInsightsApplication。

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`：Web 應用程式在 IIS 中的名稱。
* `-InstrumentationKey` (選擇項。 ) 使用此選項來變更應用程式遙測的傳送資源。
* 此 Cmdlet：
  * 將具名應用程式升級至最近下載到這台電腦之 SDK 的版本。 (僅適用於 `SdkState==EnabledAfterDeployment`時)
  * 如果您提供檢測金鑰，具名應用程式會重新設定為將遙測傳送至具有該索引鍵的資源。 (適用於 `SdkState != Disabled`時)

`Update-ApplicationInsightsVersion`

* 將最新的 Application Insights SDK 下載至伺服器。

## <a name="questions-about-status-monitor"></a><a name="questions"></a>狀態監視器的相關問題

### <a name="what-is-status-monitor"></a>什麼是狀態監視器？

您在 IIS Web 伺服器中安裝的桌面應用程式。 它可協助您檢測和設定 Web 應用程式。 

### <a name="when-do-i-use-status-monitor"></a>如何使用狀態監視器？

* 檢測在 IIS 伺服器上執行的任何 Web 應用程式 - 即使它已在執行中。
* 針對已在編譯階段[使用 Application Insights SDK 建置](./asp-net.md)的 Web 應用程式，啟用其他遙測。 

### <a name="can-i-close-it-after-it-runs"></a>是否可以在執行之後將它關閉？

是。 檢測您選取的網站之後，您可以將它關閉。

它本身不會收集遙測資料。 它只會設定 Web 應用程式和設定一些權限。

### <a name="what-does-status-monitor-do"></a>狀態監視器有何作用？

當您選取 Web 應用程式以供狀態監視器檢測時︰

* 下載 Application Insights 組件和 ApplicationInsights.config 檔案並且放置於 Web 應用程式的 binaries 資料夾中。
* 啟用 CLR 分析以收集相依性呼叫。

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>狀態監視器安裝哪個版本的 Application Insights SDK？

目前，狀態監視器只能安裝 Application Insights SDK 版本 2.3 或 2.4。 

Application Insights SDK 2.4 版是 [最後一個支援 .net 4.0 的版本](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) ，也就是 [1 月 2016](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/)。 因此，從現在開始狀態監視器可以用來檢測 .NET 4.0 應用程式。 

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>每次更新應用程式時，是否需要執行狀態監視器？

如果您是以累加方式重新部署，則不需要。 

如果您在發佈程序中選取 [刪除現有的檔案] 選項，則必須重新執行狀態監視器才能設定 Application Insights。

### <a name="what-telemetry-is-collected"></a>會收集哪些遙測資料？

對於只會在執行階段使用狀態監視器檢測的應用程式︰

* HTTP 要求
* 相依性呼叫
* 例外狀況
* 效能計數器

對於在編譯階段已經過檢測的應用程式︰

 * 處理程序計數器。
 * 相依性呼叫 (.NET 4.5)；在相依性呼叫中傳回值 (.NET 4.6)。
 * 例外狀況堆疊追蹤值。

[深入了解](https://apmtips.com/posts/2016-11-18-how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download-status-monitor"></a><a name="download"></a>下載狀態監視器

- 使用新的 [PowerShell 模組](./status-monitor-v2-overview.md)
- 下載並執行 [狀態監視器安裝程式](https://go.microsoft.com/fwlink/?LinkId=506648)
- 或執行 [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) 並在其中搜尋 Application Insights 狀態監視器。

## <a name="next-steps"></a><a name="next"></a>後續步驟

檢視遙測：

* [探索計量](../platform/metrics-charts.md)以監視效能和使用量
* [搜尋事件和記錄][diagnostic]以診斷問題
* 更多進階查詢的[分析](../log-query/log-query-overview.md)

新增更多遙測：

* [建立 Web 測試][availability]，確定您的網站保持即時狀態。
* [新增 Web 用戶端遙測][usage]，以查看網頁程式碼中的例外狀況，並讓您插入追蹤呼叫。
* [將 Application Insights SDK 新增至您的程式碼][greenbrown]，以便插入追蹤和記錄呼叫

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[roles]: ./resources-roles-access-control.md
[usage]: ./javascript.md

