---
title: 計量、警示和診斷記錄 - Azure Batch | Microsoft Docs
description: 記錄並分析 Azure Batch 帳戶資源 (如集區和工作) 的診斷記錄事件。
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 68d5976a5a79dbde88b7f80b02b39793ffc86de9
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254848"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>用於診斷評估和監視的 Batch 計量、警示和記錄

 
本文說明如何使用 [Azure 監視器](../azure-monitor/overview.md)的功能來監視 Batch 帳戶。 Azure 監視器會收集您 Batch 帳戶中的資源[計量](../azure-monitor/platform/data-platform-metrics.md)和[診斷記錄](../azure-monitor/platform/platform-logs-overview.md)。 透過各種方式收集及使用此資料，以監視您的 Batch 帳戶和診斷問題。 您也可以設定[計量警示](../azure-monitor/platform/alerts-overview.md)，在計量達到指定值時接收通知。 

## <a name="batch-metrics"></a>Batch 計量

計量是指 Azure 遙測資料 (也稱為效能計數器)，由 Azure 監視器服務取用的 Azure 資源所發出。 Batch 帳戶中的範例計量包括：集區建立事件、低優先順序的節點計數及工作完成事件。 

請參閱[支援的 Batch 計量清單](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)。

計量：

* 在每個 Batch 帳戶中預設啟用，不需要額外設定
* 每隔 1 分鐘產生
* 不會自動保存，但是有 30 天的累積記錄。 您可以保存活動計量以作為診斷記錄的一部分。

### <a name="view-metrics"></a>檢視計量

在 Azure 入口網站中檢視 Batch 帳戶的計量。 帳戶的**概觀**頁面會預設顯示索引鍵節點、核心和工作計量。 

檢視所有 Batch 帳戶計量： 

1. 在入口網站中，按一下 [所有服務] > [Batch 帳戶]，然後按一下您的 Batch 帳戶名稱。
2. 在 [監視] 下，按一下 [計量]。
3. 選取一或多個計量。 如有需要，可使用 [訂閱]、[資源群組]、[資源類型]和 [資源] 下拉式清單，來選取其他資源計量。
    * 針對以計數為基礎的計量（例如「專用核心計數」或「低優先順序節點計數」），請使用「平均」匯總。 若為以事件為基礎的計量（例如「集區調整大小完成事件」），請使用「計數」匯總。

> [!WARNING]
> 請勿使用「總和」匯總，這會加總在圖表期間收到的所有資料點值
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

若要以程式設計方式擷取計量，請使用 Azure 監視器 API。 例如，請參閱[使用 .NET 擷取 Azure 監視器計量](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)。

## <a name="batch-metric-reliability"></a>Batch 計量可靠性

