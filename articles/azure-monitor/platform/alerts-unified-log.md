---
title: Azure 監視器中的記錄警示
description: 當您指定的記錄查詢準則符合時，觸發電子郵件、通知、呼叫網站 Url (webhook) 或自動化
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: a913bc0ae01507cb26c1650d63918a8319eeacf4
ms.sourcegitcommit: 697638c20ceaf51ec4ebd8f929c719c1e630f06f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857421"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure 監視器中的記錄警示

## <a name="overview"></a>總覽

記錄警示是 [Azure 警示](./alerts-overview.md)中支援的其中一種警示類型。 記錄警示可讓使用者使用 [Log Analytics](../log-query/log-analytics-tutorial.md) 查詢來評估每個設定頻率的資源記錄，並根據結果引發警示。 規則可以使用 [動作群組](./action-groups.md)觸發一或多個動作。

> [!NOTE]
> Log [Analytics 工作區](../log-query/log-analytics-tutorial.md) 中的記錄資料可以傳送至 Azure 監視器計量存放區。 計量警示有 [不同的行為](alerts-metric-overview.md)，視您使用的資料而定，這可能更適合。 如需有關如何將記錄路由至計量的詳細資訊，請參閱 [記錄的計量警示](alerts-metric-logs.md)。

> [!NOTE]
> API 版本 `2020-05-01-preview` 和以資源為中心的記錄警示目前不會產生額外費用。  預覽版中的功能價格將于未來宣佈，並在開始計費之前提供通知。 如果您選擇在通知期間之後繼續使用新的 API 版本和以資源為中心的記錄警示，將會以適用的費率計費。

## <a name="prerequisites"></a>必要條件

記錄警示會對 Log Analytics 資料執行查詢。 首先，您應該開始 [收集記錄資料](resource-logs.md) 並查詢記錄資料中的問題。 您可以使用 Log Analytics 中的 [警示查詢範例主題](../log-query/example-queries.md) ，來瞭解您可以探索的內容，或 [開始撰寫您自己的查詢](../log-query/log-analytics-tutorial.md)。

[Azure 監視參與者](./roles-permissions-security.md) 是建立、修改和更新記錄警示所需的共同角色。 也需要存取資源記錄檔的 & 查詢執行許可權。 部分存取資源記錄可能會使查詢失敗或傳回部分結果。 [深入瞭解如何在 Azure 中設定記錄警示](./alerts-log.md)。

> [!NOTE]
> 適用于使用舊版 [Log Analytics 警示 API](api-alerts.md)來管理之 log analytics 的記錄警示。 [深入瞭解如何切換至目前的 SCHEDULEDQUERYRULES API](alerts-log-api-switch.md)。

## <a name="query-evaluation-definition"></a>查詢評估定義

記錄搜尋規則條件定義開始自：

- 要執行哪個查詢？
- 如何使用結果？

下列各節說明您可以用來設定上述邏輯的不同參數。

### <a name="log-query"></a>記錄檔查詢
用來評估規則的 [Log Analytics](../log-query/log-analytics-tutorial.md) 查詢。 此查詢所傳回的結果會用來判斷是否要觸發警示。 查詢的範圍可以是：

- 特定的資源，例如虛擬機器。
- 大規模資源，例如訂用帳戶或資源群組。
- 使用 [跨資源查詢的](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)多個資源。 
 
> [!IMPORTANT]
> 警示查詢具有條件約束，以確保最佳效能和結果相關性。 若要[深入瞭解](./alerts-log-query.md)，請參閱。

