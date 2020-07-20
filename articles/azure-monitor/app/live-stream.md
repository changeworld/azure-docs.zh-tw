---
title: 使用即時計量資料流 Azure 應用程式見解進行診斷
description: 透過自訂計量即時監視您的 Web 應用程式，並透過失敗、追蹤和事件的即時摘要診斷問題。
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: e554595a7a88e1455f7426636dc69db99a7d3e94
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86166479"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>即時計量資料流︰以 1 秒的延遲進行監視與診斷

使用[Application Insights](../../azure-monitor/app/app-insights-overview.md)中的即時計量資料流，監視即時、生產中的 web 應用程式。 選取並篩選要即時監看的計量和效能計數器，而不會對您的服務造成任何干擾。 檢查失敗要求和例外狀況範例中的堆疊追蹤。 與[Profiler](../../azure-monitor/app/profiler.md)和[快照偵錯工具](../../azure-monitor/app/snapshot-debugger.md)搭配使用，即時計量資料流為您的即時網站提供強大且不具侵入性的診斷工具。

您可以使用即時計量資料流：

* 藉由監看效能和失敗計數，在發行修正程式時進行驗證。
* 監看測試負載的影響，並即時診斷問題。
* 藉由選取並篩選您想要監看的計量，專注於特定測試工作階段或篩選出已知問題。
* 在發生例外狀況時取得其追蹤。
* 試驗篩選，以尋找最相關的 KPI。
* 即時監看任何 Windows 效能計數器。
* 輕鬆識別有問題的伺服器，並將所有 KPI/即時摘要篩選到只有該伺服器。

![[即時計量] 索引標籤](./media/live-stream/live-metric.png)

目前支援 ASP.NET、ASP.NET Core、Azure Functions、JAVA 和 Node.js 應用程式的即時計量。

## <a name="get-started"></a>開始使用

1. 在您的應用程式中[安裝 Application Insights](../../azure-monitor/azure-monitor-app-hub.yml) 。
2. 除了標準 Application Insights 套件之外，還需要 [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/)，才能啟用「即時計量」串流。
3. **更新至最新版本**的 Application Insights 套件。 在 Visual Studio 中，以滑鼠右鍵按一下您的專案，然後選擇 [**管理 NuGet 封裝**]。 開啟 [更新]**** 索引標籤，然後選取所有 Microsoft.ApplicationInsights.* 套件。

    重新部署您的應用程式。

3. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您應用程式的 Application Insights 資源，然後開啟 [即時資料流]。

