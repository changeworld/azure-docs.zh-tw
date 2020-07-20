---
title: 計量、警示和診斷記錄
description: 記錄並分析 Azure Batch 帳戶資源 (如集區和工作) 的診斷記錄事件。
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: seodec18
ms.openlocfilehash: abf9ef53d3f2e3ffeffabfe9b7c77dc5c5debec3
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145091"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>用於診斷評估和監視的 Batch 計量、警示和記錄
 
本文說明如何使用 [Azure 監視器](../azure-monitor/overview.md)的功能來監視 Batch 帳戶。 Azure 監視器會收集您 Batch 帳戶中的資源[計量](../azure-monitor/platform/data-platform-metrics.md)和[診斷記錄](../azure-monitor/platform/platform-logs-overview.md)。 透過各種方式收集及使用此資料，以監視您的 Batch 帳戶和診斷問題。 您也可以設定[計量警示](../azure-monitor/platform/alerts-overview.md)，在計量達到指定值時接收通知。

## <a name="batch-metrics"></a>Batch 計量

計量是 Azure 遙測資料 (也稱為效能計數器) ，會由您的 Azure 資源發出，並由 Azure 監視器服務所使用。 Batch 帳戶中的計量範例包括集區建立事件、低優先順序節點計數和工作完成事件。

請參閱[支援的 Batch 計量清單](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)。

計量：

- 在每個 Batch 帳戶中預設啟用，不需要額外設定
- 每隔 1 分鐘產生
- 不會自動保存，但是有 30 天的累積記錄。 您可以保存活動計量以作為診斷記錄的一部分。

## <a name="view-batch-metrics"></a>查看批次計量

在 Azure 入口網站中，帳戶的 [**總覽**] 頁面預設會顯示主要節點、核心和工作計量。

若要在 Azure 入口網站中查看所有 Batch 帳戶計量：

