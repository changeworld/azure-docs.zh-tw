---
title: '使用 Azure 資料總管 Azure 監視器查詢資料 (預覽) '
description: 使用 Azure 資料總管在 Azure 監視器中的 Azure 資料總管、Log Analytics 工作區和傳統 Application Insights 應用程式之間執行交叉乘積查詢。
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: a31ef69d84f64e4bcaa46adac26a29d2cc367351
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731695"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 資料總管來查詢 Azure 監視器中的資料 (預覽)

Azure 資料總管支援 Azure 資料總管、 [Application Insights (AI) ](../app/app-insights-overview.md)和 [LOG Analytics (LA) ](./data-platform-logs.md)之間的跨服務查詢。 然後，您可以使用 Azure 資料總管工具來查詢 Log Analytics/Application Insights 工作區，並在跨服務查詢中加以參考。 本文說明如何進行跨服務查詢，以及如何將 Log Analytics/Application Insights 工作區新增至 Azure 資料總管 Web UI。

Azure 資料總管跨服務查詢流程： :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="azure 資料瀏覽器 proxy 流程。":::

> [!NOTE]
> * 從 azure 資料總管直接從 Azure 資料總管用戶端工具查詢 Azure 監視器資料，或透過在 Azure 資料總管叢集上執行查詢來間接查詢資料的功能處於預覽模式。
>* 如有任何問題，請洽詢「 [跨服務查詢](mailto:adxproxy@microsoft.com) 」團隊。

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>將 Log Analytics/Application Insights 工作區新增至 Azure 資料總管用戶端工具

1. 在連線到 Log Analytics 或 Application Insights 叢集之前，請先確認您的 Azure 資料總管原生叢集 (例如 help 叢集) 已出現在左側功能表上。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure 資料總管原生叢集。":::

 在 Azure 資料總管 UI (https://dataexplorer.azure.com/clusters) 中，選取 [新增叢集]。

2. 在 [新增叢集] 視窗中，新增 LA 或 AI 叢集的 URL。

    * LA：`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * AI：`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * 選取 [新增]  。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="新增叢集。":::
 
>[!NOTE]
>如果您將連線新增至一個以上的 Log Analytics/Application insights 工作區，請提供每個不同的名稱。 否則，左窗格中的名稱會全部相同。

 建立連線之後，您的 Log Analytics 或 Application Insights 工作區將會出現在左窗格中，並顯示您的原生 Azure 資料總管叢集。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics 和 Azure 資料總管叢集。":::
 
> [!NOTE]
> 可以對應的 Azure 監視器工作區數目上限為 100 個。

## <a name="create-queries-using-azure-monitor-data"></a>使用 Azure 監視器資料建立查詢

您可以使用支援 Kusto 查詢的用戶端工具來執行查詢，例如：Kusto Explorer、Azure 資料總管 Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、Lens、REST API。

> [!NOTE]
> 「跨服務查詢」功能僅供資料抓取之用。 如需詳細資訊，請參閱[函式支援性](#function-supportability)。

> [!TIP]
> * 資料庫名稱的名稱應該與跨服務查詢中指定的資源相同。 名稱區分大小寫。
> * 在跨叢集查詢中，請確定 Application Insights 應用程式和 Log Analytics 工作區的命名是正確的。
> * 如果名稱包含特殊字元，則會以跨服務查詢中的 URL 編碼來取代它們。
> * 如果名稱中包含不符合 [KQL 識別碼名稱規則](/azure/data-explorer/kusto/query/schema-entities/entity-names)的字元，則會以破折號 **-** 字元來取代。

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>從 Azure 資料總管用戶端工具直接查詢您的 Log Analytics 或 Application Insights 工作區

在您的 Log Analytics 或 Application Insights 工作區上執行查詢。 確認左側窗格中已選取您的工作區。
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="查詢 Log Analytics 工作區。":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>對 Log Analytics 或 Application Insights 和 Azure 資料總管原生叢集進行交叉查詢

當您執行跨叢集服務查詢時，請確認左側窗格中已選取您的 Azure 資料總管原生叢集。 下列範例示範如何搭配 [使用 union](/azure/data-explorer/kusto/query/unionoperator) 與 Log Analytics 工作區來合併 Azure 資料總管叢集資料表。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="從 Azure 資料總管的跨服務查詢。":::

若使用 [`join` 運算子](/azure/data-explorer/kusto/query/joinoperator) (而不是 union)，可能需要 [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) 在 Azure 資料總管原生叢集上執行該運算子。

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>將一個租用戶中的 Azure 資料總管叢集資料與另一個租用戶中的 Azure 監視器資源資料聯結

不支援服務之間的跨租使用者查詢。 您會登入單一租用戶來執行橫跨這兩個資源的查詢。

如果 Azure 資料總管資源是在租用戶 'A' 中，而 Log Analytics 工作區是在租用戶 'B' 中，請使用下列兩種方法的其中一種：

1. Azure 資料總管可讓您為不同租用戶中的主體新增角色。 將租用戶 'B' 中的使用者識別碼新增為 Azure 資料總管叢集上的授權使用者。 驗證 Azure 資料總管叢集上的 ['TrustedExternalTenant'](/powershell/module/az.kusto/update-azkustocluster) 屬性是否包含租用戶 'B'。 在租用戶 'B' 中完整地執行交叉查詢。

2. 使用 [Lighthouse](../../lighthouse/index.yml) 將 Azure 監視器資源投射到租用戶 'A'。
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>從不同的租用戶連線到 Azure 資料總管叢集

Kusto Explorer 會自動將您登入使用者帳戶原先所屬的租用戶。 若要使用相同的使用者帳戶存取其他租用戶中的資源，必須在連接字串中明確指定 `tenantId`：`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>函式支援性

Azure 資料總管跨服務查詢支援 Application Insights 和 Log Analytics 的功能。
這項功能可讓跨叢集查詢直接參考 Azure 監視器表格式函式。
跨服務查詢支援下列命令：

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

下圖描述從 Azure 資料總管 Web UI 查詢表格式函式的範例。
若要使用函式，請在查詢視窗中執行名稱。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="從 Azure 資料總管 Web UI 查詢表格式函數。":::

## <a name="additional-syntax-examples"></a>其他語法範例

呼叫 Log Analytics 或 Application Insights 叢集時，可以使用下列語法選項：

|語法描述  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 資料庫位在只包含此訂用帳戶中已定義資源的叢集中 (**建議用於跨叢集查詢**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| 包含此訂用帳戶中所有應用程式/工作區的叢集    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|叢集包含訂用帳戶中屬於此資源群組成員的所有應用程式/工作區    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|僅包含此訂用帳戶中已定義資源的叢集      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Log Analytics 工作區和 Application Insights 的資料結構](data-platform-logs.md)。
- 瞭解如何 [在 Azure 資料總管中撰寫查詢](/azure/data-explorer/write-queries)。