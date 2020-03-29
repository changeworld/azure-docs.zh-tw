---
title: 估計 Azure 函數中的消耗規劃成本
description: 瞭解如何更好地估計在 Azure 中的消耗計畫中運行函數應用時可能產生的成本。
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 0e3177d7c65eb1624441427f123e6f95095bdbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963983"
---
# <a name="estimating-consumption-plan-costs"></a>估計消費規劃成本

目前，在 Azure 函數中運行的應用有三種類型的託管計畫，每個計畫都有自己的定價模型： 

| 計畫 | 描述 |
| ---- | ----------- |
| [**耗用量**](functions-scale.md#consumption-plan) | 僅對函數應用運行時收費。 此計畫包括每個訂閱的[免費授予][定價頁]。|
| [**溢價**](functions-scale.md#premium-plan) | 為您提供與消耗計畫相同的功能和縮放機制，但具有增強的性能和 VNET 存取權限。 成本基於您選擇的定價層。 要瞭解更多資訊，請參閱[Azure 函數高級計畫](functions-premium-plan.md)。 |
| [**專用（應用服務）**](functions-scale.md#app-service-plan) <br/>（基本層或更高） | 當您需要在專用 VM 中運行或單獨運行時，請使用自訂映射，或者希望使用多餘的應用服務方案容量。 使用[常規應用程式服務計畫計費](https://azure.microsoft.com/pricing/details/app-service/)。 成本基於您選擇的定價層。|

您選擇了最能支援您的功能性能和成本要求的計畫。 若要深入了解，請參閱 [Azure Functions 規模調整和主控](functions-scale.md)。

本文僅討論消耗計畫，因為此計畫會導致可變成本。 本文取代[消費規劃成本計費常見問題](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)文章。

持久函數也可以在消耗計畫中運行。 要瞭解有關使用持久函數時的成本注意事項，請參閱[持久函數計費](./durable/durable-functions-billing.md)。

## <a name="consumption-plan-costs"></a>使用量方案成本

單個函數執行的執行*成本*以*GB 秒*為單位。 執行成本的計算方式是將記憶體使用量與其執行時間相結合。 運行更長時間的函數的成本也更大，消耗更多記憶體的函數也是如此。 

考慮函數使用的記憶體量保持不變的情況。 在這種情況下，計算成本是簡單的乘法。 例如，假設您的函數在 3 秒內消耗了 0.5 GB。 然後執行成本為`0.5GB * 3s = 1.5 GB-seconds`。 

由於記憶體使用量隨時間而變化，因此計算本質上是記憶體使用量隨時間而變化的組成部分。  系統通過定期採樣進程的記憶體使用方式（以及子進程）來進行此計算。 如[定價頁上]所述，記憶體使用量四捨五入到最接近的 128 MB 存儲桶。 當進程使用 160 MB 時，您需要支付 256 MB 的費用。 計算考慮併發性，即同一進程中的多個併發函數執行。

> [!NOTE]
> 雖然 CPU 使用率不會直接考慮執行成本，但當它影響函數的執行時間時，可能會影響成本。

## <a name="other-related-costs"></a>其他相關費用

在估計在任何計畫中運行函數的總體成本時，請記住函數運行時使用其他幾個 Azure 服務，這些服務分別計費。 計算函數應用的定價時，您擁有的任何觸發器和綁定都需要您創建和支付這些附加服務。 

對於在消耗計畫中運行的函數，總成本是函數的執行成本，以及頻寬和其他服務的成本。 

估算函數應用和相關服務的總體成本時，請使用[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=functions)。 

| 相關成本 | 描述 |
| ------------ | ----------- |
| **存儲帳戶** | 每個函數應用都要求您具有一個關聯的通用[Azure 存儲帳戶](../storage/common/storage-introduction.md#types-of-storage-accounts)，該帳戶[單獨計費](https://azure.microsoft.com/pricing/details/storage/)。 函數運行時在內部使用此帳戶，但您也可以將其用於存儲觸發器和綁定。 如果您沒有存儲帳戶，則在創建函數應用時為您創建一個存儲帳戶。 要瞭解更多資訊，請參閱[存儲帳戶要求](storage-considerations.md#storage-account-requirements)。|
| **Application Insights** | 功能依賴于[應用程式見解](../azure-monitor/app/app-insights-overview.md)，為您的功能應用提供高性能的監視體驗。 雖然不是必需的，但您應該[啟用應用程式見解集成](functions-monitoring.md#enable-application-insights-integration)。 每個月都會免費授予遙測資料。 要瞭解更多資訊，請參閱[Azure 監視器定價頁](https://azure.microsoft.com/pricing/details/monitor/)。 |
| **網路頻寬** | 您不為同一區域中的 Azure 服務之間的資料傳輸付費。 但是，您可以產生向另一個區域或 Azure 外部傳輸出站資料的成本。 要瞭解更多資訊，請參閱[頻寬定價詳細資訊](https://azure.microsoft.com/pricing/details/bandwidth/)。 |

## <a name="behaviors-affecting-execution-time"></a>影響執行時間的行為

函數的以下行為可能會影響執行時間：

+ **觸發器和綁定**：從函數綁定讀取輸入並將輸出寫入[函數綁定](functions-triggers-bindings.md)所佔用的時間計為執行時間。 例如，當函數使用輸出綁定將消息寫入 Azure 存儲佇列時，執行時間包括將消息寫入佇列所花費的時間，這包含在計算函數成本中。 

+ **非同步執行**：函數等待非同步請求結果的時間（`await`在 C#中）計為執行時間。 GB 秒計算基於函數的開始和結束時間以及該期間的記憶體使用方式。 計算中未考慮在此期間 CPU 活動方面發生的情況。 在非同步作業期間，您可以使用[持久函數](durable/durable-functions-overview.md)來降低成本。 您不會為在協調器功能中等待所花費的時間收費。

## <a name="view-execution-data"></a>查看執行資料

在[發票](/azure/billing/billing-download-azure-invoice)中，您可以查看**總執行- 函數**和執行**時間 - 函數**的成本相關資料，以及實際計費成本。 但是，此發票資料是過去發票期間的每月聚合。 

為了更好地理解函數的成本影響，可以使用 Azure 監視器查看函數應用當前生成的成本相關指標。 您可以使用[Azure 門戶]中的[Azure 監視器指標資源管理器](../azure-monitor/platform/metrics-getting-started.md)或 REST API 來獲取此資料。

### <a name="monitor-metrics-explorer"></a>監視指標資源管理器

使用[Azure 監視器指標資源管理器](../azure-monitor/platform/metrics-getting-started.md)以圖形格式查看消費計畫功能應用的成本相關資料。 

1. 在**搜索服務、資源和文檔**`monitor`中的 Azure[門戶]頂部搜索並選擇 **"服務**"下的 **"監視器**"。

1. 在左側選擇 **"** > 指標**選擇資源**"，然後使用圖像下方的設置選擇函數應用。

    ![選擇函數應用資源](media/functions-consumption-costing/select-a-resource.png)

      
    |設定  |建議的值  |描述  |
    |---------|---------|---------|
    | 訂用帳戶    |  您的訂用帳戶  | 函數應用的訂閱。  |
    | 資源群組     | 您的資源群組  | 包含函數應用的資源組。   |
    | 資源類型     |  應用程式服務 | 功能應用在監視器中顯示為應用服務實例。 |
    | 資源     |  您的功能應用  | 要監視的函數應用。        |

1. 選擇 **"應用"** 以選擇函數應用作為要監視的資源。

1. 從**指標**中，選擇**聚合**的**函數執行計數****和總和**。 這會將所選時間段內的執行計數的總和添加到圖表中。

    ![定義要添加到圖表中的函數應用指標](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. 選擇 **"添加指標**"和"重複步驟 2-4"以將**函數執行單位**添加到圖表中。 

生成的圖表包含所選時間範圍內兩個執行指標的總計，在這種情況下為兩個小時。

![函數執行計數和執行單點陣圖](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

由於執行單位的數量比執行計數多得多，因此圖表僅顯示執行單位。

此圖表顯示在兩小時內總共消耗了 11.1 億`Function Execution Units`，以 MB 毫秒為單位。 要轉換為 GB 秒，請將除以 1024000。 在此示例中，函數應用消耗了`1110000000 / 1024000 = 1083.98`GB 秒。 您可以在["函數定價頁定價"頁上][pricing page]採用此值並乘以執行時間的當前價格，前提是您已經使用了任何免費的執行時間授予，這為您提供了這兩個小時的成本。 

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/)具有用於檢索指標的命令。 可以從本地命令環境使用 CLI，也可以直接從門戶使用[Azure 雲外殼](../cloud-shell/overview.md)。 例如，以下[az 監視器指標清單](/cli/azure/monitor/metrics#az-monitor-metrics-list)命令返回之前使用的相同時間段的每小時資料。

請確保使用運行該`<AZURE_SUBSCRIPTON_ID>`命令的 Azure 訂閱 ID 替換。

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

此命令返回 JSON 負載，如下所示的示例：

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
此特定回應顯示，從`2019-09-11T21:46``2019-09-11T23:18`到 ，應用消耗了 11100000000MB 毫秒（1083.98 GB-秒）。

## <a name="determine-memory-usage"></a>確定記憶體使用方式

函數執行單元是執行時間和記憶體使用方式的組合，這使得它很難理解記憶體使用方式。 記憶體資料不是當前通過 Azure 監視器可用的指標。 但是，如果要優化應用的記憶體使用方式，可以使用應用程式見解收集的效能計數器資料。  

如果尚未這樣做，請[在函數應用中啟用應用程式見解](functions-monitoring.md#enable-application-insights-integration)。 啟用此集成後，您可以在[門戶中查詢此遙測資料](functions-monitoring.md#query-telemetry-data)。  

在 **"監視**"下，選擇 **"日誌（分析"），** 然後複製以下遙測查詢並將其粘貼到查詢視窗中，然後選擇 **"運行**"。 此查詢返回每個採樣時間的總記憶體使用量。

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

結果類似于以下示例：

| 時間戳記\[UTC\]          | NAME          | value       |
|----------------------------|---------------|-------------|
| 2019/9/12 上午 1：05：14\.947 | 私用位元組 | 209,932,288 |
| 2019/9/12 上午 1：06：14\.994 | 私用位元組 | 212,189,184 |
| 2019/9/12 上午 1：06：30\.010 | 私用位元組 | 231,714,816 |
| 2019/9/12 上午 1：07：15\.040 | 私用位元組 | 210,591,744 |
| 2019/9/12 上午 1：12：16\.285 | 私用位元組 | 216,285,184 |
| 2019/9/12 上午 1：12：31\.376 | 私用位元組 | 235,806,720 |

## <a name="function-level-metrics"></a>函數級指標

Azure 監視器跟蹤資源級別的指標，對於函數，這是函數應用。 應用程式見解集成基於每個函數發出指標。 下面是一個示例分析查詢，用於獲取函數的平均持續時間：

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| NAME                       | 平均持續時間毫秒 |
|----------------------------|-----------------------------|
| 佇列觸發平均持續時間M | 16\.087                     |
| 佇列觸發最大持續時間M | 90\.249                     |
| 佇列觸發最小持續時間M | 8\.522                      |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解有關監視功能應用的更多資訊](functions-monitoring.md)

[定價頁面]:https://azure.microsoft.com/pricing/details/functions/
[Azure 門戶]: https://portal.azure.com
