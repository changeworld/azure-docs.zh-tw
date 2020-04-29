---
title: 管理 Azure Application Insights 的使用量和成本 | Microsoft Docs
description: 在 Application Insights 中管理遙測量和監視成本。
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 11/27/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0225484de06ae4e595f1dcbcdd520f4e0e4d53f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405380"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>管理 Application Insights 的使用量和成本

> [!NOTE]
> 本文說明如何瞭解並控制 Application Insights 的成本。  相關文章、[監視使用量和估計成本](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs)說明如何針對不同的計價模式，跨多項 Azure 監視功能來查看使用量和估計成本。

Application Insights 的設計，是為了取得監視 web 應用程式的可用性、效能和使用方式所需的所有專案，不論它們是裝載在 Azure 或內部部署環境。 Application Insights 支援熱門的語言和架構，例如 .NET、JAVA 和 node.js，並與 Azure DevOps、Jira 和 PagerDuty 等 DevOps 流程和工具整合。 請務必瞭解如何判斷監視應用程式的成本。 在本文中，我們會回顧您的應用程式監視成本，以及如何主動監視及控制這些專案。

如果您有關於 Application Insights 定價運作方式的疑問，請在我們的[論壇](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc)中張貼問題。

## <a name="pricing-model"></a>定價模式

[Azure 應用程式 Insights][start]的定價是以資料量內嵌為基礎的**隨用隨付**模型，並可選擇性地保留較長的資料。 每項 Application Insights 資源都是個別計費的服務，並且會計入到您的 Azure 訂用帳戶帳單。 資料量是以 Application Insights 從應用程式接收的未壓縮 JSON 資料套件大小來測量。 使用[即時計量資料流](../../azure-monitor/app/live-stream.md)並不會產生任何資料量費用。

[多重步驟 Web 測試](../../azure-monitor/app/availability-multistep.md)會產生額外費用。 多重步驟 Web 測試係指執行一系列動作的 Web 測試。 單一頁面的「Ping 測試」** 不另外收費。 針對來自 Ping 測試和多重步驟測試的遙測，收費方式與來自您應用程式的其他遙測一樣。

