---
title: 估計 Azure Functions 中的耗用量方案成本
description: 瞭解如何在 Azure 中的取用方案中執行函數應用程式時，更清楚地預估可能產生的成本。
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 0e3177d7c65eb1624441427f123e6f95095bdbbd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76963983"
---
# <a name="estimating-consumption-plan-costs"></a>估計取用方案成本

在 Azure Functions 中執行的應用程式目前有三種類型的主控方案，每個方案都有自己的定價模型： 

| 計畫 | 描述 |
| ---- | ----------- |
| [**率**](functions-scale.md#consumption-plan) | 您只需針對函數應用程式執行的時間付費。 此方案包含每個訂用帳戶的[免費授]與[定價頁面]。|
| [**Premium**](functions-scale.md#premium-plan) | 提供您與取用方案相同的功能和調整機制，但具備增強的效能和 VNET 存取權。 成本是根據您選擇的定價層。 若要深入瞭解，請參閱[Azure Functions Premium 方案](functions-premium-plan.md)。 |
| [**專用（App Service）**](functions-scale.md#app-service-plan) <br/>（基本層或更高版本） | 當您需要在專用 Vm 或隔離中執行時，請使用自訂映射，或想要使用您超額的 App Service 方案容量。 使用[一般 App Service 方案計費](https://azure.microsoft.com/pricing/details/app-service/)。 成本是根據您選擇的定價層。|

您選擇最能支援函數效能和成本需求的方案。 若要深入了解，請參閱 [Azure Functions 規模調整和主控](functions-scale.md)。

本文只會處理取用方案，因為此計畫會產生可變成本。 這篇文章取代了取用[方案成本計費常見問題](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)文章。

Durable Functions 也可以在取用量方案中執行。 若要深入瞭解使用 Durable Functions 時的成本考慮，請參閱[Durable Functions 計費](./durable/durable-functions-billing.md)。

## <a name="consumption-plan-costs"></a>使用量方案成本

單一函式執行的執行*成本*是以*GB*為單位來測量。 執行成本的計算方式是將其記憶體使用量與執行時間合併。 針對較長成本執行的函式會更多，如同耗用更多記憶體的函數一樣。 

假設函式所使用的記憶體數量保持不變。 在此情況下，計算成本是簡單的乘法。 例如，假設您的函式已耗用 0.5 GB 3 秒。 然後執行成本為`0.5GB * 3s = 1.5 GB-seconds`。 

由於記憶體使用量會隨著時間而改變，因此計算基本上是一段時間內記憶體使用量的整數。  系統會以固定間隔取樣進程的記憶體使用量（連同子進程）來進行這項計算。 如[定價頁面]中所述，記憶體使用量會無條件進位到最接近的 128-MB 值區。 當您的處理常式使用 160 MB 時，會向您收取 256 MB 的費用。 計算會將並行處理納入考慮，這是相同進程中的多個並行函數執行。

> [!NOTE]
> 雖然 CPU 使用量不會直接被視為執行成本，但它可能會影響函式執行時間的成本。

## <a name="other-related-costs"></a>其他相關成本

當您在任何計畫中評估執行函式的整體成本時，請記住函式執行時間會使用數個其他 Azure 服務，而每個服務分別計費。 計算函式應用程式的定價時，您與其他 Azure 服務整合的所有觸發程式和系結，都需要您建立並支付那些額外的服務。 

對於在取用方案中執行的函式，總成本是您函式的執行成本，加上頻寬和其他服務的成本。 

估計函數應用程式和相關服務的整體成本時，請使用[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=functions)。 

| 相關成本 | 描述 |
| ------------ | ----------- |
| **儲存體帳戶** | 每個函式應用程式都需要您擁有相關聯的一般用途[Azure 儲存體帳戶](../storage/common/storage-introduction.md#types-of-storage-accounts)，這會[分別計費](https://azure.microsoft.com/pricing/details/storage/)。 此帳戶是由函式執行時間在內部使用，但您也可以將它用於儲存體觸發程式和系結。 如果您沒有儲存體帳戶，則會在建立函數應用程式時為您建立一個。 若要深入瞭解，請參閱[儲存體帳戶需求](storage-considerations.md#storage-account-requirements)。|
| **Application Insights** | 函式依賴[Application Insights](../azure-monitor/app/app-insights-overview.md) ，為您的函式應用程式提供高效能的監視體驗。 雖然並非必要，但您應該[啟用 Application Insights 整合](functions-monitoring.md#enable-application-insights-integration)。 每個月都會包含免費的遙測資料授與。 若要深入瞭解，請參閱[Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)。 |
| **網路頻寬** | 您不需支付相同區域中 Azure 服務之間的資料傳輸費用。 不過，輸出資料傳輸至另一個區域或 Azure 外部可能會產生成本。 若要深入瞭解，請參閱[頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。 |

## <a name="behaviors-affecting-execution-time"></a>影響執行時間的行為

函數的下列行為可能會影響執行時間：

+ **觸發程式和**系結：在函式系結中讀取輸入和將輸出寫入至函式系結時所花費的時間[，會計算](functions-triggers-bindings.md)為執行時間。 例如，當您的函式使用輸出系結將訊息寫入至 Azure 儲存體佇列時，您的執行時間會包含將訊息寫入佇列所需的時間，這會包含在函數成本的計算中。 

+ **非同步執行**：函式等候非同步要求結果的時間（`await`以 c # 表示）會視為執行時間。 GB 秒的計算是以函式的開始和結束時間，以及該期間內的記憶體使用量為基礎。 在這段時間內，CPU 活動不會考慮到計算中的情況。 您可以使用[Durable Functions](durable/durable-functions-overview.md)，在非同步作業期間降低成本。 在協調器函式中，您不需支付花費在等候的時間。

## <a name="view-execution-data"></a>查看執行資料

在[您的發票](/azure/billing/billing-download-azure-invoice)中，您可以查看總執行的成本相關資料 **-函數**和**執行時間函數**，以及實際的計費成本。 不過，此發票資料是過去發票期間的每月匯總。 

若要進一步瞭解函數的成本影響，您可以使用 Azure 監視器來查看您的函式應用程式目前所產生的成本相關計量。 您可以使用[Azure 入口網站]或 REST api 中的[Azure 監視器計量瀏覽器](../azure-monitor/platform/metrics-getting-started.md)來取得此資料。

### <a name="monitor-metrics-explorer"></a>監視計量瀏覽器

使用[Azure 監視器計量瀏覽器](../azure-monitor/platform/metrics-getting-started.md)，以圖形格式為您的取用方案函式應用程式來查看成本相關資料。 

1. 在**搜尋服務、資源和**檔`monitor`的[Azure 入口網站]頂端，搜尋並選取 [**服務**] 底下的 [**監視**]。

1. 在左側選取 [**計量** > ] [**選取資源**]，然後使用影像下方的設定來選擇您的函數應用程式。

    ![選取您的函數應用程式資源](media/functions-consumption-costing/select-a-resource.png)

      
    |設定  |建議的值  |描述  |
    |---------|---------|---------|
    | 訂用帳戶    |  您的訂用帳戶  | 您的函數應用程式的訂用帳戶。  |
    | 資源群組     | 您的資源群組  | 包含函數應用程式的資源群組。   |
    | 資源類型     |  應用程式服務 | 函數應用程式會顯示為監視器中的應用程式服務實例。 |
    | 資源     |  您的函數應用程式  | 要監視的函數應用程式。        |

1. 選取 [套用]**以選擇您**的函式應用程式作為要監視的資源。

1. 從 **[計量] 中，** 選擇 [**函數執行計數**] 和 [匯總**的****總和**]。 這會將所選期間內執行計數的總和加到圖表中。

    ![定義要加入至圖表的函式應用程式度量](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. 選取 [**新增度量**] 並重複步驟2-4，將函式**執行單位**加入至圖表。 

產生的圖表會包含所選時間範圍內這兩個執行計量的總計，在此案例中為兩小時。

![函式執行計數和執行單位的圖形](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

當執行單位數目大於執行計數時，圖表只會顯示執行單位。

此圖表顯示在兩小時期間`Function Execution Units`內使用的總計1110000000，以 MB-毫秒為單位。 若要轉換成 GB 秒，請除以1024000。 在此範例中，函式應用`1110000000 / 1024000 = 1083.98`程式已耗用 GB-秒。 您可以採用此值並乘以函式[定價頁面][定價頁面]上目前的執行時間價格，這會提供這兩個小時的費用，假設您已使用任何免費的執行時間授與。 

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/)具有用來抓取計量的命令。 您可以從本機命令環境或使用[Azure Cloud Shell](../cloud-shell/overview.md)直接從入口網站使用 CLI。 例如，下列[az monitor 計量 list](/cli/azure/monitor/metrics#az-monitor-metrics-list)命令會傳回過去使用的相同時間週期內的每小時資料。

請務必將取代`<AZURE_SUBSCRIPTON_ID>`為您執行命令的 Azure 訂用帳戶識別碼。

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

此命令會傳回如下列範例所示的 JSON 承載：

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
此特定回應顯示從`2019-09-11T21:46`到`2019-09-11T23:18`，應用程式耗用 1110000000 MB-毫秒（1083.98 GB-秒）。

## <a name="determine-memory-usage"></a>判斷記憶體使用量

函式執行單位是執行時間和記憶體使用量的組合，這讓它成為瞭解記憶體使用量的難以辨識。 記憶體資料不是目前透過 Azure 監視器提供的度量。 不過，如果您想要將應用程式的記憶體使用量優化，可以使用 Application Insights 所收集的效能計數器資料。  

如果您尚未這麼做，請[在您的函數應用程式中啟用 Application Insights](functions-monitoring.md#enable-application-insights-integration)。 啟用此整合之後，您就可以[在入口網站中查詢此遙測資料](functions-monitoring.md#query-telemetry-data)。  

在 [**監視**] 底下，選取 [**記錄（分析）**]，然後複製下列遙測查詢並貼到查詢視窗中，然後選取 [**執行**]。 此查詢會傳回每個取樣時間的總記憶體使用量。

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

結果看起來如下列範例所示：

| UTC \[時間戳記\]          | 名稱          | value       |
|----------------------------|---------------|-------------|
| 9/12/2019，1:05:14\.947 AM | 私用位元組 | 209932288 |
| 9/12/2019，1:06:14\.994 AM | 私用位元組 | 212189184 |
| 9/12/2019，1:06:30\.010 AM | 私用位元組 | 231714816 |
| 9/12/2019，1:07:15\.040 AM | 私用位元組 | 210591744 |
| 9/12/2019，1:12:16\.285 AM | 私用位元組 | 216285184 |
| 9/12/2019，1:12:31\.376 AM | 私用位元組 | 235806720 |

## <a name="function-level-metrics"></a>函數層級計量

Azure 監視器會追蹤資源層級的計量，而函式是函式應用程式。 Application Insights 整合會針對每個函式來發出計量。 以下是取得函數的平均持續時間的範例分析查詢：

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| 名稱                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| QueueTrigger MinDurationMs | 8\.522                      |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入瞭解監視函數應用程式](functions-monitoring.md)

[定價頁面]:https://azure.microsoft.com/pricing/details/functions/
[Azure 入口網站]: https://portal.azure.com