1. 在 [Azure 入口網站中，選取 [**所有服務**]  >  [**Batch 帳戶**]，然後選取您的 Batch 帳戶名稱。
2. 在 [**監視**] 底下，選取 [**計量**]。
3. 選取 [**新增度量**]，然後從下拉式清單中選擇 [度量]。
4. 選取度量的**匯總**選項。 針對以計數為基礎的計量 (例如「專用核心計數」或「低優先順序節點計數」 ) ，請使用**平均**匯總。 如需以事件為基礎的計量 (像是「集區調整大小完成事件」 ) ，請使用「匯總」**計數**。

   > [!WARNING]
   > 請勿使用「總和」匯總，這會加總在圖表期間收到的所有資料點的值。

5. 若要新增其他計量，請重複步驟3和4。

您也可以使用 Azure 監視器 Api 以程式設計方式抓取計量。 如需範例，請參閱[使用 .Net 取得 Azure 監視器計量](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)。

### <a name="batch-metric-reliability"></a>Batch 計量可靠性

計量有助於找出趨勢，並可用於資料分析。 請務必注意，計量傳遞並不保證，而且可能會受到順序的傳遞、資料遺失和/或重複。 因此，不建議使用單一事件來警示或觸發函式。 如需如何設定警示閾值的詳細資訊，請參閱下一節。

過去3分鐘內發出的計量可能仍在匯總中，因此在此時間範圍內可能會短報度量值。

## <a name="batch-metric-alerts"></a>Batch 計量警示

您可以設定近乎即時的計量*警示*，當指定的度量值超出您指派的閾值時觸發。 當警示為「已啟動」時 (超出閾值且符合警示條件時)，以及當警示為「已解決」時 (再次超出閾值且不再符合條件時)，警示會產生通知。

不建議在單一資料點上觸發的警示，因為計量會受到不按照順序的傳遞、資料遺失和/或重複。 建立警示時，您可以使用閾值來考慮這些不一致的情況。

例如，您可以在低優先順序核心計數降至特定層級時設定計量警示，以便您調整集區的構成要素。 為獲得最佳結果，請設定10個或更多分鐘的時間，如果平均低優先順序核心計數低於整個期間的臨界值，警示就會觸發。 這可讓您有更多時間來匯總計量，讓您取得更精確的結果。 

若要在 Azure 入口網站中設定計量警示：

1. 選取 [所有服務] > [Batch 帳戶]，然後選取您的 Batch 帳戶名稱。
2. 在 [**監視**] 底下，選取 [**警示**]，然後選取 [**新增警示規則**]。
3. 按一下 [**選取條件**]，然後選擇 [度量]。 確認 [**圖表週期**]、[**閾數值型別**]、[**運算子**] 和 [**匯總類型**] 的值，然後輸入**臨界值**。 然後選取 [完成]****。
4. 透過選取現有動作群組或建立新的動作群組，將動作群組新增至警示。
5. 在 [**警示規則詳細資料**] 區段中，輸入**警示規則名稱**和**描述**，然後選取**嚴重性**
6. 選取 [建立警示規則]。

如需建立計量警示的詳細資訊，請參閱[瞭解計量警示在 Azure 監視器中](../azure-monitor/platform/alerts-metric-overview.md)的使用方式和[使用 Azure 監視器建立、查看和管理計量警示](../azure-monitor/platform/alerts-metric.md)。

您也可以使用 Azure 監視器[REST API](/rest/api/monitor/)來設定近乎即時的警示。 如需詳細資訊，請參閱[Microsoft Azure 中的警示總覽](../azure-monitor/platform/alerts-overview.md)。 若要在您的警示中包含作業、工作或集區特定資訊，請參閱[使用 Azure 監視器警示回應事件](../azure-monitor/learn/tutorial-response.md)中的搜尋查詢相關資訊。

## <a name="batch-diagnostics"></a>Batch 診斷

診斷記錄包含 Azure 資源發出的資訊，其會描述每個資源的作業。 針對 Batch，您可以收集下列記錄：

- 在個別 Batch 資源 (如集區或工作) 存留期間，由 Azure Batch 服務發出的**服務記錄**事件。
- 帳戶層級上的**計量**記錄。

啟用診斷記錄收集的設定並不會預設為啟用。 請為每個您想要監視的 Batch 帳戶明確啟用診斷設定。

### <a name="log-destinations"></a>記錄目的地

常見案例是選取 Azure 儲存體帳戶作為記錄目的地。 若要在 Azure 儲存體中儲存記錄，請在啟用記錄收集之前建立帳戶。 如果您有與 Batch 帳戶產生關聯的儲存體帳戶，您可以選擇該帳戶作為記錄目的地。

或者，您可以：

- 將 Batch 診斷記錄事件串流至 [Azure 事件中樞](../event-hubs/event-hubs-about.md)。 事件中樞每秒可輸入數百萬個事件，您可以使用任何即時分析提供者來轉換和儲存。 
- 將診斷記錄傳送至 [Azure 監視器記錄](../azure-monitor/log-query/log-query-overview.md)，您可以在其中分析這些記錄，或將其匯出以便在 Power BI 或 Excel 中進行分析。

> [!NOTE]
> 使用 Azure 服務儲存或處理診斷記錄資料可能會產生額外費用。 

### <a name="enable-collection-of-batch-diagnostic-logs"></a>啟用 Batch 診斷記錄的收集

若要在 Azure 入口網站中建立新的診斷設定，請遵循下列步驟。

1. 在 [Azure 入口網站中，選取 [**所有服務**]  >  [**Batch 帳戶**]，然後選取您的 Batch 帳戶名稱。
2. 在 [監視]**** 下方，選取 [診斷設定]****。
3. 在 [**診斷設定**] 中，選取 [**新增診斷設定**]。
4. 輸入設定的名稱。
5. 選取目的地： [**傳送至 Log Analytics**]、[封存**至儲存體帳戶**] 或 [**串流至事件中樞**]。 如果您選取儲存體帳戶，您可以選擇性地設定保留原則。 如果您未指定保留天數，資料的保留期限會是儲存體帳戶的存留期間。
6. 選取 [ **ServiceLog**]、[ **AllMetrics**] 或 [兩者]。
7. 選取 [**儲存**] 以建立診斷設定。

您也可以[透過 Azure 入口網站中的 Azure 監視器來啟用收集](../azure-monitor/platform/diagnostic-settings.md)，以使用[Resource Manager 範本](../azure-monitor/platform/diagnostic-settings-template.md)，或使用 Azure PowerShell 或 Azure CLI 來設定診斷設定。 如需詳細資訊，請參閱[Azure 平臺記錄的總覽](../azure-monitor/platform/platform-logs-overview.md)。

### <a name="access-diagnostics-logs-in-storage"></a>存取儲存體中的診斷記錄

如果您將 Batch 診斷記錄封存在儲存體帳戶中，當發生相關事件時，儲存體帳戶中就會建立儲存體容器。 Blob 會根據下列命名模式建立：

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如︰

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

每個 `PT1H.json` Blob 檔案都包含 JSON 格式的事件，這是在 Blob URL 指定時數內 (例如 `h=12`) 發生的事件。 在目前這一小時，事件一發生就會附加到 `PT1H.json` 檔案。 分鐘值 (`m=00`) 一律是 `00`，因為診斷記錄事件是分成每小時的個別 Blob。 (所有時間都是採用 UTC 格式。)

以下是 `PT1H.json` 記錄檔中 `PoolResizeCompleteEvent` 項目的範例。 其中包含專用節點和低優先順序節點目前數目和目標數目的相關資訊，以及作業的開始和結束時間：

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

如需儲存體帳戶中診斷記錄架構的詳細資訊，請參閱將[Azure 資源記錄封存至儲存體帳戶](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)。 若要以程式設計方式存取您儲存體帳戶中的記錄，請使用儲存體 API。

### <a name="service-log-events"></a>服務記錄檔事件

Azure Batch 服務記錄檔（如果收集）包含在個別 Batch 資源（例如集區或工作）的存留期間，由 Azure Batch 服務發出的事件。 Batch 發出的每個事件都會以 JSON 格式記錄。 例如，**集區建立事件**範例的主體為：

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Batch 服務所發出的服務記錄事件包括下列各項：

- [建立集區](batch-pool-create-event.md)
- [開始刪除集區](batch-pool-delete-start-event.md)
- [完成集區刪除](batch-pool-delete-complete-event.md)
- [開始調整集區大小](batch-pool-resize-start-event.md)
- [完成集區大小調整](batch-pool-resize-complete-event.md)
- [開始工作](batch-task-start-event.md)
- [完成工作](batch-task-complete-event.md)
- [工作失敗](batch-task-fail-event.md)

## <a name="next-steps"></a>後續步驟

- 了解可用來建置 Batch 解決方案的 [Batch API 和工具](batch-apis-tools.md)。
- 深入了解[監視 Batch 解決方案](monitoring-overview.md)。