4. 如果您可能在篩選中使用客戶名稱等敏感性資料，請[保護控制通道](#secure-the-control-channel)。

### <a name="no-data-check-your-server-firewall"></a>沒有資料？ 請檢查您的伺服器防火牆

檢查是否已開啟您伺服器防火牆中[即時計量資料流的連出連接埠](../../azure-monitor/app/ip-addresses.md#outgoing-ports)。

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>即時計量資料流與計量瀏覽器和分析有何不同？

| |即時資料流 | 計量瀏覽器和分析 |
|---|---|---|
|**延遲**|在一秒內顯示資料|在幾分鐘後進行彙總|
|**沒有保留**|資料在圖表上就會保存，之後便會捨棄該資料|[資料會保留 90 天](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|**隨選**|只有在 [即時計量] 窗格開啟時，才會串流處理資料 |每當安裝並啟用 SDK 時都會傳送資料|
|**免費**|即時資料流資料免費|依[價格](../../azure-monitor/app/pricing.md)付費
|**取樣**|傳輸所有選取的計量和計數器。 取樣失敗和堆疊追蹤。 不會套用 TelemetryProcessors。|可能[取樣](../../azure-monitor/app/api-filtering-sampling.md)事件|
|**控制通道**|篩選控制項訊號會傳送至 SDK。 建議您保護這個通道。|通訊是以單向方式前往入口網站|

## <a name="select-and-filter-your-metrics"></a>選取並篩選您的計量

(適用於 ASP.NET、ASP.NET Core 及 Azure Functions (v2))。

您可以從入口網站將任何篩選條件套用在任何 Application Insights 遙測上，以便即時監視自訂 KPI。 按一下您將滑鼠移過任何圖表時所顯示的篩選條件控制項。 下列圖表是使用 URL 和 Duration 屬性的篩選條件來繪製自訂要求計數 KPI。 利用可顯示符合您在任一時間點所指定準則之遙測即時摘要的「串流預覽」區段來驗證您的篩選條件。

![篩選要求率](./media/live-stream/filter-request.png)

您可以監視與計數不同的值。 選項取決於串流的類型，可能是任何的 Application Insights 遙測︰要求、相依性、例外狀況、追蹤、事件或計量。 它可以是您自己的[自訂測量](../../azure-monitor/app/api-custom-events-metrics.md#properties)：

![具有自訂計量之要求速率的查詢產生器](./media/live-stream/query-builder-request.png)

除了 Application Insights 遙測之外，您也可以監視任何 Windows 效能計數器，方法是從串流選項中選取，並提供效能計數器的名稱。

即時計量會在兩個地方進行彙總︰在每一部伺服器上本機進行，以及在所有伺服器上進行。 您可以選取個別下拉式清單中的其他選項，在任一位置變更預設。

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>範例遙測︰自訂即時診斷事件
依預設，事件的即時摘要會顯示失敗要求和相依性呼叫、例外狀況、事件以及追蹤的範例。 按一下篩選圖示可查看任一時間點套用的準則。

![[篩選] 按鈕](./media/live-stream/filter.png)

如同計量，您可以將任意準則指定為任何的 Application Insights 遙測類型。 在此範例中，我們會選取 [特定的要求失敗] 和 [事件]。

![查詢產生器](./media/live-stream/query-builder.png)

> [!NOTE]
> 目前針對以例外狀況訊息為基礎的準則，請使用最外部的例外狀況訊息。 在上述範例中，若要使用內部例外狀況訊息 (後接 "<--" 分隔符號)「用戶端中斷連線。」將良性的例外狀況篩選掉， 請使用不包含「讀取要求內容時發生錯誤」準則的訊息。

按一下即時摘要中的項目，以查看它的詳細資料。 您可以按一下 [暫停]**** 或只向下捲動，或是按一下項目來將摘要暫停。 在您捲動回到頂端後，或是按一下暫停時所收集到的項目計數器，即時摘要就會繼續進行。

![取樣的即時失敗](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>依伺服器執行個體篩選

如果您想要監視特定伺服器角色執行個體，可以依伺服器篩選。 若要篩選，請選取 [*伺服器*] 下的伺服器名稱。

![取樣的即時失敗](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>保護控制通道

> [!NOTE]
> 目前，您只能使用程式碼基底監視來設定已驗證的通道，而且無法使用無程式碼 attach 來驗證服務器。

您指定的自訂篩選條件準則會傳回給 Application Insights SDK 中的即時計量元件。 篩選條件可能會包含機密資訊，例如 customerIDs。 除了檢測金鑰之外，您還可以利用祕密 API 金鑰來保護頻道安全。
### <a name="create-an-api-key"></a>建立 API 金鑰

![API 金鑰 > [建立 API 金鑰 ](./media/live-stream/api-key.png)
 ![ ] [建立 api 金鑰] 索引標籤。選取 [驗證 SDK 控制通道]，然後按一下 [產生金鑰]](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>將 API 金鑰新增至設定

### <a name="classic-aspnet"></a>傳統 ASP.NET

在 applicationinsights.config 檔案中，將 AuthenticationApiKey 新增至 QuickPulseTelemetryModule：
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
或在程式碼中，在 QuickPulseTelemetryModule 上設定它：

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Azure 函數應用程式

針對 Azure 函式應用程式 (v2) ，使用環境變數可以完成使用 API 金鑰來保護通道的安全。

請從 Application Insights 資源內部建立 API 金鑰，然後前往您「函數應用程式」的 [應用程式設定]****。 選取 [新增設訂]****，然後輸入名稱 `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` 及與您 API 金鑰對應的值。

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-or-greater"></a>ASP.NET Core (需要 Application Insights ASP.NET Core SDK 2.3.0 或更高版本) 

修改您的 startup.cs 檔案，如下所示：

第一次新增

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

然後在 ConfigureServices 方法內新增：

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

不過，如果您認得並信任所有連線的伺服器，則無需透驗證的頻道就可以嘗試自訂篩選器。 這個選項有六個月可供使用。 一旦每個新的工作階段或是新的伺服器上線後，就需要此覆寫。

![即時計量驗證選項](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>我們強烈建議您在篩選條件準則中輸入潛在的機密資訊 (例如 CustomerID) 之前，先設定驗證的頻道。
>

## <a name="supported-features-table"></a>支援的功能資料表

| 語言                         | 基本計量       | 效能度量 | 自訂篩選    | 範例遙測    | 依進程分割的 CPU |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | 支援的 (V 2.7.2 +)  | 支援的 (V 2.7.2 +)  | 支援的 (V 2.7.2 +)  | 支援的 (V 2.7.2 +)  | 支援的 (V 2.7.2 +)   |
| .NET Core (target =. NET Framework) | 支援的 (V 2.4.1 +)  | 支援的 (V 2.4.1 +)  | 支援的 (V 2.4.1 +)  | 支援的 (V 2.4.1 +)  | 支援的 (V 2.4.1 +)   |
| .NET Core (目標 = NET Core)      | 支援的 (V 2.4.1 +)  | 支援*          | 支援的 (V 2.4.1 +)  | 支援的 (V 2.4.1 +)  | **不受支援**    |
| Azure Functions v2               | 支援           | 支援           | 支援           | 支援           | **不受支援**    |
| Java                             | 支援的 (V 2.0.0 +)  | 支援的 (V 2.0.0 +)  | **不受支援**   | **不受支援**   | **不受支援**    |
| Node.js                          | 支援的 (V 1.3.0 +)  | 支援的 (V 1.3.0 +)  | **不受支援**   | 支援的 (V 1.3.0 +)  | **不受支援**    |

基本計量包括要求、相依性和例外狀況速率。 效能計量 (效能計數器) 包含記憶體和 CPU。 範例遙測會顯示失敗要求和相依性、例外狀況、事件和追蹤的詳細資訊串流。

 \*PerfCounters 支援與不是以 .NET Framework 為目標的 .NET Core 版本稍有不同：

- 在 Windows Azure App Service 中執行時，支援 PerfCounters 度量。  (AspNetCore SDK Version 2.4.1 或更高版本) 
- 當應用程式在任何 Windows 電腦 (VM、雲端服務或內部內部部署等中執行時，支援 PerfCounters。 )  (AspNetCore SDK 版本2.7.1 或更高的) ，但適用于以 .NET Core 2.0 或更新版本為目標的應用程式。
- 當應用程式在 (Linux、Windows、適用于 Linux 的 app service、容器等的任何位置執行時，會支援 PerfCounters，而在最新的 Beta (中 ) ，亦即 AspNetCore SDK 版本 2.8.0-Beta1 或更高) ，但適用于以 .NET Core 2.0 或更新版本為目標的應用程式。

預設會停用 Node.js SDK 中的即時計量。 若要啟用即時計量，請在 `setSendLiveMetrics(true)` 初始化 SDK 時，將新增至您的設定[方法](https://github.com/Microsoft/ApplicationInsights-node.js#configuration)。

## <a name="troubleshooting"></a>疑難排解

沒有資料？ 如果您的應用程式位於受保護的網路中：即時計量資料流使用與其他 Application Insights 遙測不同的 IP 位址。 請確定[這些 IP 位址](../../azure-monitor/app/ip-addresses.md)在您的防火牆中為開啟狀態。

## <a name="next-steps"></a>後續步驟
* [使用 Application Insights 監視使用情況](../../azure-monitor/app/usage-overview.md)
* [使用診斷搜尋](../../azure-monitor/app/diagnostic-search.md)
* [分析工具](../../azure-monitor/app/profiler.md)
* [快照集偵錯工具](../../azure-monitor/app/snapshot-debugger.md)
