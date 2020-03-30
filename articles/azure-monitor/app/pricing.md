---
title: 管理 Azure Application Insights 的使用量和成本 | Microsoft Docs
description: 在 Application Insights 中管理遙測量和監視成本。
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 11/27/2019
ms.reviewer: mbullwin
ms.openlocfilehash: b782477fd29b34eda70813fc2aff29157f02acb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275941"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>管理 Application Insights 的使用量和成本

> [!NOTE]
> 本文介紹如何理解和控制應用程式見解的成本。  相關文章"[監視使用方式和估計成本](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs)"介紹了如何查看不同定價模型的多個 Azure 監視功能的使用方式和估計成本。

應用程式見解旨在獲取監視 Web 應用程式的可用性、性能和使用方式所需的一切，無論這些應用程式託管在 Azure 上還是本地。 應用程式見解支援常用語言和框架，如 .NET、JAVA 和 Node.js，並與 DevOps 進程和工具（如 Azure DevOps、Jira 和 PagerDuty）集成。 瞭解哪些決定監視應用程式的成本非常重要。 在本文中，我們將回顧是什麼驅動您的應用程式監控成本，以及如何主動監視和控制它們。

如果您有關於 Application Insights 定價運作方式的疑問，請在我們的[論壇](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc)中張貼問題。

## <a name="pricing-model"></a>定價模式

[Azure 應用程式見解][start]的定價是基於引入的資料量的**即用即付**模型，並可選擇用於延長資料保留時間。 每項 Application Insights 資源都是個別計費的服務，並且會計入到您的 Azure 訂用帳戶帳單。 資料量是以 Application Insights 從應用程式接收的未壓縮 JSON 資料套件大小來測量。 使用[即時指標流](../../azure-monitor/app/live-stream.md)不收取資料量。

[多重步驟 Web 測試](../../azure-monitor/app/availability-multistep.md)會產生額外費用。 多重步驟 Web 測試係指執行一系列動作的 Web 測試。 單一頁面的「Ping 測試」** 不另外收費。 針對來自 Ping 測試和多重步驟測試的遙測，收費方式與來自您應用程式的其他遙測一樣。

## <a name="estimating-the-costs-to-manage-your-application"></a>估算管理應用程式的成本