[啟用自訂計量維度警示](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation)的 Application Insights 選項也可能產生額外成本，因為這可能會導致建立額外的預先匯總度量。 [深入瞭解](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics)Application Insights 中的記錄式和預先匯總的計量，以及 Azure 監視器自訂計量的[定價](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="estimating-the-costs-to-manage-your-application"></a>估計管理應用程式的成本

如果您尚未使用 Application Insights，您可以使用[Azure 監視器定價計算機](https://azure.microsoft.com/pricing/calculator/?service=monitor)來預估使用 Application Insights 的成本。 一開始請在搜尋方塊中輸入 "Azure 監視器"，然後按一下產生的 [Azure 監視器] 磚。 在頁面上向下 Azure 監視器，然後從 [類型] 下拉式清單中選取 [Application Insights]。  您可以在這裡輸入每月預期收集的資料 GB 數，因此問題是 Application Insights 收集監視應用程式的資料量。

有兩種方法可以解決這種情況：使用預設監視和調適型取樣（可在 ASP.NET SDK 中取得），或根據其他類似的客戶來估計可能的資料內嵌。

### <a name="data-collection-when-using-sampling"></a>使用取樣時的資料收集

透過 ASP.NET SDK 的調適型[取樣](sampling.md#adaptive-sampling)，資料磁片區會自動調整，以保持在預設 Application Insights 監視的指定最大傳輸速率。 如果應用程式產生較少的遙測資料（例如，在進行調試時或由於低使用量的緣故），取樣處理器就不會卸載專案，只要磁片區低於每秒設定的事件數層級即可。 針對高容量的應用程式，預設臨界值為每秒五個事件，調適型取樣會將每日事件數限制為432000。 使用一般的平均事件大小 1 KB，這會對應13.4 到每個節點每31天的遙測資料，每個月裝載您的應用程式（因為取樣是在每個節點的本機執行）。 

針對不支援調適型取樣的 Sdk，您可以使用內建[取樣](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling)，其中會根據要保留的資料百分比來 Application Insights 接收資料，或針對[ASP.NET、ASP.NET Core 和 JAVA 網站的固定速率取樣](sampling.md#fixed-rate-sampling)，減少從您的 web 伺服器和網頁瀏覽器傳送的流量

### <a name="learn-from-what-similar-customers-collect"></a>瞭解類似客戶所收集的內容

在適用于 Application Insights 的 Azure 監視定價計算機中，如果您啟用「根據應用程式活動估計資料量」功能，您可以提供應用程式的輸入（每月個要求和每月的頁面流覽次數，萬一您要收集用戶端遙測），然後計算機會告訴您類似的應用程式所收集的資料中間值和第90個百分位數。 這些應用程式會跨越 Application Insights 設定的範圍（例如，有些有預設[取樣](../../azure-monitor/app/sampling.md)，有些則沒有取樣等等），因此您仍然可以使用取樣，讓您的控制項減少您在最下方的中介層中所內嵌的資料量。 但這是一個起點，可瞭解其他類似客戶的觀點。

## <a name="understand-your-usage-and-estimate-costs"></a>瞭解您的使用量和估計成本

Application Insights 可讓您根據最近的使用模式，輕鬆了解可能產生的成本。 若要開始，請在 Azure 入口網站中，針對 Application Insights 資源，移至 [使用量和估計成本]**** 頁面：

![選擇價格](./media/pricing/pricing-001.png)

A. 檢閱當月的資料量。 這包括從您的伺服器和用戶端應用程式，以及從可用性測試接收並保留的所有資料 (在任何[取樣](../../azure-monitor/app/sampling.md)之後)。  
B. 進行[多步驟 Web 測試](../../azure-monitor/app/availability-multistep.md)需另外收費。 (這不包括簡單的可用性測試，其已包含在資料量費用中。)  
C. 檢視上個月的資料量趨勢。  
D. 啟用資料擷取[取樣](../../azure-monitor/app/sampling.md)。
E. 設定每日資料量上限。  

（請注意，本文的螢幕擷取畫面中顯示的所有價格僅供範例之用。 如需您的貨幣和地區的目前價格，請參閱[Application Insights 定價][pricing]）。

若要更深入調查 Application Insights 的使用量，請開啟 [計量]**** 頁面，新增名為「資料點量」的計量，然後選取 [套用分割]** 選項，以根據「遙測項目類型」拆分資料。

Application Insights 費用會加到您的 Azure 帳單中。 您可以在 Azure 入口網站的 [帳務]**** 區段中，或在 [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)中，查看您的 Azure 帳單詳細資料。

![在左側功能表中，選取 [帳務]](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>使用資料量計量
<a id="understanding-ingested-data-volume"></a>

若要深入瞭解您的資料磁片區，請選取 Application Insights 資源的**計量**，並新增新的圖表。 針對圖表計量，請在 [以**記錄為基礎的度量**] 底下選取 [**資料點磁片**區]。 按一下 [套用**分割**]，然後選取 [依** `Telemetryitem`類型**群組]。

![使用計量查看資料量](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>瞭解資料量細節的查詢

有兩種方法可以調查 Application Insights 的資料磁片區。 第一個會使用`systemEvents`資料表中的匯總資訊，而第二個`_BilledSize`使用屬性，這在每個內嵌事件上都有提供。

#### <a name="using-aggregated-data-volume-information"></a>使用匯總資料量資訊

例如，您可以使用`systemEvents`資料表來查看過去24小時內查詢的資料磁片區內嵌：

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

或者，若要查看過去30天內資料類型的資料量（以位元組為單位），您可以使用：

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

請注意，您可以在[Azure 記錄警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)中使用此查詢來設定資料磁片區的警示。  

若要深入瞭解您的遙測資料變更，我們可以使用查詢依類型取得事件計數：

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>使用每個事件的資料大小資訊

若要深入瞭解資料磁片區的來源，您可以使用出現在每`_BilledSize`個內嵌事件上的屬性。

例如，若要查看過去30天內產生最多資料量的作業，我們可以加總`_BilledSize`所有相依性事件：

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>在您的 Azure 帳單上查看 Application Insights 使用量

Azure 在[Azure 成本管理 + 計費](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中樞提供了大量有用的功能。 例如，「成本分析」功能可讓您查看 Azure 資源的花費。 依資源類型新增篩選器（至 Application Insights 的 microsoft insights/元件）可讓您追蹤費用。

[從 Azure 入口網站下載您的使用量](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)，即可取得更多使用方式的瞭解。
在下載的試算表中，您可以看到每天每個 Azure 資源的使用量。 在此 Excel 試算表中，您可以藉由先篩選「計量類別」資料行來顯示「Application Insights」和「Log Analytics」，然後在「包含 microsoft Insights/元件」的「實例識別碼」資料行上新增篩選，來找到您 Application Insights 資源的使用量。  大部分的 Application Insights 使用量都會以 Log Analytics 計量類別的計量報告，因為所有 Azure 監視器元件都有單一記錄後端。  只有舊版定價層和多重步驟 web 測試的 Application Insights 資源，會以 Application Insights 的計量類別來報告。  使用量會顯示在 [取用的數量] 資料行中，每個專案的單位會顯示在 [測量單位] 資料行中。  有更多詳細資料可協助您[瞭解您的 Microsoft Azure 帳單](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。

## <a name="managing-your-data-volume"></a>管理您的資料量

您可以使用下列技術來管理您傳送的資料量：

* **取樣**：您可以使用取樣來減少從伺服器和用戶端應用程式傳送的遙測量，這對計量的扭曲程度最小。 取樣是您可用來調整所傳送資料量的主要工具。 深入了解[取樣功能](../../azure-monitor/app/sampling.md)。

* **限制 ajax 呼叫**：您可以限制可以在每個網頁檢視中[報告的 ajax 呼叫數目](../../azure-monitor/app/javascript.md#configuration)，或關閉 ajax 報告功能。

* **停**用不必要的模組：[編輯 ApplicationInsights](../../azure-monitor/app/configuration-with-applicationinsights-config.md)以關閉您不需要的集合模組。 例如，您可能會決定效能計數器或相依性資料是不必要的。

* **預先匯總的計量**：如果您將呼叫放在應用程式中，您可以使用可接受測量批次平均值和標準差計算的多載，以減少流量。 或者，您也可以使用[預先彙總套件](https://www.myget.org/gallery/applicationinsights-sdk-labs)。
 
* **每日上限**：在 Azure 入口網站中建立 Application Insights 資源時，每日上限會設定為每日 100 GB。 在 Visual Studio 中建立 Application Insights 資源時，預設值很小 (只有每日 32.3 MB)。 每日上限預設值的設定是為了協助測試。 這是預期使用者在將應用程式部署至生產環境之前，會提高每日上限。 

    除非您針對高流量的應用程式要求更高的最大值，否則每日的最高上限是 1,000 GB。
    
    有關每日上限的警告電子郵件會傳送給屬於您 Application Insights 資源之這些角色成員的帳戶： "ServiceAdmin"、"AccountAdmin"、"共同管理員"、"Owner"。

    設定每日上限時，請務必小心。 您的目的應該是「一律不要達到每日上限」**。 如果達到每日上限，就會失去當天其餘時間的資料，而無法監視應用程式。 若要變更每日上限，請使用 [每日用量上限]**** 選項。 您可以在 [使用量和估計成本]**** 窗格中存取此選項 (本文稍後會有更詳細的說明)。
    
    我們已移除某些訂用帳戶類型的信用額度無法用於 Application Insights 的限制。 先前，如果訂用帳戶有消費限制，則 [每日上限] 對話方塊中會有指示，說明如何移除消費限制並讓每日上限提高到每日 32.3 MB 以上。
    
* **節流**：節流會將資料速率限制在每秒 32,000 個事件 (每個檢測金鑰以 1 分鐘計算平均值)。 系統會每分鐘評估應用程式傳送的資料量。 如果每秒速率超過每分鐘平均值，伺服器會拒絕部分要求。 SDK 會緩衝處理資料，然後嘗試重新傳送它。 它會在數分鐘的期間內散佈大量資料。 如果應用程式始終以高於節流速率的方式傳送資料，有些資料會就遭到捨棄。 （ASP.NET、JAVA 和 JavaScript Sdk 會以這種方式嘗試重新傳送資料; 其他 Sdk 可能只是卸載節流的資料）。如果發生節流，通知警告會通知您發生這種情況。

## <a name="manage-your-maximum-daily-data-volume"></a>管理您的每日最大資料量

您可以使用每日用量上限來限制所收集的資料。 不過，如果達到上限，就會失去當天其餘時間從您應用程式傳送的所有遙測資料。 建議您「不要」** 讓應用程式達到每日上限。 一旦應用程式達到每日上限，您便無法追蹤應用程式的健康情況和效能。

請不要使用每日用量上限，而是改用[取樣](../../azure-monitor/app/sampling.md)將資料量調整到您想要的程度。 然後，只使用每日上限作為應付萬一應用程式開始未預期地傳送大量遙測時的「最後手段」。

### <a name="identify-what-daily-data-limit-to-define"></a>識別要定義的每日資料限制

請參閱 Application Insights 使用方式和估計成本，以瞭解資料內嵌趨勢，以及要定義的每日量上限。 請謹慎考慮，因為在達到限制之後，您將無法監視您的資源。

### <a name="set-the-daily-cap"></a>設定每日上限

若要變更每日上限，請在 Application Insights 資源的 [**設定**] 區段的 [**使用量和估計成本**] 頁面中，選取 [**每日上限**]。

![調整每日遙測資料量上限](./media/pricing/pricing-003.png)

若要透過[Azure Resource Manager 變更每日上限](../../azure-monitor/app/powershell.md)，要變更的屬性為`dailyQuota`。  透過 Azure Resource Manager 您也可以設定`dailyQuotaResetTime`和每日上限。 `warningThreshold`

### <a name="create-alerts-for-the-daily-cap"></a>建立每日上限的警示

當內嵌資料磁片區達到警告層級或每日上限層級時，Application Insights 每日上限會在 Azure 活動記錄中建立事件。  您可以根據[這些活動記錄事件建立警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal)。 這些事件的信號名稱包括：

* 已達到 Application Insights 元件每日上限的警告閾值

* 已達到 Application Insights 元件每日上限

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

Application Insights 資源的預設保留期為90天。 可以為每個 Application Insights 資源選取不同的保留週期。 一組完整的可用保留週期為30、60、90、120、180、270、365、550或730天。

若要變更保留期，請在 Application Insights 資源中，移至 [**使用量和估計成本**] 頁面，然後選取 [**資料保留**] 選項：

![調整每日遙測資料量上限](./media/pricing/pricing-005.png)

降低保留期時，會在移除最舊的資料之前，有幾天的寬限期。

您也可以使用`retentionInDays`參數，透過[PowerShell 以程式設計方式設定](powershell.md#set-the-data-retention)保留期。 如果您將資料保留期設定為30天，您可以使用`immediatePurgeDataOn30Days`參數觸發立即清除較舊的資料，這可能適用于合規性相關案例。 這種清除功能只會透過 Azure Resource Manager 公開，而且應該小心使用。 資料量上限的每日重設時間可以使用 Azure Resource Manager 設定`dailyQuotaResetTime`參數。

## <a name="data-transfer-charges-using-application-insights"></a>使用 Application Insights 的資料傳輸費用

將資料傳送至 Application Insights 可能會產生資料頻寬費用。 如[Azure 頻寬定價頁面](https://azure.microsoft.com/pricing/details/bandwidth/)中所述，位於兩個區域的 Azure 服務之間的資料傳輸會依正常費率向輸出資料傳輸收費。 輸入資料傳輸是免費的。 不過，這種費用非常小（幾%）相較于 Application Insights 記錄資料內嵌的成本。 因此，控制 Log Analytics 的成本需要專注于[您的內嵌](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume)資料磁片區，而我們也有指導方針可協助您瞭解這點。

## <a name="limits-summary"></a>限制摘要

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>停用每日上限電子郵件

若要停用每日數量上限電子郵件，請在 Application Insights 資源的 [設定]**** 區段之下，從 [使用量和估計成本]**** 窗格選取 [每日上限]****。 此處有設定可在達到上限時，以及已達到可調整的警告層級時傳送電子郵件。 如果您想要停用所有每日上限與磁片區相關的電子郵件，請取消勾選這兩個方塊。

## <a name="legacy-enterprise-per-node-pricing-tier"></a>舊版企業（每個節點）定價層

針對 Azure 應用程式深入解析的早期採用者，仍然有兩個可能的定價層：基本和企業。 基本定價層與上面所述相同，而且是預設層。 它包含所有企業層功能，不需額外費用。 基本層帳單主要取決於所內嵌的資料量。

> [!NOTE]
> 這些舊版定價層已重新命名。 企業定價層現在會**針對每個節點**呼叫，而基本定價層現在會**針對每 GB**呼叫。 這些新名稱會在 Azure 入口網站中使用。  

每個節點（先前稱為「企業」）層都有每個節點的費用，而且每個節點都會收到每日資料額度。 在每個節點定價層中，您需支付超過所包含額度的資料內嵌費用。 如果您使用 Operations Management Suite，您應該選擇 [每個節點] 層。

如需您幣別與區域的目前價格，請參閱 [Application Insights 價格](https://azure.microsoft.com/pricing/details/application-insights/)。

> [!NOTE]
> 我們在 2018 年 4 月[推出](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)新的 Azure 監視定價模型。 這個模型在完整的監視服務組合之間採用簡單的「隨用隨付」模型。 深入瞭解新的[定價模型](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)、如何根據您的使用模式[評估移到這個模型的影響](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs)，以及[如何加入宣告新的模型](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>每個節點層和 Operations Management Suite 訂用帳戶權利

購買 Operations Management Suite E1 和 E2 的客戶可以將每個節點 Application Insights 為其他元件，而不需額外付費，如[先前所宣佈](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)。 具體來說，Operations Management Suite E1 和 E2 的每個單位都包含每個節點層 Application Insights 的一個節點權利。 每個 Application Insights 節點包含每天最多 200 MB 擷取的資料 (與 Log Analytics 資料擷取分開計算)，資料可保留 90 天而無須額外付費。 本文章稍後會詳細說明此層。

因為此層僅適用于具有 Operations Management Suite 訂用帳戶的客戶，所以沒有 Operations Management Suite 訂用帳戶的客戶不會看到選取此層的選項。

> [!NOTE]
> 為確保您取得此權利，您的 Application Insights 資源必須位於每個節點的定價層。 此權利只能以節點為單位套用。 每 GB 層中的 Application Insights 資源都不會實現任何好處。 在 [使用量和估計成本]**** 窗格中所顯示的估計成本中看不到此權利。 此外，如果您在2018年4月將訂用帳戶移至新的 Azure 監視定價模型，則每 GB 層是唯一可用的層級。 如果您擁有 Operations Management Suite 訂用帳戶，建議您不要將訂用帳戶移轉至新的 Azure 監視定價模型。

### <a name="how-the-per-node-tier-works"></a>每個節點層的運作方式

* 針對每個節點層中的任何應用程式傳送遙測資料的每個節點，您必須支付費用。
  * *節點*是裝載您應用程式的實體或虛擬伺服器電腦或平臺即服務角色實例。
  * 開發用機器、用戶端瀏覽器及行動裝置不算節點。
  * 如果您的應用程式有數個會傳送遙測的元件，例如 Web 服務和後端背景工作角色，則會分開計算元件。
  * [即時計量串流](../../azure-monitor/app/live-stream.md)資料不會計入價格用途。 訂用帳戶的收費是依照每一節點，而非每一應用程式。 如果您有五個節點傳送 12 個應用程式的遙測，則是以五個節點計算收費。
* 雖然費用是按月報價，您的收費僅適用於一個節點從一個應用程式傳送遙測的任何小時。 每小時的費用是月費報價除以 744 (一個月 31 天的小時數)。
* 每個節點 (資料粒度為小時) 偵測每日 200 MB 的資料量配置。 未使用的資料配置不會累計到隔天。
  * 如果您選擇 [每個節點] 定價層，每個訂用帳戶會根據將遙測傳送至該訂用帳戶中 Application Insights 資源的節點數目，取得每日的資料額度。 因此，如果您有 5 個節點全天候傳送資料，您會有合起來 1GB 的資料額度，套用至該訂用帳戶中的所有 Application Insights 資源。 由於內含資料會在所有節點之間共用，因此特定節點所傳送的資料是否超過其他節點並無妨。 如果在指定的一天內，Application Insights 資源會收到超過此訂用帳戶的每日資料配置所包含的資料，則適用每 GB 超額資料費用。 
  * 每日資料額度的計算方式，是每個節點在當天傳送遙測資料的時數 (使用 UTC) 除以 24 再乘以 200 MB。 因此，如果您有四個節點在一天 24 個小時的 15 個小時內傳送遙測資料，則當天內含資料量會是 ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB。 以資料超量每一 GB 的價格是 2.30 美元來說，如果節點那一天會傳送 1 GB 的資料，費用就是 1.15 美元。
  * 每個節點層的每日額度不會與您已選擇每個 GB 層的應用程式共用。 未使用額度不會逐日累計。

### <a name="examples-of-how-to-determine-distinct-node-count"></a>如何判斷相異節點計數的範例

| 案例                               | 每日節點總數 |
|:---------------------------------------|:----------------:|
| 1 個應用程式使用 3 個 Azure App Service 執行個體和 1 個虛擬伺服器 | 4 |
| 3個在2部 Vm 上執行的應用程式;這些應用程式的 Application Insights 資源位於相同的訂用帳戶中，且位於每個節點層 | 2 | 
| 4 個應用程式的 Application Insights 資源位於相同訂用帳戶中；每個應用程式在離峰的 16 小時期間執行 2 個執行個體，在尖峰的 8 小時期間執行 4 個執行個體 | 13.33 |
| 雲端服務有 1 個背景工作角色和 1 個 Web 角色，各執行 2 個執行個體 | 4 | 
| 5 個節點的 Azure Service Fabric 叢集執行 50 個微服務，每個微服務執行 3 個執行個體 | 5|

* 節點的精確計算取決於您的應用程式使用哪個 Application Insights SDK。 
  * 在 SDK 2.2 版和更新版本中，Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 和 [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 都會將每個應用程式主機報告為節點。 範例包括實體伺服器和虛擬機器主機的電腦名稱，或雲端服務的執行個體名稱。  唯一的例外狀況是僅使用 [.NET Core](https://dotnet.github.io/) 和 Application Insights Core SDK 的應用程式。 在此情況下，因為主機名稱無法使用，所以只會針對所有主機報告一個節點。
  * 對於較早版本的 SDK，[Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 的行為就和較新版的 SDK 一樣，不過不論應用程式主機的數目是多少，[Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 都只會報告一個節點。
  * 如果您的應用程式使用 SDK 來將 **roleInstance** 設為自訂值，依預設將使用那個相同的值來判斷節點計數。
  * 如果您使用新的 SDK 版本搭配從用戶端電腦或行動裝置執行的應用程式，則節點計數可能會傳回很大的數位（因為用戶端機器或行動裝置數目龐大）。

## <a name="automation"></a>自動化

您可以使用 Azure 資源管理來撰寫腳本，以設定定價層。 [了解作法](powershell.md#price)。

## <a name="next-steps"></a>後續步驟

* [取樣](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/