計量用於趨勢與資料分析。 計量傳遞不受到保證，且會有未按順序傳遞、遺失資料和/或重複的狀況。 不建議使用單一事件警示或觸發程序函數。 請參閱 [Batch 計量警示](#batch-metric-alerts)一節，以取得如何設定警示臨界值的詳細資訊。

系統仍會彙總過去 3 分鐘發出的計量。 在此時段中，計量值可能會被短報。

## <a name="batch-metric-alerts"></a>Batch 計量警示

(選擇性) 設定幾近即時的「計量警示」，當指定的計量值超出您指派的閾值時，就會觸發此警示。 當警示為「已啟動」時 (超出閾值且符合警示條件時)，以及當警示為「已解決」時 (再次超出閾值且不再符合條件時)，您選擇的警示會產生[通知](../monitoring-and-diagnostics/insights-alerts-portal.md)。 不建議使用以單一資料量為基礎的警示，因為計量可能會未按順序傳遞、遺失資料，和/或重複的狀況。 警示應使用臨界值說明這些不一致。

例如，您可以在低優先順序核心計數降至特定層級時設定計量警示，以便您調整集區的構成要素。 建議設定 10 分鐘以上的期間，在這段期間，如果平均的低優先順序核心計數低於整段期間的臨界值，就會觸發警示。 建議 1-5 分鐘警示，因為仍可能會彙總計量。

在入口網站中設定計量警示：

1. 按一下 [所有服務] > [Batch 帳戶]，然後按一下您的 Batch 帳戶名稱。
2. 在 [監視] 下方，按一下 [警示規則] > [新增計量警示]。
3. 選取計量、警示條件 (例如計量在某個期間超過特定值)，以及一個或多個通知。

您也可以使用 [REST API](https://docs.microsoft.com/rest/api/monitor/) 設定幾近即時的警示。 如需詳細資訊，請參閱[警示總覽](../azure-monitor/platform/alerts-overview.md)。 若要在您的警示中包含作業、工作或集區特定資訊，請參閱[使用 Azure 監視器警示回應事件](../azure-monitor/learn/tutorial-response.md)中的搜尋查詢相關資訊

## <a name="batch-diagnostics"></a>Batch 診斷

診斷記錄包含 Azure 資源發出的資訊，其會描述每個資源的作業。 針對 Batch，您可以收集下列記錄：

* 在個別 Batch 資源 (如集區或工作) 存留期間，由 Azure Batch 服務發出的**服務記錄**事件。 

* 帳戶層級上的**計量**記錄。 

啟用診斷記錄收集的設定並不會預設為啟用。 請為每個您想要監視的 Batch 帳戶明確啟用診斷設定。

### <a name="log-destinations"></a>記錄目的地

常見案例是選取 Azure 儲存體帳戶作為記錄目的地。 若要在 Azure 儲存體中儲存記錄，請在啟用記錄收集之前建立帳戶。 如果您有與 Batch 帳戶產生關聯的儲存體帳戶，您可以選擇該帳戶作為記錄目的地。 

診斷記錄目的地的其他選項：

* 將 Batch 診斷記錄事件串流至 [Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)。 事件中樞每秒可輸入數百萬個事件，您可以使用任何即時分析提供者來轉換和儲存。 

* 將診斷記錄傳送至[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)檔，您可以在其中分析它們，或將它們匯出以在 Power BI 或 Excel 中進行分析。

> [!NOTE]
> 使用 Azure 服務儲存或處理診斷記錄資料可能會產生額外費用。 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>啟用 Batch 診斷記錄的收集

1. 在入口網站中，按一下 [所有服務] > [Batch 帳戶]，然後按一下您的 Batch 帳戶名稱。
2. 在 [監視] 下方，按一下 [診斷記錄] > [開啟診斷]。
3. 在 [**診斷設定**] 中，輸入設定的名稱，然後選擇記錄目的地（現有的儲存體帳戶、事件中樞或 Azure 監視器記錄）。 選取 **ServiceLog** 或/和 **AllMetrics**。

    當您選取儲存體帳戶時，可選擇性地設定保留原則。 如果您未指定保留天數，資料的保留期限會是儲存體帳戶的存留期間。

4. Haga clic en **Guardar**.

    ![Batch 診斷](media/batch-diagnostics/diagnostics-portal.png)

啟用記錄收集的其他選項包括：使用入口網站中的 Azure 監視器設定診斷設定、使用 [Resource Manager 範本](../azure-monitor/platform/diagnostic-settings-template.md)或使用 Azure PowerShell 或 Azure CLI。 請參閱[收集並取用來自 Azure 資源的記錄資料](../azure-monitor/platform/platform-logs-overview.md)。


### <a name="access-diagnostics-logs-in-storage"></a>存取儲存體中的診斷記錄

如果您將 Batch 診斷記錄封存在儲存體帳戶中，當發生相關事件時，儲存體帳戶中就會建立儲存體容器。 Blob 會根據下列命名模式建立：

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
範例：

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
每個 `PT1H.json` blob 檔案都包含 JSON 格式的事件，在 blob URL 中所指定的一小時內發生（例如，`h=12`）。 在目前的一小時期間，事件會在發生時附加至 `PT1H.json` 檔案。 分鐘值（`m=00`）一律 `00`，因為診斷記錄事件會分成每小時的個別 blob。 (所有時間都是採用 UTC 格式。)

以下是 `PT1H.json` 記錄檔中 `PoolResizeCompleteEvent` 專案的範例。 其中包含專用和低優先順序節點目前和目標數目的相關資訊，以及作業的開始和結束時間：

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

若要深入了解儲存體帳戶中的診斷記錄結構描述，請參閱[封存 Azure 診斷記錄](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account)。 若要以程式設計方式存取您儲存體帳戶中的記錄，請使用儲存體 API。 

### <a name="service-log-events"></a>服務記錄檔事件
已收集的 Azure Batch 服務記錄中包含在個別 Batch 資源 (如集區或工作) 存留期間，由 Azure Batch 服務發出的事件。 Batch 發出的每個事件都會以 JSON 格式記錄。 例如，**集區建立事件**範例的主體為：

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

Batch 服務目前會發出下列服務記錄檔事件。 這份清單可能不完整，因為自上次更新此文件後可能已新增額外的事件。

| **服務記錄檔事件** |
| --- |
| [建立集區](batch-pool-create-event.md) |
| [開始刪除集區](batch-pool-delete-start-event.md) |
| [完成集區刪除](batch-pool-delete-complete-event.md) |
| [開始調整集區大小](batch-pool-resize-start-event.md) |
| [完成集區大小調整](batch-pool-resize-complete-event.md) |
| [開始工作](batch-task-start-event.md) |
| [完成工作](batch-task-complete-event.md) |
| [工作失敗](batch-task-fail-event.md) |



## <a name="next-steps"></a>後續步驟

* 了解可用來建置 Batch 解決方案的 [Batch API 和工具](batch-apis-tools.md)。
* 深入了解[監視 Batch 解決方案](monitoring-overview.md)。