> [!IMPORTANT]
> 只有使用目前的 scheduledQueryRules API 支援以資源為中心和 [跨資源的查詢](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) 。 如果您使用舊版 [Log Analytics 警示 API](api-alerts.md)，您將需要切換。 [深入瞭解切換](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>查詢時間範圍

時間範圍是在規則條件定義中設定。 在工作區和 Application Insights 中，它稱為 **句點**。 在所有其他資源類型中，它稱為「覆 **寫查詢時間範圍**」。

如同在 log analytics 中，時間範圍會將查詢資料限制為指定的範圍。 即使在查詢中使用 **前** 一個命令，也會套用時間範圍。

例如，查詢會掃描60分鐘，如果時間範圍是60分鐘，即使文字包含 **前 (1d)**。 時間範圍和查詢時間篩選需要相符。 在範例案例中，將 **週期** 覆  /  **寫查詢時間範圍** 變更為一天，會如預期般運作。

### <a name="measure"></a>量值

記錄警示會將記錄轉換成可評估的數值。 您可以測量兩個不同的專案：

#### <a name="count-of-the-results-table-rows"></a>結果資料表資料列的計數

結果計數是預設量值。 適用于處理事件，例如 Windows 事件記錄、syslog、應用程式例外狀況。 當記錄檔記錄發生或未在評估的時間範圍內發生時觸發。

當您嘗試在記錄檔中偵測到資料時，記錄警示的效果最佳。 當您嘗試在記錄檔中偵測缺少的資料時，它的運作效果比較低。 例如，警示虛擬機器的信號。

針對工作區和 Application Insights，則會 **根據** 選取 **的結果數目** 來呼叫它。 在所有其他資源類型中，它是使用選取 **資料表資料列** 來呼叫 **量值**。

> [!NOTE]
> 由於記錄是半結構化的資料，因此其本質上會高於計量，您可能會在嘗試偵測到記錄中缺少資料時遇到 misfires，而且您應該考慮使用計量 [警示](alerts-metric-overview.md)。 您可以使用 [記錄的計量警示](alerts-metric-logs.md)，將資料從記錄傳送至計量存放區。

##### <a name="example-of-results-table-rows-count-use-case"></a>結果資料表資料列計數使用案例範例

您想要知道應用程式何時回應錯誤碼 500 (內部伺服器錯誤) 。 您可以建立詳細資料如下的警示規則：

- **查詢：** 

```Kusto
requests
| where resultCode == "500"
```

- **時間週期/匯總細微性：** 15 分鐘
- **警示頻率：** 15 分鐘
- **閾值：** 大於 0

然後，警示規則會監視任何以500錯誤碼結尾的要求。 在過去15分鐘內，查詢會每隔15分鐘執行一次。 如果找到一筆記錄，就會引發警示並觸發設定的動作。

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>根據數值資料行的量值計算 (例如 CPU 計數器值) 

針對工作區和 Application Insights，則會 **根據** 選取的 **度量量測** 來呼叫它。 在所有其他資源類型中，它會被稱為 **量值** ，並選取任何數目的資料行名稱。

### <a name="aggregation-type"></a>彙總類型

在多筆記錄上完成的計算，以將它們匯總成一個數值。 例如：
- **Count** 會傳回查詢中的記錄數目
- **Average** 會傳回所定義之量值資料行 [**匯總細微性**](#aggregation-granularity) 的平均值。

在工作區和 Application Insights 中，只有計量 **測量** 量數值型別才支援。 查詢結果必須包含一個稱為 AggregatedValue 的資料行，該資料行會在使用者定義匯總之後提供數值。 在所有其他資源類型中，會從該名稱的欄位中選取 **匯總類型** 。

### <a name="aggregation-granularity"></a>匯總細微性

決定用來將多筆記錄匯總成一個數值的間隔。 例如，如果您指定了 **5 分鐘**，則會使用指定的 **匯總類型** ，將記錄分組為5分鐘的間隔。

在工作區和 Application Insights 中，只有計量 **測量** 量數值型別才支援。 查詢結果必須包含 [bin ( # B1 ](/azure/kusto/query/binfunction) ，以在查詢結果中設定間隔。 在所有其他的資源類型中，控制此設定的欄位稱為「 **匯總細微性**」。

> [!NOTE]
> 由於 [bin ( # B1 ](/azure/kusto/query/binfunction) 可能會導致時間間隔不平均，因此警示服務會自動將 [Bin ( # B3 ](/azure/kusto/query/binfunction) 函式自動轉換成在執行時間使用適當的時間 [bin_at ( # B5 ](/azure/kusto/query/binatfunction) 函式，以確保具有固定點的結果。

### <a name="split-by-alert-dimensions"></a>依警示維度分割

藉由群組為唯一的組合，將警示依數位或字串資料行分割成個別的警示。 在大規模建立以資源為中心的警示時 (訂用帳戶或資源群組範圍) ，您可以依 Azure 資源識別碼資料行分割。 在 Azure 資源識別碼資料行上進行分割，會將警示的目標變更為指定的資源。

當您想要在多個 Azure 資源上監視相同的條件時，建議依 Azure 資源識別碼資料行進行分割。 例如，監視所有虛擬機器的 CPU 使用率超過80%。 如果您想要在範圍內的多個資源上進行條件，例如監視資源群組範圍中至少有五部機器的 CPU 使用率超過80%，您也可以決定不要分割。

在工作區和 Application Insights 中，只有計量 **測量** 量數值型別才支援。 此欄位在上稱為「 **匯總**」。 其限制為三個數據行。 查詢中有三個以上的分組依據資料行可能會導致非預期的結果。 在所有其他資源類型中，它會在條件的 [ **依維度分割** ] 區段中設定 (限制為六個分割) 。

#### <a name="example-of-splitting-by-alert-dimensions"></a>依警示維度分割的範例

例如，您想要監視在特定資源群組中執行網站/應用程式的多部虛擬機器的錯誤。 您可以使用記錄警示規則來執行此作業，如下所示：

- **查詢：** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    使用工作區和 Application Insights 與 **計量測量** 警示邏輯時，必須將這一行新增至查詢文字：

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **資源識別碼資料行：** 依警示規則中的資源識別碼資料行分割 _ResourceId (只適用于目前) 的訂用帳戶和資源群組
- **維度/匯總于：**
  - 電腦 = VM1、VM2 (警示規則定義中的篩選值目前無法用於工作區和 Application Insights。 篩選查詢文字。 ) 
- **時間週期/匯總細微性：** 15 分鐘
- **警示頻率：** 15 分鐘
- **閾值：** 大於 0

此規則會監視過去15分鐘內是否有任何虛擬機器發生錯誤事件。 每部虛擬機器都會個別監視，並且會個別觸發動作。

> [!NOTE]
> 依警示維度分割僅適用于目前的 scheduledQueryRules API。 如果您使用舊版 [Log Analytics 警示 API](api-alerts.md)，您將需要切換。 [深入瞭解切換](./alerts-log-api-switch.md)。 只有 API 版本和更新版本才支援大規模以資源為中心的警示 `2020-05-01-preview` 。

## <a name="alert-logic-definition"></a>警示邏輯定義

當您定義要執行和評估結果的查詢之後，您需要定義警示邏輯和何時引發動作。 下列各節說明您可以使用的不同參數：

### <a name="threshold-and-operator"></a>閾值和運算子

查詢結果會轉換成與臨界值和運算子比較的數位。

### <a name="frequency"></a>頻率

執行查詢的間隔。 可設定為5分鐘到一天。 必須等於或小於 [查詢時間範圍](#query-time-range) ，才不會遺漏記錄檔記錄。

例如，如果您將時間週期設定為30分鐘，並將頻率設定為1小時。  如果查詢是在00:00 執行，則會傳回介於23:30 和00:00 之間的記錄。 下一次執行查詢時，會傳回01:00，並傳回00:30 與01:00 之間的記錄。 將永遠不會評估在00:00 和00:30 之間建立的任何記錄。

### <a name="number-of-violations-to-trigger-alert"></a>觸發警示的違規次數

您可以指定警示評估期間，以及觸發警示所需的失敗次數。 讓您能夠更妥善地定義觸發警示的影響時間。 

例如，如果您的規則 [**匯總細微性**](#aggregation-granularity) 定義為「5分鐘」，則只有在發生最後一小時的15分鐘)  (三次失敗時，才會觸發警示。 這項設定是由您的應用程式商務原則所定義。

## <a name="state-and-resolving-alerts"></a>狀態和解決警示

記錄警示是無狀態的。 警示會在每次符合條件時引發，即使先前已引發亦同。 引發的警示無法解決。 您可以 [將警示標示為已關閉](alerts-managing-alert-states.md)。 您也可以將動作靜音，以防止它們在引發警示規則之後觸發一段期間。

在工作區和 Application Insights 中，它稱為 **隱藏警示**。 在所有其他資源類型中，它稱為「 **靜音動作**」。 

請參閱此警示評估範例：

| Time    | 記錄條件評估 | 結果 
| ------- | ----------| ----------| ------- 
| 00:05 | FALSE | 不會引發警示。 未呼叫任何動作。
| 00:10 | TRUE  | 引發警示，並呼叫動作群組。 新的警示狀態為 [作用中]。
| 00:15 | TRUE  | 引發警示，並呼叫動作群組。 新的警示狀態為 [作用中]。
| 00:20 | FALSE | 不會引發警示。 未呼叫任何動作。 舊警示狀態維持使用中狀態。

## <a name="pricing-and-billing-of-log-alerts"></a>記錄警示的定價和計費

定價資訊位於 [Azure 監視器定價] 頁面](https://azure.microsoft.com/pricing/details/monitor/)。 記錄警示會列在資源提供者底下 `microsoft.insights/scheduledqueryrules` ：

- Application Insights 上的記錄警示，以及資源群組和警示屬性所顯示的確切資源名稱。
- Log Analytics 上的記錄警示會以確切的資源名稱以及資源群組和警示屬性顯示;使用 [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)建立時。
- 從 [舊版 Log ANALYTICS API](./api-alerts.md) 建立的記錄警示不會追蹤 [Azure 資源](../../azure-resource-manager/management/overview.md) ，且不會強制執行唯一的資源名稱。 這些警示仍會 `microsoft.insights/scheduledqueryrules` 以隱藏資源的形式建立，而這些資源具有此資源命名結構 `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` 。 舊版 API 上的記錄警示會顯示于上述隱藏資源名稱，以及資源群組和警示屬性。

> [!NOTE]
> 不支援的資源字元（例如） `<, >, %, &, \, ?, /` 會取代為 `_` 隱藏的資源名稱，這也會反映在帳單資訊中。

> [!NOTE]
> 使用舊版 [Log Analytics 警示 API](api-alerts.md) 來管理 log analytics 的記錄警示，以及 [log analytics 儲存的搜尋和警示](../insights/solutions.md)舊版範本。 [深入瞭解如何切換至目前的 SCHEDULEDQUERYRULES API](alerts-log-api-switch.md)。 任何警示規則管理都應該使用 [舊版 Log ANALYTICS API](api-alerts.md) 來完成，直到您決定切換，而且無法使用隱藏的資源。

## <a name="next-steps"></a>後續步驟

* 了解[在 Azure 警示中的記錄警示中建立](./alerts-log.md)。
* 了解 [Azure 中記錄警示中的 Webhook](alerts-log-webhook.md)。
* 了解 [Azure 警示](./alerts-overview.md)。
* 深入瞭解 [Log Analytics](../log-query/log-query-overview.md)。
