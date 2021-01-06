---
title: 估計 Azure Functions 中的耗用量方案成本
description: 瞭解如何在 Azure 的取用方案中執行函數應用程式時，更有效地評估可能產生的成本。
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 430804d478df718f51ae1da9adb6693f597157a9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934878"
---
# <a name="estimating-consumption-plan-costs"></a>估計取用方案成本

在 Azure Functions 中執行的應用程式目前有三種類型的主控方案，每個方案都有自己的定價模型： 

| 規劃 | 說明 |
| ---- | ----------- |
| [**耗用量**](consumption-plan.md) | 您只需針對函數應用程式執行的時間付費。 此方案包含每個訂用帳戶的[免費授]與[定價頁面]。|
| [**Premium**](functions-premium-plan.md) | 提供您與取用方案相同的功能和調整機制，但具備增強的效能和 VNET 存取權。 成本是以您選擇的定價層為基礎。 若要深入瞭解，請參閱 [Azure Functions Premium 方案](functions-premium-plan.md)。 |
| [**專用 (App Service)**](dedicated-plan.md) <br/> (基本層或更高)  | 當您需要在專用 Vm 或隔離中執行時，請使用自訂映射，或想要使用超出 App Service 的方案容量。 使用 [定期 App Service 方案計費](https://azure.microsoft.com/pricing/details/app-service/)。 成本是以您選擇的定價層為基礎。|

您選擇最能支援您的函數效能和成本需求的方案。 若要深入了解，請參閱 [Azure Functions 規模調整和主控](functions-scale.md)。

本文只會處理取用方案，因為此方案會產生變動成本。 本文將取代取用 [方案成本計費常見問題](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) 文章。

Durable Functions 也可以在取用方案中執行。 若要深入瞭解使用 Durable Functions 時的成本考慮，請參閱 [Durable Functions 帳單](./durable/durable-functions-billing.md)。

## <a name="consumption-plan-costs"></a>使用量方案成本

單一函式執行的執行 *成本* 是以 *GB 為單位* 來測量。 執行成本的計算方式是將其記憶體使用量與執行時間結合在一起。 執行較長成本的函式，也就是耗用更多記憶體的函數。 

假設函數所使用的記憶體數量維持不變。 在此情況下，計算成本是簡單的乘法。 例如，假設您的函式在3秒內耗用了 0.5 GB。 然後，執行成本為 `0.5GB * 3s = 1.5 GB-seconds` 。 

由於記憶體使用量會隨著時間而改變，因此計算本質上是一段時間內記憶體使用量的整數。  系統會藉由取樣進程 (的記憶體使用量，以及) 固定間隔的子進程，來進行這項計算。 如 [定價] [頁面]所述，記憶體使用量會進位到最接近的 128-MB 值區。 當您的進程使用 160 MB 時，您需要支付 256 MB 的費用。 計算會考慮平行存取，也就是相同進程中的多個並行函數執行。

> [!NOTE]
> 雖然不會在執行成本中直接考慮 CPU 使用量，但它可能會影響到函數的執行時間。

針對 HTTP 觸發的函式，如果在函式程式碼開始執行之前發生錯誤，則不需支付執行費用。 這表示由於 API 金鑰驗證或 App Service 的驗證/授權功能，來自平臺的401回應並不會計入您的執行成本。 同樣地，如果5xx 狀態碼回應在處理要求的函式之前出現在平臺中，則不會加以計算。 當您的函式程式碼開始執行之後，由平臺所產生的5xx 回應仍會被視為執行，即使您的函式程式碼未引發錯誤。

## <a name="other-related-costs"></a>其他相關成本

在評估任何計畫中執行函式的整體成本時，請記住，函式執行時間會使用數個其他 Azure 服務，這些服務分別計費。 計算函數應用程式的價格時，與其他 Azure 服務整合的任何觸發程式和系結都需要您建立並支付這些額外的服務。 

針對在取用方案中執行的函式，總成本是您的函式的執行成本，加上頻寬和其他服務的成本。 

估計函數應用程式和相關服務的整體成本時，請使用 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=functions)。 

