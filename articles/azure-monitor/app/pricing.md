---
title: 管理 Azure Application Insights 的使用量和成本 | Microsoft Docs
description: 在 Application Insights 中管理遙測量和監視成本。
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: DaleKoetke
ms.author: dalek
ms.date: 5/7/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 69ac1e82c267dee521143c4ed5f6c2be4d32e2ea
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531321"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>管理 Application Insights 的使用量和成本

> [!NOTE]
> 本文說明如何了解和控制 Application Insights 的成本。  [監視使用量和估計成本](../platform/usage-estimated-costs.md)這篇相關文章會說明如何針對不同的定價模型，檢視多項 Azure 監視功能的使用量和估計成本。

Application Insights 設計的目的在取得您所需的各項資源，以監視 Web 應用程式的可用性、效能及使用情況 (不論應用程式是裝載在雲端還是內部部署環境)。 Application Insights 支援熱門的語言和架構，例如 .NET、Java 以及 Node.js，並與 DevOps 流程與工具整合，例如 Azure DevOps、Jira 和 PagerDuty。 了解何種因素決定了應用程式的監視成本非常重要。 在本文中，我們會複習驅動應用程式監視成本的因素，以及如何主動監視及控制成本。

如果您有關於 Application Insights 定價方式的疑問，請在我們的 [Microsoft 問與答頁面](/answers/topics/azure-monitor.html)中張貼問題。

## <a name="pricing-model"></a>定價模式

[Azure Application Insights][start] 的價格是 **隨用隨付** 模式 (以擷取的資料量做為根據)，而且可選擇性地用於需要長時間保留資料的情況。 每項 Application Insights 資源都是個別計費的服務，並且會計入到您的 Azure 訂用帳戶帳單。 資料量是以 Application Insights 從應用程式接收的未壓縮 JSON 資料套件大小來測量。 使用[即時計量資料流](./live-stream.md)不會產生任何資料量費用。

[多重步驟 Web 測試](./availability-multistep.md)會產生額外費用。 多重步驟 Web 測試係指執行一系列動作的 Web 測試。 單一頁面的「Ping 測試」不另外收費。 針對來自 Ping 測試和多重步驟測試的遙測，收費方式與來自您應用程式的其他遙測一樣。

