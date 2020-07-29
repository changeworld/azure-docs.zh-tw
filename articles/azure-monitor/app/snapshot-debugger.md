---
title: .NET 應用程式的 Azure 應用程式 Insights 快照偵錯工具
description: 在生產環境 .NET 應用程式中擲回例外狀況時，會自動收集偵錯快照集
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: 58fbb0cee5e4f06c20d31b4b5011582957f6f6c3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325639"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET 應用程式中的例外狀況偵錯快照集
發生例外狀況時，您可以自動從即時 Web 應用程式收集偵錯快照集。 快照集會顯示擲回例外狀況時原始程式碼和變數的狀態。 [Azure 應用程式 Insights](./app-insights-overview.md)中的快照偵錯工具會監視來自 web 應用程式的例外狀況遙測。 它會收集前幾個擲回例外狀況的快照集，讓您取得診斷生產環境中問題所需的資訊。 在您的應用程式中包含[快照集收集器 NuGet 套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)，並選擇性地在[ApplicationInsights.config](./configuration-with-applicationinsights-config.md)中設定集合參數。快照集會出現在 Application Insights 入口網站的[例外](./asp-net-exceptions.md)狀況中。

您可以檢視入口網站中的偵錯快照集，以查看呼叫堆疊並檢查每個呼叫堆疊框架的變數。 若要使用原始程式碼取得更強大的偵錯工具體驗，請使用 Visual Studio 2019 Enterprise 開啟快照集。 在 Visual Studio 中，您也可以[設定貼齊點以互動方式建立快照集](https://aka.ms/snappoint)，而不需等待例外狀況。

「偵錯工具快照集」會儲存15天。 此保留原則會就個別的應用程式而設定。 如果您需要增加此值，您可以在 Azure 入口網站中建立支援案例，以提出增加的要求。

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>啟用應用程式的 Application Insights 快照偵錯工具
快照集集合適用於：
* 執行 .NET Framework 4.5 或更新版本的 .NET Framework 和 ASP.NET 應用程式。
* 在 Windows 上執行的 .NET Core 2.0 和 ASP.NET Core 2.0 應用程式。

支援下列環境：

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* 執行 OS 系列4或更新版本的[Azure 雲端服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* 在 Windows Server 2012 R2 或更新版本上執行的[Azure Service Fabric 服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* 執行 Windows Server 2012 R2 或更新版本的[Azure 虛擬機器和虛擬機器擴展集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* 執行 Windows Server 2012 R2 或更新版本或 Windows 8.1 或更新版本[的內部部署虛擬或實體機器](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> 不支援用戶端應用程式 (例如，WPF、Windows Forms 或 UWP)。

如果您已啟用快照偵錯工具，但看不到快照集，請參閱我們的[疑難排解指南](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。

## <a name="grant-permissions"></a>授與權限

對快照集的存取會受到角色型存取控制 (RBAC) 的保護。 必須在訂用帳戶擁有者將您新增至必要角色後，您才能檢查快照集。

> [!NOTE]
> 擁有者和參與者並不會自動獲得此角色。 如果他們想要檢視快照集，他們必須將自己新增至此角色。

訂用帳戶擁有者應將 `Application Insights Snapshot Debugger` 角色指派給將會檢查快照集的使用者。 這個角色可以由目標 Application Insights 資源或其資源群組或訂用帳戶的訂用帳戶擁有者，指派給個別使用者或群組。

1. 在 Azure 入口網站中巡覽到 Application Insights 資源。
1. 按一下 [存取控制 (IAM)]。
1. 按一下 [+新增角色指派]**** 按鈕。
1. 從 [角色]**** 下拉式清單中選取 [Application Insights 快照集偵錯工具]****。
1. 搜尋並輸入要新增的使用者名稱。
1. 按一下 [儲存]**** 按鈕，將使用者新增至角色。


> [!IMPORTANT]
> 快照集可能會在變數和參數值中包含個人和其他機密資訊。

## <a name="view-snapshots-in-the-portal"></a>在入口網站中查看快照集

在您的應用程式中發生例外狀況並建立快照集之後，您應該會有要查看的快照集。 可能需要5到10分鐘的時間，才能完成快照集的例外狀況，並可從入口網站查看。 若要查看快照集，請在 [**失敗**] 窗格中選取 [**作業**] 按鈕，查看 [**作業**] 索引標籤，或在 [**例外**] 索引標籤中選取 [**例外**狀況] 按鈕

![失敗頁面](./media/snapshot-debugger/failures-page.png)

在右窗格中選取作業或例外狀況，以開啟 [**端對端交易詳細資料**] 窗格，然後選取 [例外狀況] 事件。 如果快照集可用於指定的例外狀況，則在右窗格上會出現 [**開啟的偵錯工具快照**集] 按鈕，其中包含[例外](./asp-net-exceptions.md)狀況的詳細資料。

![例外狀況的 [開啟偵錯快照集] 按鈕](./media/snapshot-debugger/e2e-transaction-page.png)

在 [偵錯快照集] 檢視中，您會看到呼叫堆疊和變數窗格。 當您在 [呼叫堆疊] 窗格中選取呼叫堆疊的框架時，您可以檢視 [變數] 窗格中的本機變數和該函式呼叫的參數。

![檢視入口網站中的偵錯快照集](./media/snapshot-debugger/open-snapshot-portal.png)

快照集可能包含機密資訊，依預設為不可檢視。 若要檢視快照集，您必須有指派給您的 `Application Insights Snapshot Debugger` 角色。

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>查看 Visual Studio 2017 Enterprise 或更新版本中的快照集
1. 按一下 [**下載快照**集] 按鈕 `.diagsession` ，下載可由 Visual Studio Enterprise 開啟的檔。

2. 若要開啟檔案 `.diagsession` ，您必須安裝快照偵錯工具 Visual Studio 元件。 快照偵錯工具元件是 Visual Studio 中 ASP.net 工作負載的必要元件，而且可以從 Visual Studio 安裝程式中的個別元件清單中選取。 如果您使用 Visual Studio 2017 15.5 版之前的 Visual Studio 版本，您必須從[Visual Studio Marketplace](https://aka.ms/snapshotdebugger)安裝延伸模組。

3. 開啟快照集檔案之後，Visual Studio 中的 [小型傾印偵錯] 分頁隨即出現。 按一下 [偵錯受控碼]**** 以開始偵錯快照集。 快照集會開啟至擲回例外狀況的程式碼行，您可將程序的目前狀態進行偵錯。

    ![檢視 Visual Studio 中的偵錯快照集](./media/snapshot-debugger/open-snapshot-visualstudio.png)

下載的快照集會包含 Web 應用程式伺服器上找到的任何符號檔。 若要建立快照集資料與原始程式碼的關聯，就需要這些符號檔。 對於 App Service 應用程式，當您發佈 Web 應用程式時請務必啟用符號部署。

## <a name="how-snapshots-work"></a>快照集運作方式

快照集收集器會實作為 [Application Insights 遙測處理器](./configuration-with-applicationinsights-config.md#telemetry-processors-aspnet)。 當您的應用程式執行時，快照集收集器的遙測處理器會新增至您應用程式的遙測管線中。
每次應用程式呼叫 [TrackException](./asp-net-exceptions.md#exceptions) 時，快照集收集器會根據所擲回例外狀況的類型和擲回方法計算問題識別碼。
每次應用程式呼叫 TrackException 時，適當問題識別碼的計數器即會遞增。 當計數器達到 `ThresholdForSnapshotting` 值時，問題識別碼就會新增至收集計畫。

快照集收集器還會藉由訂閱 [AppDomain.CurrentDomain.FirstChanceException](/dotnet/api/system.appdomain.firstchanceexception) 事件來監視擲回的例外狀況。 當該事件引發時，系統會計算例外狀況的問題識別碼，並將其與收集計畫中的問題識別碼進行比較。
如果沒有相符項目，則會建立執行中處理序的快照集。 快照集獲指派一個唯一的識別碼，而例外狀況則標有該識別碼的戳記。 FirstChanceException 處理常式返回之後，所擲回的例外狀況會以正常方式處理。 最後，例外狀況會再次到達 TrackException 方法，它將在其中與快照集識別碼一起回報給 Application Insights。

主要處理序會繼續執行，並在很少中斷的情況下提供流量給使用者。 同時，快照集會遞交給快照集上傳程式處理序。 接著，快照集上傳程式會建立小型傾印，並將其與任何相關符號檔 (.pdb) 一起上傳至 Application Insights。

> [!TIP]
> - 處理序快照集是執行中處理序的已暫止複製品。
> - 建立快照集大約需要 10 到 20 毫秒的時間。
> - `ThresholdForSnapshotting` 的預設值為 1。 這也是最小值。 因此，在建立快照集之前，您的應用程式必須觸發相同的例外狀況**兩次**。
> - 如果您想要在 Visual Studio 中進行偵錯時產生快照集，請將 `IsEnabledInDeveloperMode` 設定為 true。
> - 快照集建立速率受限於 `SnapshotsPerTenMinutesLimit` 設定。 根據預設，限制為每隔十分鐘建立一個快照集。
> - 每日可上傳的快照集不得超過 50 個。

## <a name="limitations"></a>限制

預設資料保留期為15天。 針對每個 Application Insights 實例，每天允許50個快照集的最大數目。

### <a name="publish-symbols"></a>發佈符號
快照集偵錯工具需要符號檔出現在生產環境伺服器上，才可將變數解碼並提供 Visual Studio 中的偵錯體驗。
Visual Studio 2017 的 15.2 版 (或更新版本) 在發佈至 App Service 時，依預設會發佈版本組建的符號。 在舊版中，您必須將下列這一行新增至發佈設定檔 `.pubxml` 檔案，才會在發行模式中將符號發佈：

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

針對 Azure Compute 和其他類型，請確定符號檔案與主要應用程式 .dll 位於相同資料夾 (通常為 `wwwroot/bin`)，或可在目前的路徑使用。

> [!NOTE]
> 如需有關可用之不同符號選項的詳細資訊，請參閱[Visual Studio 檔](/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
)。 為獲得最佳結果，建議使用「完整」、「可攜」或「內嵌」。

### <a name="optimized-builds"></a>最佳化的組建
在某些情況下，由於 JIT 編譯器所套用的最佳化，使版本組建無法檢視本機變數。
不過，在 Azure 應用程式服務中，快照集收集器可以將屬於其收集計劃一部分的擲回方法取消最佳化。

> [!TIP]
> 在應用程式服務中安裝 Application Insights 網站延伸模組，以取得取消最佳化支援。

## <a name="next-steps"></a>後續步驟
啟用應用程式的 Application Insights 快照偵錯工具：

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 雲端服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虛擬機器和虛擬機器擴展集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [內部部署虛擬或實體機器](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

除了 Application Insights 快照偵錯工具：
 
* [在您的程式碼中設定 Snappoint](/visualstudio/debugger/debug-live-azure-applications) 以取得快照集，而不需等待例外狀況。
* [診斷 Web Apps 中的例外狀況](./asp-net-exceptions.md)說明如何讓 Application Insights 看見更多的例外狀況。
* [智慧型偵測](./proactive-diagnostics.md)會自動探索效能異常。