| 相關成本 | 描述 |
| ------------ | ----------- |
| **儲存體帳戶** | 每個函式應用程式都需要您有相關聯的一般用途 [Azure 儲存體帳戶](../storage/common/storage-introduction.md#types-of-storage-accounts)，此帳戶會 [另外計費](https://azure.microsoft.com/pricing/details/storage/)。 函式執行時間會在內部使用此帳戶，但是您也可以將它用於儲存體觸發程式和系結。 如果您沒有儲存體帳戶，則會在建立函數應用程式時為您建立一個儲存體帳戶。 若要深入了解，請參閱[儲存體帳戶需求](storage-considerations.md#storage-account-requirements)。|
| **Application Insights** | 函式依賴 [Application Insights](../azure-monitor/app/app-insights-overview.md) 為您的函式應用程式提供高效能的監視體驗。 雖然並非必要，但您應該 [啟用 Application Insights 整合](configure-monitoring.md#enable-application-insights-integration)。 每個月都會包含遙測資料的免費授與。 若要深入瞭解，請參閱 [Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)。 |
| **網路頻寬** | 您不需支付相同區域中的 Azure 服務之間的資料傳輸費用。 不過，您可能會因為將資料傳輸到另一個區域或 Azure 外部而產生費用。 若要深入瞭解，請參閱 [頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。 |

## <a name="behaviors-affecting-execution-time"></a>影響執行時間的行為

下列函式的行為可能會影響執行時間：

+ **觸發程式和** 系結：從 [函數](functions-triggers-bindings.md) 系結讀取輸入和將輸出寫入至函式系結所花費的時間，會計算為執行時間。 例如，當您的函式使用輸出系結將訊息寫入至 Azure 儲存體佇列時，您的執行時間會包含將訊息寫入佇列所花費的時間，該訊息會包含在函數成本的計算中。 

+ **非同步執行**：當您的函式等候非同步要求的結果時， (`await` c # ) 會計算為執行時間。 GB 秒計算是以函式的開始和結束時間以及該期間內的記憶體使用量為基礎。 在這段時間內，在 CPU 活動方面所發生的情況並不會納入計算中。 您可以使用 [Durable Functions](durable/durable-functions-overview.md)，在非同步作業期間降低成本。 在協調器函式中等候花費的時間並不計費。

## <a name="viewing-cost-related-data"></a>查看成本相關資料

在 [您的發票](../cost-management-billing/understand/download-azure-invoice.md)中，您可以查看總執行的成本相關資料（函 **式** 和 **執行時間函數**），以及實際計費成本。 不過，此發票資料是過去發票期間的每月匯總。 

### <a name="function-app-level-metrics"></a>函數應用層級計量

若要進一步瞭解函式的成本影響，您可以使用 Azure 監視器來查看函式應用程式目前產生的成本相關計量。 您可以使用[Azure 入口網站]或 REST api 中的[Azure 監視器計量瀏覽器](../azure-monitor/platform/metrics-getting-started.md)來取得此資料。

#### <a name="monitor-metrics-explorer"></a>監視計量瀏覽器

使用 [Azure 監視器計量瀏覽器](../azure-monitor/platform/metrics-getting-started.md) ，以圖形格式針對您的耗用量方案函式應用程式來查看成本相關資料。 

1. 在 **搜尋服務、資源和** 檔的 [Azure 入口網站]頂端，搜尋 [服務] 下的 [ `monitor` 監視] ****，然後選取 [**監視**]。

1. 在左側選取 [**計量**]  >  **選取資源**，然後使用影像下方的設定來選擇您的函式應用程式。

    ![選取您的函數應用程式資源](media/functions-consumption-costing/select-a-resource.png)

      
    |設定  |建議的值  |描述  |
    |---------|---------|---------|
    | 訂用帳戶    |  您的訂用帳戶  | 使用您函式應用程式的訂用帳戶。  |
    | 資源群組     | 您的資源群組  | 包含函數應用程式的資源群組。   |
    | 資源類型     |  應用程式服務 | 函數應用程式會顯示為監視中的應用程式服務實例。 |
    | 資源     |  您的函數應用程式  | 要監視的函數應用程式。        |

1. 選取 [套用] **以選擇您** 的函式應用程式作為要監視的資源。

1. 從 [**度量**] 中，選擇 [**函數執行計數**] 和 [**匯總** **]。** 這會將所選期間內的執行計數加總到圖表中。

    ![定義要新增至圖表的函式應用程式度量](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. 選取 [ **新增** 計量] 並重複步驟2-4，將 **函數執行單位** 新增至圖表。 

產生的圖表包含所選時間範圍內的兩個執行度量總計，在此案例中為兩小時。

![函數執行計數和執行單位的圖表](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

當執行單位數目大於執行計數時，圖表只會顯示執行單位。

此圖表顯示 `Function Execution Units` 在兩小時期間內耗用的1110000000總計（以 MB 為單位）。 若要轉換為 GB 秒，請除以1024000。 在此範例中，函式應用程式已耗用 `1110000000 / 1024000 = 1083.98` GB 秒。 您可以採用此值並乘以 [函式 [定價頁面][定價] 頁面]上目前的執行時間價格，這可為您提供這兩個小時的費用，假設您已使用任何免費的執行時間授與。 

#### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/)具有可用於抓取計量的命令。 您可以從本機命令環境使用 CLI，或直接從入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md)。 例如，下列 [az monitor 計量清單](/cli/azure/monitor/metrics#az-monitor-metrics-list) 命令會傳回過去使用的相同時間週期內的每小時資料。

請務必將取代為 `<AZURE_SUBSCRIPTON_ID>` 您執行命令的 Azure 訂用帳戶識別碼。

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
此特定回應顯示從 `2019-09-11T21:46` 到 `2019-09-11T23:18` ，應用程式耗用 1110000000 MB-毫秒 (1083.98 GB 秒) 。

### <a name="function-level-metrics"></a>函數層級計量

函式執行單位是執行時間和記憶體使用量的組合，因此是瞭解記憶體使用量的困難度量。 記憶體資料不是目前可透過 Azure 監視器取得的度量。 但是，如果您想要將應用程式的記憶體使用量優化，可以使用 Application Insights 所收集的效能計數器資料。  

如果您尚未這麼做，請 [在您的函數應用程式中啟用 Application Insights](configure-monitoring.md#enable-application-insights-integration)。 啟用此整合之後，您可以 [在入口網站中查詢此遙測資料](analyze-telemetry-data.md#query-telemetry-data)。 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入瞭解如何監視函數應用程式](functions-monitoring.md)

[定價頁面]:https://azure.microsoft.com/pricing/details/functions/
[Azure 入口網站]: https://portal.azure.com
