---
title: 沒有要進行疑難排解的資料 - Application Insights for .NET
description: 在 Azure Application Insights 中看不到資料？ 試試這裡。
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 351ef145ab65fee8397034912f9a6ce295f1f909
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517163"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>針對沒有資料進行疑難排解 - Application Insights for .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>我遺失了部分遙測
*在 Application Insights 中，我只會看到我的應用程式所產生的一小部分事件。*

* 如果您持續看到同一個部分，可能是因為調適性 [取樣](../../azure-monitor/app/sampling.md)所導致。 若要確認這一點，開啟 [搜尋] \(從 [概觀] 刀鋒視窗)，並查看要求或其他事件的執行個體。 按一下 [屬性] 區段底部的 [...]，以取得完整的屬性詳細資料。 如果要求計數 > 1，則表示取樣正在運作中。
* 否則，有可能是您已達到定價方案的 [資料速率限制](../../azure-monitor/app/pricing.md#limits-summary) 。 每分鐘都會套用這些限制。

*我會隨機遇到資料遺失的情況。*

* 請在[遙測通道](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)確認您是否遇到資料遺失的情況

* 請在遙測通道的 [GitHub 存放庫](https://github.com/Microsoft/ApplicationInsights-dotnet/issues)中查看是否有任何已知問題

*當應用程式即將停止時，我會在主控台應用程式或 Web 應用程式上遇到資料遺失的情況。*

* SDK 通道會將遙測資料保留在緩衝區中，並以批次方式傳送。 如果應用程式即將關閉，您可能需要明確呼叫 [Flush()](api-custom-events-metrics.md#flushing-data)。 `Flush()` 的行為取決於實際使用的[通道](telemetry-channels.md#built-in-telemetry-channels)。

## <a name="no-data-from-my-server"></a>沒有來自我的伺服器的資料
我已在 Web 伺服器上安裝我的應用程式，而我現在沒看到任何來自於它的遙測。它在我的開發電腦上運作正常。

* 可能是防火牆問題。 [設定 Application Insights 的防火牆例外狀況以傳送資料](../../azure-monitor/app/ip-addresses.md)。
* IIS 伺服器可能會遺漏某些先決條件：.NET 擴充性 4.5 和 ASP.NET 4.5。

*我已在 Web 伺服器上[安裝狀態監視器](../../azure-monitor/app/monitor-performance-live-website-now.md)來監視現有的應用程式。我沒有看到任何結果。*

* 請參閱 [疑難排解狀態監視器](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot)。

## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>在 Visual Studio 中沒有「新增 Application Insights」選項
當我以滑鼠右鍵按一下 [方案總管] 中的現有專案時，沒有看到任何 Application Insights 選項。

* 工具並非支援所有類型的 .NET 專案。 支援 Web 和 WCF 專案。 對於其他像是傳統型或服務應用程式的專案類型，您仍然可以 [手動將 Application Insights SDK 新增至您的專案](../../azure-monitor/app/windows-desktop.md)。
* 請確定您有 [Visual Studio 2013 Update 3 或更新版本](/visualstudio/releasenotes/vs2013-update3-rtm-vs)。 它會預先安裝開發人員分析工具，這些工具提供 Application Insights SDK。
* 選取 [工具]、[擴充功能和更新]，檢查 [開發人員分析工具] 是否已安裝並啟用。 如果是，按一下 [更新]  以查看是否有可用的更新。
* 開啟 [新增專案] 對話方塊，並且選擇 [ASP.NET Web 應用程式]。 如果您在那裡看到 Application Insights 選項，則工具已安裝。 如果沒有，請嘗試先解除安裝，然後再重新安裝 Developer Analytics Tools。

## <a name="adding-application-insights-failed"></a><a name="q02"></a>新增 Application Insights 失敗
當我嘗試將 Application Insights 新增至現有的專案時，看到錯誤訊息。

可能的原因：

* 與 Application Insights 入口網站通訊失敗；或者
* 您的 Azure 帳戶發生問題；
* 您只具備 [您嘗試建立新資源之訂用帳戶或群組的讀取權限](../../azure-monitor/app/resources-roles-access-control.md)。

修正：

* 請檢查您為正確的 Azure 帳戶提供登入認證。
* 在瀏覽器中，檢查您是否可以存取 [Azure 入口網站](https://portal.azure.com)。 開啟 [設定] 並查看是否有任何限制。
* [將 Application Insights 新增至您現有的專案](../../azure-monitor/app/asp-net.md)：在 [方案總管] 中以滑鼠右鍵按一下您的專案，並選擇 [新增 Application Insights]。

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>我收到「檢測金鑰不能是空白」的錯誤
可能是您在安裝 Application Insights 或記錄配接器時發生問題。

在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [Application Insights] > [設定 Application Insights]。 將會出現對話方塊邀請您登入 Azure，並建立 Application Insights 資源或重複使用現有的資源。

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a> 我的組建伺服器上「NuGet 封裝遺失」
*我在我的開發機器上偵錯時所有組建都沒有問題，但是在組建伺服器上收到 NuGet 錯誤。*

請參閱 [NuGet 套件還原](https://docs.nuget.org/Consume/Package-Restore)和[自動套件還原](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore)。

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>遺失從 Visual Studio 開啟 Application Insights 的功能表命令
*當我以滑鼠右鍵按一下專案 [方案總管] 時，我沒有看到任何 Application Insights 命令，或沒有看到開啟 Application Insights 命令。*

可能的原因：

* 如果您是以手動方式建立 Application Insights 資源，或專案是 Application Insights 工具不支援的類型。
* 您的 Visual Studio 中已停用開發人員分析工具。
* 您的 Visual Studio 版本比 2013 Update 3 還舊。

修正：

* 請確定您的 Visual Studio 版本是 2013 Update 3 或更新版本。
* 選取 [工具]、[擴充功能和更新]，檢查 [開發人員分析工具] 是否已安裝並啟用。 如果是，按一下 [更新]  以查看是否有可用的更新。
* 以滑鼠右鍵按一下 [方案總管] 中的專案。 如果您看到命令 [Application Insights] > [設定 Application Insights]，使用它將專案連線至 Application Insights 服務中的資源。

否則，Developer Analytics Tools 不會直接支援您的專案類型。 若要查看您的遙測，請登入 [Azure 入口網站](https://portal.azure.com)，在左側的導覽列中，選擇 [Application Insights]，然後選取您的應用程式。

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>從 Visual Studio 開啟 Application Insights 時「拒絕存取」
*[開啟 Application Insights] 功能表命令帶我前往 Azure 入口網站，但是我收到「拒絕存取」錯誤。*

您上一次在預設瀏覽器中使用的 Microsoft 登入，沒有 [將 Application Insights 新增至這個應用程式時所建立的資源](../../azure-monitor/app/asp-net.md)的存取權。 有兩個可能的原因：

* 您有一個以上的 Microsoft 帳戶 - 可能是工作和個人 Microsoft 帳戶？ 您上一次在預設瀏覽器上使用的登入，是與具有 [將 Application Insights 新增至專案](../../azure-monitor/app/asp-net.md)的存取權的帳戶不同的帳戶。
  * 修正：按一下瀏覽器視窗右上方的您的名稱並且登出。然後以具有存取權的帳戶登入。 然後在左側的導覽列中，按一下 [Application Insights]，並選取您的應用程式。
* 其他人將 Application Insights 新增至專案，且他們忘記為您提供所建立的 [資源群組的存取權](../../azure-monitor/app/resources-roles-access-control.md) 。
  * 修正：如果他們使用組織帳戶，他們可以將您加入小組；或者，他們可以授與您資源群組的個別存取權。

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>從 Visual Studio 開啟 Application Insights 時「找不到資產」
*[開啟 Application Insights] 功能表命令帶我前往 Azure 入口網站，但是我收到「找不到資產」錯誤。*

可能的原因：

* 您的應用程式的 Application Insights 資源已刪除；或
* 檢測金鑰已在 ApplicationInsights.config 中直接編輯以設定或變更，而不需更新專案檔案。

ApplicationInsights.config 中的檢測金鑰會控制遙測傳送的位置。 專案檔案中的資料行控制當您在 Visual Studio 中使用命令時會開啟哪一個資源。

修正：

* 在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [Application Insights]、[設定 Application Insights]。 在對話方塊中，您可以選擇將遙測傳送至現有的資源，或建立新的資源。 或：
* 直接開啟資源。 登入 [Azure 入口網站](https://portal.azure.com)，在左側的導覽列中，按一下 [Application Insights]，然後選取您的應用程式。

## <a name="where-do-i-find-my-telemetry"></a>哪裡可以找到我的遙測？
*我已登入 [Microsoft Azure 入口網站](https://portal.azure.com)，且正在查看 Azure 主儀表板。那麼我可以在哪裡找到我的 Application Insights 資料？*

* 在左側的導覽列中，按一下 [Application Insights]，然後選取您的應用程式名稱。 如果您在其中沒有任何專案，則需要 [在您的 Web 專案中新增或設定 Application Insights](../../azure-monitor/app/asp-net.md)。  
  您可以從中看到一些摘要圖表。 您可以逐一點選以查看更詳細的資料。
* 在 Visual Studio 中偵錯應用程式時，按一下 [Application Insights] 按鈕。

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a> 沒有任何伺服器資料 (或根本沒有資料)
我執行我的應用程式，然後在 Microsoft Azure 中開啟 Application Insights 服務，但是所有圖表都顯示「了解如何收集...」或「未設定」。 或者，只有頁面檢視和使用者資料，但卻沒有任何伺服器資料。

* 在 Visual Studio 中以偵錯模式執行您的應用程式 (F5)。 使用您的應用程式以產生一些遙測。 請檢查您可以在 Visual Studio 的 [輸出] 視窗中看到所記錄的事件。  
  ![顯示在 Visual Studio 的 [偵測模式] 中執行應用程式的螢幕擷取畫面。](./media/asp-net-troubleshoot-no-data/output-window.png)
* 在 Application Insights 入口網站中，開啟 [診斷搜尋](../../azure-monitor/app/diagnostic-search.md)。 這裡通常會先顯示資料。
* 按一下 [重新整理] 按鈕。 刀鋒視窗會定期自行重新整理，但您也可以手動重新整理。 時間範圍越大，重新整理的間隔就越長。
* 檢查檢測金鑰相符。 在 Application Insights 入口網站的應用程式主要刀鋒視窗中，在 [Essentials] 下拉式清單中，查看 [檢測金鑰]。 然後，在您的 Visual Studio 的專案中，開啟 ApplicationInsights.config，並尋找 `<instrumentationkey>`。 請檢查兩個金鑰是否相等。 如果不是：  
  * 在入口網站中，按一下 [Application Insights]，並且尋找具有正確金鑰的應用程式資源；或
  * 在 Visual Studio [方案總管] 中，以滑鼠右鍵按一下專案，然後選擇 [Application Insights]、[設定]。 重設應用程式，以將遙測傳送至正確的資源。
  * 如果找不到相符的金鑰，請確認您在 Visual Studio 中使用與入口網站相同的登入認證。
* 在 [Microsoft Azure 主儀表板](https://portal.azure.com)中，查看服務健全狀況對應。 如果看到一些警示指示，請等待它們恢復 [正常]，然後關閉再重新開啟 Application Insights 應用程式刀鋒視窗。
* 也請查閱 [我們的狀態部落格](https://techcommunity.microsoft.com/t5/azure-monitor-status/bg-p/AzureMonitorStatusBlog)。
* 您針對[伺服器端 SDK](../../azure-monitor/app/api-custom-events-metrics.md) 撰寫的任何程式碼是否可能會變更 `TelemetryClient` 執行個體或 `TelemetryContext` 中的檢測金鑰？ 或者您撰寫的 [篩選或取樣組態](../../azure-monitor/app/api-filtering-sampling.md) 可能會篩選出過多項目？
* 如果您編輯過 ApplicationInsights.config，請仔細檢查 [TelemetryInitializers 和 TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md)的組態。 不當命名的類型或參數可能導致 SDK 不會傳送任何資料。

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>頁面檢視、瀏覽器、使用量上沒有任何資料
*我在「伺服器回應時間」和「伺服器要求」圖表中看到資料，但是 [頁面檢視載入] 時間，或 [瀏覽器] 或 [使用量] 刀鋒視窗中沒有任何資料。*

資料來自網頁中的指令碼。 

* 如果您將 Application Insights 加入至現有的 Web 專案，則 [您必須手動加入指令碼](../../azure-monitor/app/javascript.md)。
* 確定 Internet Explorer 並非以相容性模式顯示您的網站。
* 若要確認資料確實傳送至 `dc.services.visualstudio.com`，請使用瀏覽器的偵錯功能 (部分瀏覽器是使用 F12 鍵，然後選擇 [網路])。

## <a name="no-dependency-or-exception-data"></a>沒有相依性或例外狀況資料
請參閱[相依性遙測](../../azure-monitor/app/asp-net-dependencies.md)和[例外狀況遙測](asp-net-exceptions.md)。

## <a name="no-performance-data"></a>沒有效能資料
效能資料 (CPU、IO 速率等等) 適用於 [Java Web 服務](../../azure-monitor/app/java-collectd.md)、[Windows 傳統型應用程式](../../azure-monitor/app/windows-desktop.md)、[IIS Web 應用程式和服務 (若您安裝狀態監視器)](../../azure-monitor/app/monitor-performance-live-website-now.md) 和 [Azure 雲端服務](../../azure-monitor/app/app-insights-overview.md)。 您將會在 [設定]、[伺服器] 之下看到該資料。

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>我發佈應用程式到我的伺服器之後卻沒有 (伺服器) 資料
* 請確認您實際上已複製所有 Microsoft. ApplicationInsights DLL 到伺服器，並且連帶 Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll ApplicationInsights Dll 一併複製。
* 在防火牆中，您可能必須[開啟某些 TCP 連接埠](../../azure-monitor/app/ip-addresses.md)。
* 如果您必須使用 Proxy 在貴公司網路之外傳送內容，請設定 Web.config 中的 [defaultProxy](/previous-versions/dotnet/netframework-1.1/aa903360(v=vs.71))
* Windows Server 2008：請確定您已安裝下列更新：[KB2468871](https://support.microsoft.com/kb/2468871)、[KB2533523](https://support.microsoft.com/kb/2533523)、[KB2600217](https://support.microsoft.com/kb/2600217)。

## <a name="i-used-to-see-data-but-it-has-stopped"></a>我曾經看到資料，但是已停止
* 您有達到資料點的每月配額嗎？ 開啟 [設定/配額和定價] 即可查看。若有達到配額，您可以升級您的方案，或付費取得額外容量。 請參閱 [定價配置](https://azure.microsoft.com/pricing/details/application-insights/)。

## <a name="i-dont-see-all-the-data-im-expecting"></a>我並沒有看到預期的所有資料
如果您的應用程式傳送大量資料，且您是使用 Application Insights SDK for ASP.NET 版本 2.0.0-beta3 或更新版本，則 [調適性取樣](../../azure-monitor/app/sampling.md) 功能可能會運作，並只傳送一部分的遙測資料。

您可以停用它，但是這不是建議的作法。 取樣經過設計，以便能正確傳輸相關的遙測，供診斷之用。

## <a name="client-ip-address-is-0000"></a>用戶端 IP 位址為 0.0.0.0

我們已在 2018 年 2 月 5 日宣布我們已移除記錄用戶端 IP 位址的功能。 這不會影響地理位置。

> [!NOTE]
> 如果您需要 IP 位址的前 3 個八位元，可以使用[遙測初始設定式](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)來新增自訂屬性。
> 這不會影響到 2018 年 2 月 5 日以前收集的資料。

## <a name="wrong-geographical-data-in-user-telemetry"></a>使用者遙測中錯誤的地理資料
城市、區域和國家/地區維度衍生自 IP 位址，而且不一定準確。 這些 IP 位址會先針對位置進行處理，然後變更為 0.0.0.0 以進行儲存。

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>在 Azure 雲端服務中執行時發生的「找不到方法」例外狀況
您是否已針對 .NET 4.6 組建？ Azure 雲端服務角色不自動支援 4.6。 [在每個角色上安裝 4.6](../../cloud-services/cloud-services-dotnet-install-dotnet.md) ，再執行您的 App。

## <a name="troubleshooting-logs"></a>對記錄進行疑難排解

請依照下列指示擷取架構的疑難排解記錄。

### <a name="net-framework"></a>.NET Framework

1. 從 NuGet 安裝 [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) 套件。 您安裝的版本必須符合目前安裝的 `Microsoft.ApplicationInsighs` 版本

2. 修改您的 applicationinsights.config 檔案以包含下列內容：

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    您的應用程式必須對設定的位置具有寫入權限

3. 重新啟動程序，讓 SDK 取得這些新設定

4. 完成作業後，請還原這些變更。

### <a name="net-core"></a>.NET Core

1. 從 NuGet 安裝適用于 ASP.NET Core 套件的[APPLICATION INSIGHTS SDK NuGet 套件](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)。 您安裝的版本必須符合目前安裝的版本 `Microsoft.ApplicationInsights` 。

   AspNetCore 的最新版本是2.14.0，而它是指 ApplicationInsights 版本2.14.0。 因此，要安裝的 ApplicationInsights 版本應該是2.14.0。

2. 修改 `Startup.cs` 類別中的 `ConfigureServices` 方法：

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    您的應用程式必須對設定的位置具有寫入權限

3. 重新啟動程序，讓 SDK 取得這些新設定

4. 完成作業後，請還原這些變更。


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a> 使用 PerfView 收集記錄
[PerfView](https://github.com/Microsoft/perfview) 是免費的診斷和效能分析工具，可從許多來源收集診斷資訊並加以視覺化，以協助找出 CPU、記憶體和其他問題。

Application Insights SDK 會記錄 EventSource 自我疑難排解記錄，並可由 PerfView 加以擷取。

若要收集記錄，請下載 PerfView 並執行下列命令：
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

您可以視需要修改這些參數：
- **MaxCollectSec**。 設定此參數可防止 PerfView 永無止盡地執行，而影響到伺服器的效能。
- **OnlyProviders**。 將此參數設定為只從 SDK 收集記錄。 您可以根據特定調查來自訂此清單。 
- **NoGui**。 設定此參數可在不使用 GUI 的情況下收集記錄。


如需詳細資訊，
- [使用 PerfView 記錄效能追蹤](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView)。
- [Application Insights 事件來源](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="collect-logs-with-dotnet-trace"></a>使用 dotnet-trace 收集記錄

[`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace) 是可收集記錄以供疑難排解之用的替代方法，此方法對於 Linux 型環境可能特別有用

```bash
dotnet-trace collect --process-id <PID> --providers Microsoft-ApplicationInsights-Core,Microsoft-ApplicationInsights-Data,Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,Microsoft-ApplicationInsights-Extensibility-DependencyCollector,Microsoft-ApplicationInsights-Extensibility-HostingStartup,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,Microsoft-ApplicationInsights-Extensibility-Web,Microsoft-ApplicationInsights-Extensibility-WindowsServer,Microsoft-ApplicationInsights-WindowsServer-Core,Microsoft-ApplicationInsights-Extensibility-EventSourceListener,Microsoft-ApplicationInsights-AspNetCore
```

## <a name="how-to-remove-application-insights"></a>如何移除 Application Insights

請遵循移除[文章](../../azure-monitor/app/remove-application-insights.md)中提供的步驟，以了解如何移除 Visual Studio 中的 Application Insights。

## <a name="still-not-working"></a>仍然無法運作...
* [Application Insights 的 Microsoft 問與答頁面](/answers/topics/azure-monitor.html)