Application Insights 選項 [[啟用自訂計量維度的警示]](./pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) 也會產生額外的成本，因為這會額外建立預先彙總指標。 [深入了解](./pre-aggregated-metrics-log-metrics.md) Application Insights 中以記錄為基礎和預先彙總的計量，以及關於 Azure 監視器自訂計量的[價格](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="workspace-based-application-insights"></a>工作區型 Application Insights

對於將資料傳送至 Log Analytics 工作區的 Application Insights 資源 (稱為[工作區型 Application Insights 資源](create-workspace-resource.md))，資料擷取和保留的計費是由 Application Insights 資料所在的工作區來完成。 這可讓客戶利用 Log Analytics [定價模式](../platform/manage-cost-storage.md#pricing-model)的所有選項，除了「隨用隨付」以外，還包括「容量保留」。 Log Analytics 也有更多的資料保留選項，包括[依資料類型的保留期](../platform/manage-cost-storage.md#retention-by-data-type)。 工作區中的 Application Insights 資料類型會有 90 天的保留期，而不會產生任何費用。 Web 測試的使用情況與啟用自訂計量維度的警示仍會透過 Application Insights 回報。 了解如何使用[使用量和估計成本](../platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs)、[Azure 成本管理 + 計費](../platform/manage-cost-storage.md#viewing-log-analytics-usage-on-your-azure-bill)和 [Log Analytics 查詢](#data-volume-for-workspace-based-application-insights-resources)，追蹤 Log Analytics 中的資料擷取和保留成本。 

## <a name="estimating-the-costs-to-manage-your-application"></a>估算應用程式的管理成本

如果您尚未使用 Application Insights，則可以使用 [Azure 監視器定價計算機](https://azure.microsoft.com/pricing/calculator/?service=monitor)來估算 Application Insights 的使用成本。 一開始請先在 [搜尋] 方塊中輸入「Azure 監視器」，然後按一下產生的 [Azure 監視器] 圖格。 將頁面向下捲動至 Azure 監視器，然後從 [類型] 下拉式清單中選取 [Application Insights]。  您可以在這裡輸入每月預期收集的資料 GB 數，因此，問題在於 Application Insights 會收集多少資料來監視您的應用程式。

有兩種方法可以解決這種情況：使用預設的監視和調適型取樣 (可在 ASP.NET SDK 中取得)，或根據其他類似的客戶情況來估計可能的資料擷取。

### <a name="data-collection-when-using-sampling"></a>使用取樣時的資料收集

使用 ASP.NET SDK 的[調適型取樣](sampling.md#adaptive-sampling)，會自動調整資料量，使其保持在預設 Application Insights 監視的指定最大流量速率內。 如果應用程式產生的遙測量很低 (例如，在偵錯時或由於低使用量的緣故)，只要數量低於設定的每秒事件數目層級，取樣處理器就不會捨棄項目。 對於大量應用程式，預設閾值為每秒五個事件，調適型取樣會將每日事件數目限制為 432,000。 使用一般的平均事件大小 1 KB，這相當於每個託管您應用程式的節點，每 31 天的月份遙測量為 13.4 GB (因為取樣是在每個節點的本機執行。) 

對於不支援調適型取樣的軟體開發套件，您可以採用[擷取取樣](./sampling.md#ingestion-sampling)，當 Application Insights 收到資料時，根據要保留的資料百分比，或[適用於 ASP.NET、ASP.NET Core 和 Java 網站的固定取樣率](sampling.md#fixed-rate-sampling)，來進行取樣，藉此減少從網頁伺服器和網頁瀏覽器傳送的流量

### <a name="learn-from-what-similar-customers-collect"></a>從類似客戶收集內容中了解

在適用於 Application Insights 的 Azure 監視定價計算機中，如果您啟用「根據應用程式活動估算資料量」功能，您可以提供應用程式的相關輸入 (如果您要收集用戶端遙測，則可提供每月要求和每月頁面檢視次數)，然後計算機會讓您知道類似的應用程式所收集資料量的中位數和第 90 個百分位數。 這些應用程式跨越 Application Insights 設定的範圍 (例如，有些有預設的[取樣](./sampling.md)，有些沒有取樣等等)，因此，您仍然可以使用取樣，控制將擷取的資料量減少到遠低於中位數層級以下。 但這是了解其他類似客戶看法的起點。

## <a name="understand-your-usage-and-estimate-costs"></a>了解您的使用量並估算成本

Application Insights 可讓您根據最近的使用模式，輕鬆了解可能產生的成本。 若要開始，請在 Azure 入口網站中，針對 Application Insights 資源，移至 [使用量和估計成本] 頁面：

![選擇價格](./media/pricing/pricing-001.png)

A. 檢閱當月的資料量。 這包括從您的伺服器和用戶端應用程式，以及從可用性測試接收並保留的所有資料 (在任何[取樣](./sampling.md)之後)。  
B. [多重步驟的 web 測試](./availability-multistep.md)會另外收費。 (這不包括簡單的可用性測試，其已包含在資料量費用中。)  
C. 檢視上個月的資料量趨勢。  
D. 啟用資料擷取[取樣](./sampling.md)。
E. 設定每日資料量上限。  

(請注意，本文中螢幕擷取畫面顯示的所有價格僅供範例使用。 如需屬於您貨幣與區域的目前定價，請參閱 [Application Insights 價格][pricing]。)

若要更深入調查 Application Insights 的使用量，請開啟 [計量] 頁面，新增名為「資料點量」的計量，然後選取 [套用分割] 選項，以根據「遙測項目類型」拆分資料。

Application Insights 費用會加到您的 Azure 帳單中。 您可以在 Azure 入口網站的 [成本管理 + 計費] 區段中，或在 [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)中，查看您的 Azure 帳單詳細資料。  如需使用 Application Insights 的詳細資料，[請參閱下列內容](#viewing-application-insights-usage-on-your-azure-bill)。 

![在左側功能表中，選取 [帳務]](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>使用資料量計量
<a id="understanding-ingested-data-volume"></a>

若要深入了解您的資料量，請選取 Application Insights 資源的 **計量**，然後新增新的圖表。 對於圖表計量，請在 [以記錄為基礎的計量] 下，選取 [資料點量]。 按一下 [套用分割]，並依 [`Telemetryitem` 類型] 選取群組。

![使用計量以查看資料量](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>了解資料量詳細資料的查詢

有兩種方法可以調查 Application Insights 的資料量。 第一個會使用 `systemEvents` 資料表中的彙總資訊，而第二個使用 `_BilledSize` 屬性，這在每個內嵌事件上都有提供。 `systemEvents` 不會有 [workspace-based-application-insights](#data-volume-for-workspace-based-application-insights-resources) 的資料大小資訊。

#### <a name="using-aggregated-data-volume-information"></a>使用彙總資料量資訊

例如，您可以使用 `systemEvents` 資料表，透過查詢來查看過去 24 小時內擷取的資料量：

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

或者，若要查看過去 30 天內依資料類型分類的資料量 (以位元組為單位)，您可以使用：

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

請注意，您可以在 [Azure 記錄警示](../platform/alerts-unified-log.md)中使用此查詢，以設定資料量的警示。  

若要深入了解您的遙測資料變更，我們可以使用查詢，依類型取得事件計數：

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>使用每個事件的資料大小資訊

若要深入了解資料量的來源，您可以使用出現在每個內嵌事件上的 `_BilledSize` 屬性。

例如，若要查看過去 30 天內產生最多資料量的作業，我們可以加總所有相依性事件的 `_BilledSize`：

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>工作區型 Application Insights 資源的資料量

若要查看上週工作區中所有[工作區型 Application Insights 資源](create-workspace-resource.md) 的資料量趨勢，請前往 Log Analytics 工作區，並執行查詢：

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

若要針對以特定工作區型 Application Insights 資源，依類型來查詢資料量趨勢，請在 Log Analytics 工作區中使用：

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>在您的 Azure 帳單上查看 Application Insights 使用量

Azure 在 [Azure 成本管理 + 計費](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json)中樞內提供了許多實用的功能。 例如，「成本分析」功能可讓您檢視 Azure 資源的花費。 依資源類型新增篩選條件 (新增至 Application Insights 的 microsoft.insights/components) 可讓您追蹤費用。 然後在 [群組依據] 中選取 [計量類別] 或 [計量]。  對於目前價格方案中的 Application Insights 資源，由於所有 Azure 監視器元件都有單一記錄後端，因此大部分使用量會顯示為「計量」類別的 Log Analytics。 

若要更加了解您的使用量，請[從 Azure 入口網站下載使用量](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal)。
在下載的試算表中，您可以看到每天每個 Azure 資源的使用量。 在此 Excel 試算表中，您可以藉由先篩選「計量類別」資料行來顯示「Application Insights」和「Log Analytics」，然後在「包含 microsoft.insights/components」的「執行個體識別碼」資料行上新增篩選條件，來找到您的 Application Insights 資源使用量。  由於所有 Azure 監視器元件都有單一記錄後端，因此，大部分 Application Insights 使用量都會以 Log Analytics 計量類別的計量報告。  只有舊版定價層和多重步驟 Web 測試的 Application Insights 資源，會以 Application Insights 的計量類別來報告。  使用量會顯示在 [已取用的數量] 資料行，每個項目的單位則會顯示在 [測量單位] 資料行。  有更多詳細資料可協助您[了解 Microsoft Azure 帳單](../../cost-management-billing/understand/review-individual-bill.md)。

## <a name="managing-your-data-volume"></a>管理您的資料量

您可以使用下列技術來管理您傳送的資料量：

* **取樣**：您可以使用取樣來減少從伺服器和用戶端應用程式傳送的遙測量，這對計量的扭曲程度最小。 取樣是您可用來調整所傳送資料量的主要工具。 深入了解[取樣功能](./sampling.md)。

* **限制 Ajax 呼叫**：您可以 [限制可回報的 Ajax 呼叫次數](./javascript.md#configuration) (在每個頁面檢視中)，或關閉 Ajax 報告功能。

* **停用不必要的模組**：[編輯 ApplicationInsights.config](./configuration-with-applicationinsights-config.md)以關閉您不需要的集合模組。 例如，您可能會決定效能計數器或相依性資料是不必要的。

* **預先彙總計量**：如果您在應用程式中呼叫 TrackMetric，便可以使用接受一批測量之平均及標準差計算的多載來減少流量。 或者，您也可以使用[預先彙總套件](https://www.myget.org/gallery/applicationinsights-sdk-labs)。
 
* **每日上限**：在 Azure 入口網站中建立 Application Insights 資源時，每日上限會設定為每日 100 GB。 在 Visual Studio 中建立 Application Insights 資源時，預設值很小 (只有每日 32.3 MB)。 每日上限預設值的設定是為了協助測試。 這是預期使用者在將應用程式部署至生產環境之前，會提高每日上限。 

    除非您針對高流量的應用程式要求更高的最大值，否則每日的最高上限是 1,000 GB。
    
    關於每日上限的警告電子郵件會針對您的 Application Insights 資源，傳送給下列角色的成員帳戶：「ServiceAdmin」、「AccountAdmin」、「CoAdmin」、「Owner」。

    設定每日上限時，請務必小心。 您的目的應該是「一律不要達到每日上限」。 如果達到每日上限，就會失去當天其餘時間的資料，而無法監視應用程式。 若要變更每日上限，請使用 [每日用量上限] 選項。 您可以在 [使用量和估計成本] 窗格中存取此選項 (本文稍後會有更詳細的說明)。
    
    我們已移除某些訂用帳戶類型的信用額度無法用於 Application Insights 的限制。 先前，如果訂用帳戶有消費限制，則 [每日上限] 對話方塊中會有指示，說明如何移除消費限制並讓每日上限提高到每日 32.3 MB 以上。
    
* **節流**：節流會將資料速率限制在每秒 32,000 個事件 (每個檢測金鑰以 1 分鐘計算平均值)。 系統會每分鐘評估應用程式傳送的資料量。 如果每秒速率超過每分鐘平均值，伺服器會拒絕部分要求。 SDK 會緩衝處理資料，然後嘗試重新傳送它。 它會在數分鐘的期間內散佈大量資料。 如果應用程式始終以高於節流速率的方式傳送資料，有些資料會就遭到捨棄。 (ASP.NET、Java 和 JavaScript SDK 會嘗試以此方式重新傳送資料，其他 SDK 則可能直接捨棄節流的資料)。當節流發生時，會有通知警告來警示您已發生這種情況。

## <a name="manage-your-maximum-daily-data-volume"></a>管理您的每日最大資料量

您可以使用每日用量上限來限制所收集的資料。 不過，如果達到上限，就會失去當天其餘時間從您應用程式傳送的所有遙測資料。 建議您「不要」讓應用程式達到每日上限。 一旦應用程式達到每日上限，您便無法追蹤應用程式的健康情況和效能。

請不要使用每日用量上限，而是改用[取樣](./sampling.md)將資料量調整到您想要的程度。 然後，只使用每日上限作為應付萬一應用程式開始未預期地傳送大量遙測時的「最後手段」。

### <a name="identify-what-daily-data-limit-to-define"></a>識別要定義的每日資料限制

檢閱 Application Insights 使用量和估計成本，以了解資料擷取趨勢及要定義的每日資料量上限。 請謹慎考量，因為達到限制之後，您將無法監視您的資源。

### <a name="set-the-daily-cap"></a>設定每日上限

若要變更每日上限，請在 Application Insights 資源的 [設定] 區段中，從 [使用量和估計成本] 頁面中選取 [每日上限]。

![調整每日遙測資料量上限](./media/pricing/pricing-003.png)

若要[透過 Azure Resource Manager 變更每日上限](./powershell.md)，要變更的屬性為 `dailyQuota`。  透過 Azure Resource Manager，您也可以設定 `dailyQuotaResetTime` 和每日上限的 `warningThreshold`。

### <a name="create-alerts-for-the-daily-cap"></a>建立每日上限的警示

當內嵌資料量達到警告層級或每日上限層級時，Application Insights 每日上限會在 Azure 活動記錄中建立事件。  您可以[根據活動記錄事件建立警示](../platform/alerts-activity-log.md#create-with-the-azure-portal)。 這些事件的訊號名稱包括：

* 已達到 Application Insights 元件每日上限警告閾值

* 已達到 Application Insights 元件每日上限

## <a name="sampling"></a>取樣
[取樣](./sampling.md) 是一種方法，可減少將遙測傳送至應用程式的速率，同時保留在診斷搜尋期間尋找相關事件的能力。 此外，也保留正確的事件計數。

取樣可有效減少費用並維持在每月配額內。 取樣演算法會保留相關的遙測項目，因此，例如在使用 [搜尋] 時，您便可以找到與特定例外狀況相關的要求。 此演算法也會保留正確的計數，因此您在 [計量瀏覽器] 中會看到要求率、例外狀況率及其他計數的正確值。

取樣有數種形式。

* [調適型取樣](./sampling.md)是 ASP.NET SDK 的預設值。 調適型取樣會自動針對應用程式所傳送的遙測量進行調整。 這會自動在 Web 應用程式的 SDK 中運作，以便降低網路上的遙測流量。 
*  是替代方法，它會在遙測從應用程式進入 Application Insights 服務時運作。 擷取取樣不會影響從應用程式傳送的遙測量，但可減少服務所保留的量。 您可以使用擷取取樣來減少被來自瀏覽器及其他 SDK 的遙測用光的配額。

若要設定擷取取樣，請移至 [定價] 窗格：

![在 [配額和定價] 窗格中，選取 [範例] 圖格，然後選取一個取樣分數](./media/pricing/pricing-004.png)

> [!WARNING]
> [資料取樣] 窗格只會控制擷取取樣的值。 它不會反映應用程式中 Application Insights SDK 所套用的取樣率。 如果已在 SDK 中對連入遙測進行取樣，就不會套用擷取取樣。
>

若要探索實際的取樣率，而不論其套用位置是哪裡，請使用[分析查詢](../log-query/log-query-overview.md)。 此查詢看起來像這樣：

```kusto
requests | where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h)
| render areachart
```

在每個保留的記錄中，`itemCount` 會指出它所代表的原始記錄數目。 它等於 1 + 先前捨棄的記錄數目。

## <a name="change-the-data-retention-period"></a>變更資料保留期

Application Insights 資源的預設保留期為 90 天。 可以為每個 Application Insights 資源選取不同的保留期間。 一組完整的可用保留期間為30、60、90、120、180、270、365、550 或 730 天。 [深入了解](https://azure.microsoft.com/pricing/details/monitor/)較長資料保留期的價格。 

若要變更保留期，請從 Application Insights 資源移至 [使用量和估計成本] 頁面，然後選取 [資料保留] 選項：

![顯示資料保留期限變更位置的螢幕擷取畫面。](./media/pricing/pricing-005.png)

縮短保留期的時候，在移除最舊的資料之前會有幾天的寬限期。

您也可以使用 `retentionInDays` 參數，[使用 PowerShell 以程式設計方式設定](powershell.md#set-the-data-retention)保留期。 如果您將資料保留期設定為 30 天，則可以使用 `immediatePurgeDataOn30Days` 參數來觸發立即清除較舊的資料，這對合規性相關案例可能會有幫助。 此清除功能僅透過 Azure Resource Manager 公開，而且應該小心使用。 可以使用 Azure Resource Manager 設定 `dailyQuotaResetTime` 參數，以設定資料量上限的每日重設時間。

## <a name="data-transfer-charges-using-application-insights"></a>使用 Application Insights 的資料傳輸費用

將資料傳送至 Application Insights 可能會產生資料頻寬費用。 如 [Azure 頻寬定價頁面](https://azure.microsoft.com/pricing/details/bandwidth/)所述，在位於兩個區域的 Azure 服務之間資料傳輸時，會依正常費率向輸出資料傳輸收費。 輸入資料傳輸則是免費的。 不過，相較於 Application Insights 的資料擷取成本，這項費用很低 (幾 %)。 因此，若要控制 Log Analytics 的成本就必須專注在已擷取的資料量上，為了協助您了解這方面的內容，[這裡](#managing-your-data-volume)有指導方針。

## <a name="limits-summary"></a>限制摘要

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>停用每日上限電子郵件

若要停用每日數量上限電子郵件，請在 Application Insights 資源的 [設定] 區段之下，從 [使用量和估計成本] 窗格選取 [每日上限]。 此處有設定可在達到上限時，以及已達到可調整的警告層級時傳送電子郵件。 如果您想停用所有每日上限數量相關的電子郵件，請取消核取這兩個方塊。

## <a name="legacy-enterprise-per-node-pricing-tier"></a>舊版「企業」(每節點) 定價層

對於 Azure Application Insights 的早期採用者，還有兩種可能的定價層：基本和企業。 「基本」定價層如上所述，而且為預設層。 它包含所有企業層功能，不需要額外費用。 「基本」層主要是針對內嵌的資料量計費。

> [!NOTE]
> 這些舊版定價層已重新命名。 企業定價層現在稱為 **每節點**，而基本定價層現在稱為 **每 GB**。 這些新名稱會在下列項目和 Azure 入口網站中使用。  

「每節點」(先前稱為「企業」) 層會針對每個節點計費，而每個節點會收到每日資料額度。 在「每節點」定價層中，您要對超過內含資料額度的內嵌資料付費。 如果您使用 Operations Management Suite，您應該選擇「每節點」層。

如需屬於您貨幣與區域的目前定價，請參閱 [Application Insights 定價](https://azure.microsoft.com/pricing/details/application-insights/)。

> [!NOTE]
> 我們在 2018 年 4 月[推出](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)新的 Azure 監視定價模型。 這個模型在完整的監視服務組合之間採用簡單的「隨用隨付」模型。 深入了解[新的定價模型](../platform/usage-estimated-costs.md)、如何根據您的使用模式[評估移到這個模型的影響](../platform/usage-estimated-costs.md#understanding-your-azure-monitor-costs)，以及[如何加入新的模型](../platform/usage-estimated-costs.md#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>「每節點」層和 Operations Management Suite 訂閱的權利

如[先前所宣布](/archive/blogs/msoms/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription)，購買 Operations Management Suite E1 和 E2 的客戶可以取得 Application Insights「每節點」做為額外的元件，無須額外付費。 具體來說，Operations Management Suite E1 和 E2 的每個單位皆包含一個 Application Insights「每節點」層節點的權利。 每個 Application Insights 節點包含每天最多 200 MB 擷取的資料 (與 Log Analytics 資料擷取分開計算)，資料可保留 90 天而無須額外付費。 本文稍後會更詳細說明此階層。

由於此階層僅適用於具有 Operations Management Suite 訂閱的客戶，因此沒有 Operations Management Suite 訂閱的客戶不會看到可選取此階層的選項。

> [!NOTE]
> 為了確保您取得此權利，您的 Application Insights 資源必須在「每節點」定價層中。 此權利只能以節點為單位套用。 「每 GB」層中的 Application Insights 資源無法實現任何效益。 在 [使用量和估計成本] 窗格中所顯示的估計成本中看不到此權利。 此外，如果您將訂閱移轉至 2018 年 4 月的新 Azure 監視定價模型，「每 GB」層將會是唯一可用的階層。 如果您擁有 Operations Management Suite 訂用帳戶，建議您不要將訂用帳戶移轉至新的 Azure 監視定價模型。

### <a name="how-the-per-node-tier-works"></a>「每節點」層的運作方式

* 在「每節點」層中，是依照傳送所有應用程式遙測的每一節點付費。
  * 「節點」是裝載應用程式的實體或虛擬伺服器機器，或是平台即服務 (PaaS) 角色執行個體。
  * 開發用機器、用戶端瀏覽器及行動裝置不算節點。
  * 如果您的應用程式有數個會傳送遙測的元件，例如 Web 服務和後端背景工作角色，則會分開計算元件。
  * [即時計量串流](./live-stream.md)資料不會計入價格用途。 訂用帳戶的收費是依照每一節點，而非每一應用程式。 如果您有五個節點傳送 12 個應用程式的遙測，則是以五個節點計算收費。
* 雖然費用是按月報價，您的收費僅適用於一個節點從一個應用程式傳送遙測的任何小時。 每小時的費用是月費報價除以 744 (一個月 31 天的小時數)。
* 每個節點 (資料粒度為小時) 偵測每日 200 MB 的資料量配置。 未使用的資料配置不會累計到隔天。
  * 如果您選擇「每節點」定價方案，每個訂閱會根據傳送遙測資料至該訂閱中 Application Insights 資源的節點數，得到每日資料額度。 因此，如果您有 5 個節點全天候傳送資料，您會有合起來 1GB 的資料額度，套用至該訂用帳戶中的所有 Application Insights 資源。 由於內含資料會在所有節點之間共用，因此特定節點所傳送的資料是否超過其他節點並無妨。 如果某一天，Application Insights 資源收到超過此訂閱的每日資料配置，則會按 GB 收取資料超量費用。 
  * 每日資料額度的計算方式，是每個節點在當天傳送遙測資料的時數 (使用 UTC) 除以 24 再乘以 200 MB。 因此，如果您有四個節點在一天 24 個小時的 15 個小時內傳送遙測資料，則當天內含資料量會是 ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB。 以資料超量每一 GB 的價格是 2.30 美元來說，如果節點那一天會傳送 1 GB 的資料，費用就是 1.15 美元。
  * 「每節點」層的每日額度並不與已選擇為「每 GB」層的應用程式共用。 未使用額度不會逐日累計。

### <a name="examples-of-how-to-determine-distinct-node-count"></a>如何判斷相異節點計數的範例

| 狀況                               | 每日節點總數 |
|:---------------------------------------|:----------------:|
| 1 個應用程式使用 3 個 Azure App Service 執行個體和 1 個虛擬伺服器 | 4 |
| 3 個應用程式在 2 個虛擬機器上執行，而這些應用程式的 Application Insights 資源位於相同的訂閱中，且採用「每節點」層 | 2 | 
| 4 個應用程式的 Application Insights 資源位於相同訂用帳戶中；每個應用程式在離峰的 16 小時期間執行 2 個執行個體，在尖峰的 8 小時期間執行 4 個執行個體 | 13.33 |
| 雲端服務有 1 個背景工作角色和 1 個 Web 角色，各執行 2 個執行個體 | 4 | 
| 5 個節點的 Azure Service Fabric 叢集執行 50 個微服務，每個微服務執行 3 個執行個體 | 5|

* 節點的精確計算取決於您的應用程式使用哪個 Application Insights SDK。 
  * 在 SDK 2.2 版和更新版本中，Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 和 [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 都會將每個應用程式主機報告為節點。 範例包括實體伺服器和虛擬機器主機的電腦名稱，或雲端服務的執行個體名稱。  唯一的例外狀況是僅使用 [.NET Core](https://dotnet.github.io/) 和 Application Insights Core SDK 的應用程式。 在此情況下，因為主機名稱無法使用，所以只會針對所有主機報告一個節點。
  * 對於較早版本的 SDK，[Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 的行為就和較新版的 SDK 一樣，不過不論應用程式主機的數目是多少，[Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 都只會報告一個節點。
  * 如果您的應用程式使用 SDK 來將 **roleInstance** 設為自訂值，依預設將使用那個相同的值來判斷節點計數。
  * 如果您使用新的 SDK 版本，搭配從用戶端機器或行動裝置執行的應用程式，則節點計數可能會傳回很大的數字 (因為有大量的用戶端機器或行動裝置)。

## <a name="automation"></a>自動化

您可以使用 Azure Resource Management 來撰寫指令碼以設定定價層。 [了解作法](powershell.md#price)。

## <a name="next-steps"></a>後續步驟

* [採樣](./sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ./app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/