如果尚未使用應用程式見解，則可以使用[Azure 監視器定價計算機](https://azure.microsoft.com/pricing/calculator/?service=monitor)來估計使用應用程式見解的成本。 首先在"搜索"框中輸入"Azure 監視器"，然後按一下生成的 Azure 監視器磁貼。 向下滾動頁面到 Azure 監視器，然後從"類型"下拉清單中選擇應用程式見解。  在這裡，您可以輸入您每月要收集的 GB 資料數，因此問題是應用程式見解將收集多少資料來監視您的應用程式。

有兩種方法可以解決此問題：使用預設監視和自我調整採樣（這在 ASP.NET SDK 中可用，或者根據其他類似客戶所看到的情況估計可能的資料引入。

### <a name="data-collection-when-using-sampling"></a>使用採樣時的資料收集

通過ASP.NET SDK 的[自我調整採樣](sampling.md#adaptive-sampling)，資料量會自動調整，以保持在指定的最大流量範圍內，以便預設應用程式見解監控。 如果應用程式生成的遙測量較低（例如調試時或由於使用率低），則只要卷低於每秒配置的事件級別，採樣處理器就不會刪除專案。 對於高容量應用程式，預設閾值為每秒五個事件，自我調整採樣將限制每日事件數為 432，000 個。 使用典型的平均事件大小為 1 KB，這相當於每個託管應用程式的節點每個 31 天月 13.4 GB 的遙測資料（因為採樣是本地執行的每個節點的）。 

對於不支援自我調整採樣的 SDK，可以使用[引入採樣](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling)，應用程式見解根據要保留的資料百分比接收資料時進行採樣，或者[對 ASP.NET、ASP.NET核心和 JAVA 網站進行固定利率採樣](sampling.md#fixed-rate-sampling)，以減少從 Web 服務器和 Web 瀏覽器發送的流量

### <a name="learn-from-what-similar-customers-collect"></a>瞭解類似客戶收集的內容

在應用程式見解的 Azure 監視定價計算機中，如果啟用"基於應用程式活動估計資料量"功能，則可以提供有關應用程式的輸入（每月請求和每月頁面流覽次數，以防您將收集用戶端遙測），然後計算機將告訴您類似應用程式收集的資料的中位數和 90 百分位。 這些應用程式涵蓋應用程式見解配置的範圍（例如，有些應用程式具有預設[採樣](../../azure-monitor/app/sampling.md)，有些沒有採樣等），因此您仍具有使用採樣將所引入的資料量減少到遠低於中位數水準的控制項。 但這是瞭解其他類似客戶所看到的一個起點。

## <a name="understand-your-usage-and-estimate-costs"></a>瞭解您的使用方式並估算成本

Application Insights 可讓您根據最近的使用模式，輕鬆了解可能產生的成本。 若要開始，請在 Azure 入口網站中，針對 Application Insights 資源，移至 [使用量和估計成本]**** 頁面：

![選擇價格](./media/pricing/pricing-001.png)

A. 檢閱當月的資料量。 這包括從您的伺服器和用戶端應用程式，以及從可用性測試接收並保留的所有資料 (在任何[取樣](../../azure-monitor/app/sampling.md)之後)。  
B. 進行[多步驟 Web 測試](../../azure-monitor/app/availability-multistep.md)需另外收費。 (這不包括簡單的可用性測試，其已包含在資料量費用中。)  
C. 檢視上個月的資料量趨勢。  
D. 啟用資料擷取[取樣](../../azure-monitor/app/sampling.md)。
E. 設定每日資料量上限。  

（請注意，本文螢幕截圖中顯示的所有價格僅供參考。 有關貨幣和地區當前的價格，請參閱[應用程式見解定價][pricing].）

若要更深入調查 Application Insights 的使用量，請開啟 [計量]**** 頁面，新增名為「資料點量」的計量，然後選取 [套用分割]** 選項，以根據「遙測項目類型」拆分資料。

Application Insights 費用會加到您的 Azure 帳單中。 您可以在 Azure 入口網站的 [帳務]**** 區段中，或在 [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)中，查看您的 Azure 帳單詳細資料。

![在左側功能表中，選取 [帳務]](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>使用資料量指標
<a id="understanding-ingested-data-volume"></a>

要瞭解有關資料卷的更多資訊，請為應用程式見解資源選擇**指標**，請添加新圖表。 對於圖表指標，**在基於日誌的指標**下，選擇**資料點卷**。 按一下 **"應用拆分**"，然後按**`Telemetryitem`類型**選擇組。

![使用指標查看資料量](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>瞭解資料量詳細資訊的查詢

有兩種方法可以調查應用程式見解的資料卷。 第一個使用表中的`systemEvents`聚合資訊，第二個使用`_BilledSize`屬性，該屬性在每個引入的事件上都可用。

#### <a name="using-aggregated-data-volume-information"></a>使用聚合資料量資訊

例如，可以使用表`systemEvents`查看過去 24 小時內隨查詢引入的資料卷：

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

或者，要查看過去 30 天按資料類型顯示的資料量圖表（以位元組為單位），可以使用：

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

請注意，此查詢可用於[Azure 日誌警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)，以設置資料卷的警報。  

要瞭解有關遙測資料更改的更多情況，我們可以使用查詢按類型獲取事件計數：

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>使用每個事件資訊的資料大小

要瞭解有關資料卷源的更多詳細資訊，可以使用每個引入事件上存在`_BilledSize`的屬性。

例如，要查看過去 30 天內哪些操作生成的資料量最多，我們可以為所有依賴項事件求`_BilledSize`和：

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>在 Azure 帳單上查看應用程式見解使用方式

Azure 在[Azure 成本管理和計費](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中心中提供了大量有用的功能。 例如，"成本分析"功能使您能夠查看 Azure 資源的支出。 按資源類型添加篩選器（到 Microsoft.insights/應用程式見解元件）將允許您跟蹤支出。

[從 Azure 門戶下載使用方式](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)可以更好地瞭解使用方式。
在下載的試算表中，每天可以看到每個 Azure 資源的使用方式。 在此 Excel 試算表中，可以通過首先在"儀錶類別"列上篩選以顯示"應用程式見解"和"日誌分析"，然後在"實例 ID"列上添加篩選器（即"包含 Microsoft.insights/元件"）中找到應用程式見解資源的使用方式。  大多數應用程式見解使用方式都報告在具有日誌分析儀錶類別的儀錶上，因為所有 Azure 監視器元件都有一個日誌後端。  只有舊版定價層和多步驟 Web 測試的應用程式見解資源報告應用程式見解表類別的應用程式見解。  用法顯示在"消耗量"列中，每個條目的單位顯示在"度量單位"列中。  有關詳細資訊，可説明您瞭解 Microsoft [Azure 帳單](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。

## <a name="managing-your-data-volume"></a>管理資料量

可以使用以下技術管理您發送的資料量：

* **取樣**：您可以使用取樣來減少從伺服器和用戶端應用程式傳送的遙測量，這對計量的扭曲程度最小。 取樣是您可用來調整所傳送資料量的主要工具。 深入了解[取樣功能](../../azure-monitor/app/sampling.md)。

* **限制Ajax調用**：您可以限制可在每個網頁檢視中[報告的Ajax調用數](../../azure-monitor/app/javascript.md#configuration)，或關閉Ajax報告。

* **禁用不需要的模組**：[編輯應用程式Insights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)以關閉不需要的集合模組。 例如，您可能會決定效能計數器或相依性資料是不必要的。

* **預聚合指標**：如果在應用中調用 TrackMetric，則可以使用接受計算一批測量的平均和標準差的超載來減少流量。 或者，您也可以使用[預先彙總套件](https://www.myget.org/gallery/applicationinsights-sdk-labs)。
 
* **每日上限**：在 Azure 入口網站中建立 Application Insights 資源時，每日上限會設定為每日 100 GB。 在 Visual Studio 中建立 Application Insights 資源時，預設值很小 (只有每日 32.3 MB)。 每日上限預設值的設定是為了協助測試。 這是預期使用者在將應用程式部署至生產環境之前，會提高每日上限。 

    除非您針對高流量的應用程式要求更高的最大值，否則每日的最高上限是 1,000 GB。
    
    有關每日上限的警告電子郵件將發送到應用程式見解資源這些角色的成員的帳戶："服務管理員"、"帳戶管理員"、"管理員"、"擁有者"。

    設定每日上限時，請務必小心。 您的目的應該是「一律不要達到每日上限」**。 如果達到每日上限，就會失去當天其餘時間的資料，而無法監視應用程式。 若要變更每日上限，請使用 [每日用量上限]**** 選項。 您可以在 [使用量和估計成本]**** 窗格中存取此選項 (本文稍後會有更詳細的說明)。
    
    我們已移除某些訂用帳戶類型的信用額度無法用於 Application Insights 的限制。 先前，如果訂用帳戶有消費限制，則 [每日上限] 對話方塊中會有指示，說明如何移除消費限制並讓每日上限提高到每日 32.3 MB 以上。
    
* **節流**：節流會將資料速率限制在每秒 32,000 個事件 (每個檢測金鑰以 1 分鐘計算平均值)。 系統會每分鐘評估應用程式傳送的資料量。 如果每秒速率超過每分鐘平均值，伺服器會拒絕部分要求。 SDK 會緩衝處理資料，然後嘗試重新傳送它。 它會在數分鐘的期間內散佈大量資料。 如果應用程式始終以高於節流速率的方式傳送資料，有些資料會就遭到捨棄。 （ASP.NET、JAVA 和 JavaScript SDK 嘗試以這種方式重新發送資料;其他 SDK 可能只是丟棄受限制的資料。如果發生限制，將發出通知警告，提醒您已發生這種情況。

## <a name="manage-your-maximum-daily-data-volume"></a>管理最大日資料量

您可以使用每日用量上限來限制所收集的資料。 不過，如果達到上限，就會失去當天其餘時間從您應用程式傳送的所有遙測資料。 建議您「不要」** 讓應用程式達到每日上限。 一旦應用程式達到每日上限，您便無法追蹤應用程式的健康情況和效能。

請不要使用每日用量上限，而是改用[取樣](../../azure-monitor/app/sampling.md)將資料量調整到您想要的程度。 然後，只使用每日上限作為應付萬一應用程式開始未預期地傳送大量遙測時的「最後手段」。

### <a name="identify-what-daily-data-limit-to-define"></a>識別要定義的每日資料限制

查看應用程式見解使用方式和估計成本，以瞭解資料引入趨勢以及要定義的每日交易量上限。 應該謹慎考慮它，因為達到限制後您將無法監視資源。

### <a name="set-the-daily-cap"></a>設置每日上限

要更改每日上限，請在"**使用和估計成本**"頁中的"配置應用程式見解"資源的"**配置**"部分中，選擇 **"每日上限**"。

![調整每日遙測資料量上限](./media/pricing/pricing-003.png)

要[通過 Azure 資源管理器更改每日上限](../../azure-monitor/app/powershell.md)，要更改的屬性是`dailyQuota`。  通過 Azure 資源管理器，您還可以設置`dailyQuotaResetTime`和 每日上限`warningThreshold`。

### <a name="create-alerts-for-the-daily-cap"></a>為每日上限創建警報

當引入的資料卷達到警告級別或每日上限級別時，應用程式見解每日上限在 Azure 活動 kog 中創建事件。  您可以[根據這些活動日誌事件創建警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal)。 這些事件的信號名稱為：

* 應用程式見解元件每日上限警告閾值達到

* 應用洞察元件每日上限達到

## <a name="sampling"></a>取樣
[取樣](../../azure-monitor/app/sampling.md)是一種方法，可降低將遙測傳送到您應用程式時的速率，同時仍保留在診斷搜尋期間尋找相關事件的功能。 此外，也保留正確的事件計數。

取樣可有效減少費用並維持在每月配額內。 取樣演算法會保留相關的遙測項目，因此，例如在使用 [搜尋] 時，您便可以找到與特定例外狀況相關的要求。 此演算法也會保留正確的計數，因此您在 [計量瀏覽器] 中會看到要求率、例外狀況率及其他計數的正確值。

取樣有數種形式。

* [調適型取樣](../../azure-monitor/app/sampling.md)是 ASP.NET SDK 的預設值。 調適型取樣會自動針對應用程式所傳送的遙測量進行調整。 這會自動在 Web 應用程式的 SDK 中運作，以便降低網路上的遙測流量。 
* ** 是替代方法，它會在遙測從應用程式進入 Application Insights 服務時運作。 擷取取樣不會影響從應用程式傳送的遙測量，但可減少服務所保留的量。 您可以使用擷取取樣來減少被來自瀏覽器及其他 SDK 的遙測用光的配額。

若要設定擷取取樣，請移至 [定價]**** 窗格：

![在 [配額和定價] 窗格中，選取 [範例] 圖格，然後選取一個取樣分數](./media/pricing/pricing-004.png)

> [!WARNING]
> [資料取樣]**** 窗格只會控制擷取取樣的值。 它不會反映應用程式中 Application Insights SDK 所套用的取樣率。 如果已在 SDK 中對連入遙測進行取樣，就不會套用擷取取樣。
>

若要探索實際的取樣率，而不論其套用位置是哪裡，請使用[分析查詢](analytics.md)。 此查詢看起來像這樣：

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

在每個保留的記錄中，`itemCount` 會指出它所代表的原始記錄數目。 它等於 1 + 先前捨棄的記錄數目。

## <a name="change-the-data-retention-period"></a>變更資料保留期

應用程式見解資源的預設保留期為 90 天。 可以為每個應用程式見解資源選擇不同的保留期。 全套可用保留期為 30、60、90、120、180、270、365、550 或 730 天。

要更改保留，請從應用程式見解資源轉到 **"使用方式和估計成本**"頁，然後選擇 **"資料保留**"選項：

![調整每日遙測資料量上限](./media/pricing/pricing-005.png)

也可以`retentionInDays`[使用 參數以程式設計方式設置](powershell.md#set-the-data-retention)保留。 此外，如果將資料保留設置為 30 天，則可以使用`immediatePurgeDataOn30Days`參數立即觸發舊資料清除，這對於合規性相關方案可能很有用。 此清除功能僅通過 Azure 資源管理器公開，應格外小心使用。 可以使用 Azure 資源管理器配置資料卷上限的每日重置時間以`dailyQuotaResetTime`設置參數。

## <a name="data-transfer-charges-using-application-insights"></a>使用應用程式見解的資料傳輸費用

將資料發送到應用程式見解可能會導致資料頻寬費用。 如 Azure[頻寬定價頁](https://azure.microsoft.com/pricing/details/bandwidth/)中所述，位於兩個區域中的 Azure 服務之間的資料傳輸按正常速率作為出站資料傳輸。 入站資料傳輸是免費的。 但是，此費用非常小（很少%）與應用程式見解日誌資料引入的成本相比。 因此，控制日誌分析的成本需要專注于您的引入資料量，我們有指導來説明[理解這一點](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume)。

## <a name="limits-summary"></a>限制摘要

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>停用每日上限電子郵件

若要停用每日數量上限電子郵件，請在 Application Insights 資源的 [設定]**** 區段之下，從 [使用量和估計成本]**** 窗格選取 [每日上限]****。 此處有設定可在達到上限時，以及已達到可調整的警告層級時傳送電子郵件。 如果您希望禁用所有每日上限與音量相關的電子郵件，請取消選中這兩個框。

## <a name="legacy-enterprise-per-node-pricing-tier"></a>舊企業（每個節點）定價層

對於 Azure 應用程式見解的早期採用者，仍有兩個可能的定價層：基本定價層和企業級。 基本定價層與上述相同，是預設層。 它包括所有企業層功能，無需額外費用。 基本層主要根據被引入的資料量進行計算。

> [!NOTE]
> 這些遺留定價層已重命名。 企業定價層現在稱為 **"每個節點"，** 基本定價層現在稱為 **"每 GB"。** 這些新名稱在下面和 Azure 門戶中使用。  

每個節點（以前是企業）層具有每個節點的費用，每個節點接收每日資料限額。 在"每個節點"定價層中，對於超出包含限額的資料，將向您收費。 如果使用操作管理套件，則應選擇"每個節點"層。

如需您幣別與區域的目前價格，請參閱 [Application Insights 價格](https://azure.microsoft.com/pricing/details/application-insights/)。

> [!NOTE]
> 我們在 2018 年 4 月[推出](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)新的 Azure 監視定價模型。 這個模型在完整的監視服務組合之間採用簡單的「隨用隨付」模型。 瞭解有關[新定價模型](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)、如何根據您的使用模式[評估遷移到此模型的影響](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs)以及如何[加入宣告新模型](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>每個節點層和運營管理套件訂閱許可權

購買運營管理套件 E1 和 E2 的客戶可以按節點獲取應用程式見解作為附加元件，無需[按先前宣佈](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)的那樣額外付費。 具體而言，操作管理套件 E1 和 E2 的每個單元都包含對每個節點層應用程式見解的一個節點的權利。 每個 Application Insights 節點包含每天最多 200 MB 擷取的資料 (與 Log Analytics 資料擷取分開計算)，資料可保留 90 天而無須額外付費。 本文稍後將更詳細地介紹該層。

由於此層僅適用于具有操作管理套件訂閱的客戶，因此沒有操作管理套件訂閱的客戶看不到選擇此層的選項。

> [!NOTE]
> 為了確保獲得此授權，應用程式見解資源必須位於"每個節點"定價層中。 此權利只能以節點為單位套用。 "每 GB"層中的應用程式見解資源不會實現任何好處。 在 [使用量和估計成本]**** 窗格中所顯示的估計成本中看不到此權利。 此外，如果在 2018 年 4 月將訂閱移動到新的 Azure 監視定價模型，則"每 GB"層是唯一可用的層。 如果您擁有 Operations Management Suite 訂用帳戶，建議您不要將訂用帳戶移轉至新的 Azure 監視定價模型。

### <a name="how-the-per-node-tier-works"></a>每個節點層的工作原理

* 為"每個節點"層中的任何應用發送遙測資料的每個節點付費。
  * *節點*是承載應用的物理或虛擬伺服器電腦或平臺即服務角色實例。
  * 開發用機器、用戶端瀏覽器及行動裝置不算節點。
  * 如果您的應用程式有數個會傳送遙測的元件，例如 Web 服務和後端背景工作角色，則會分開計算元件。
  * [即時計量串流](../../azure-monitor/app/live-stream.md)資料不會計入價格用途。 訂用帳戶的收費是依照每一節點，而非每一應用程式。 如果您有五個節點傳送 12 個應用程式的遙測，則是以五個節點計算收費。
* 雖然費用是按月報價，您的收費僅適用於一個節點從一個應用程式傳送遙測的任何小時。 每小時的費用是月費報價除以 744 (一個月 31 天的小時數)。
* 每個節點 (資料粒度為小時) 偵測每日 200 MB 的資料量配置。 未使用的資料配置不會累計到隔天。
  * 如果選擇"每個節點"定價層，則每個訂閱會根據向該訂閱中的應用程式見解資源發送遙測的節點數獲取每日資料量。 因此，如果您有 5 個節點全天候傳送資料，您會有合起來 1GB 的資料額度，套用至該訂用帳戶中的所有 Application Insights 資源。 由於內含資料會在所有節點之間共用，因此特定節點所傳送的資料是否超過其他節點並無妨。 如果在給定的某一天，應用程式見解資源接收的資料多於此訂閱的每日資料分配中包含的資料，則每 GB 超額資料費用將收取。 
  * 每日資料額度的計算方式，是每個節點在當天傳送遙測資料的時數 (使用 UTC) 除以 24 再乘以 200 MB。 因此，如果您有四個節點在一天 24 個小時的 15 個小時內傳送遙測資料，則當天內含資料量會是 ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB。 以資料超量每一 GB 的價格是 2.30 美元來說，如果節點那一天會傳送 1 GB 的資料，費用就是 1.15 美元。
  * "每個節點"層每日限額不會與您選擇"每 GB"層的應用程式共用。 未使用額度不會逐日累計。

### <a name="examples-of-how-to-determine-distinct-node-count"></a>如何判斷相異節點計數的範例

| 狀況                               | 每日節點總數 |
|:---------------------------------------|:----------------:|
| 1 個應用程式使用 3 個 Azure App Service 執行個體和 1 個虛擬伺服器 | 4 |
| 在 2 個 VM 上運行的 3 個應用程式;這些應用程式的應用程式見解資源位於同一訂閱中，並且位於"每個節點"層中 | 2 | 
| 4 個應用程式的 Application Insights 資源位於相同訂用帳戶中；每個應用程式在離峰的 16 小時期間執行 2 個執行個體，在尖峰的 8 小時期間執行 4 個執行個體 | 13.33 |
| 雲端服務有 1 個背景工作角色和 1 個 Web 角色，各執行 2 個執行個體 | 4 | 
| 5 個節點的 Azure Service Fabric 叢集執行 50 個微服務，每個微服務執行 3 個執行個體 | 5|

* 節點的精確計算取決於您的應用程式使用哪個 Application Insights SDK。 
  * 在 SDK 2.2 版和更新版本中，Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 和 [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 都會將每個應用程式主機報告為節點。 範例包括實體伺服器和虛擬機器主機的電腦名稱，或雲端服務的執行個體名稱。  唯一的例外狀況是僅使用 [.NET Core](https://dotnet.github.io/) 和 Application Insights Core SDK 的應用程式。 在此情況下，因為主機名稱無法使用，所以只會針對所有主機報告一個節點。
  * 對於較早版本的 SDK，[Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 的行為就和較新版的 SDK 一樣，不過不論應用程式主機的數目是多少，[Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 都只會報告一個節點。
  * 如果您的應用程式使用 SDK 來將 **roleInstance** 設為自訂值，依預設將使用那個相同的值來判斷節點計數。
  * 如果您使用新的 SDK 版本使用從用戶端電腦或行動裝置運行的應用，則節點計數可能會返回較大的數位（因為用戶端電腦或行動裝置數量眾多）。

## <a name="automation"></a>自動化

可以使用 Azure 資源管理編寫腳本來設置定價層。 [了解作法](powershell.md#price)。

## <a name="next-steps"></a>後續步驟

* [採樣](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
