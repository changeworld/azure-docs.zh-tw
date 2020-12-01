---
title: '使用 Azure 資料總管 Azure 監視器查詢資料 (預覽) '
description: 使用 Azure 資料總管在 Azure 監視器中的 Azure 資料總管、Log Analytics 工作區和傳統 Application Insights 應用程式之間執行交叉乘積查詢。
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: dd29b18dda46a00966a0e5adea7e06be8f43ad35
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444950"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 資料總管 Azure 監視器查詢資料 (預覽) 

Azure 資料總管支援 Azure 資料總管、 [Application Insights (AI) ](/azure/azure-monitor/app/app-insights-overview)和 [LOG Analytics (LA) ](/azure/azure-monitor/platform/data-platform-logs)之間的跨服務查詢。 然後，您可以使用 Azure 資料總管工具來查詢 Log Analytics/Application Insights 工作區，並在跨服務查詢中加以參考。 本文說明如何進行跨服務查詢，以及如何將 Log Analytics/Application Insights 工作區新增至 Azure 資料總管 Web UI。

Azure 資料總管跨服務查詢流程： :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="azure 資料瀏覽器 proxy 流程。":::

> [!NOTE]
> * 從 azure 資料總管直接從 Azure 資料總管用戶端工具查詢 Azure 監視器資料，或透過在 Azure 資料總管叢集上執行查詢來間接查詢資料的功能處於預覽模式。
>* 如有任何問題，請洽詢「 [跨服務查詢](mailto:adxproxy@microsoft.com) 」團隊。

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>將 Log Analytics/Application Insights 工作區新增至 Azure 資料總管用戶端工具

1. 在連線到 Log Analytics 或 Application Insights 叢集之前，請先確認您的 Azure 資料總管原生叢集 (（例如 *help* cluster) ）會出現在左側功能表中。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure 資料總管原生叢集。":::

 在 Azure 資料總管 UI (中 https://dataexplorer.azure.com/clusters) ，選取 [ **新增** 叢集]。

2. 在 [ **新增** 叢集] 視窗中，新增 LA 或 AI 叢集的 URL。

    * 適用于 LA： `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * 針對 AI： `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * 選取 [新增]  。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="新增叢集。":::
 
>[!NOTE]
>如果您將連線新增至一個以上的 Log Analytics/Application insights 工作區，請提供每個不同的名稱。 否則，它們會在左窗格中擁有相同的名稱。

 建立連線之後，您的 Log Analytics 或 Application Insights 工作區將會出現在您的原生 Azure 資料總管叢集的左窗格中。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics 和 Azure 資料總管叢集。":::
 
> [!NOTE]
> 可以對應的 Azure 監視器工作區數目限制為100。

## <a name="create-queries-using-azure-monitor-data"></a>使用 Azure 監視器資料建立查詢

您可以使用支援 Kusto 查詢的用戶端工具來執行查詢，例如： Kusto Explorer、Azure 資料總管 Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、鏡頭、REST API。

> [!NOTE]
> 「跨服務查詢」功能僅供資料抓取之用。 如需詳細資訊，請參閱 [函數可支援](#function-supportability)性。

> [!TIP]
> * 資料庫名稱的名稱應該與跨服務查詢中指定的資源相同。 名稱區分大小寫。
> * 在跨叢集查詢中，請確定 Application Insights apps 與 Log Analytics 工作區的命名正確。
> * 如果名稱包含特殊字元，則會以跨服務查詢中的 URL 編碼來取代它們。
> * 如果名稱包含的字元不符合 [KQL 識別碼名稱規則](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names)，則會以虛線字元取代這些字元 **-** 。

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>從 Azure 資料總管用戶端工具直接查詢 Log Analytics 或 Application Insights 工作區

在您的 Log Analytics 或 Application Insights 工作區上執行查詢。 確認已在左窗格中選取您的工作區。
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="查詢 Log Analytics 工作區。":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>對 Log Analytics 或 Application Insights 和 Azure 資料總管原生叢集進行交叉查詢

當您執行跨叢集服務查詢時，請確認已在左窗格中選取您的 Azure 資料總管原生叢集。 下列範例示範如何搭配 [使用 union](/azure/data-explorer/kusto/query/unionoperator) 與 Log Analytics 工作區來合併 Azure 資料總管叢集資料表。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="從 Azure 資料總管的跨服務查詢。":::

使用[ `join` 運算子](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator)（而非 union）可能需要在 [`hint`](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator#join-hints) Azure 資料總管原生叢集上執行。

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>使用另一個租使用者中的 Azure 監視器資源，從一個租使用者中的 Azure 資料總管叢集中聯結資料

不支援服務之間的跨租使用者查詢。 您已登入單一租使用者，以執行橫跨這兩個資源的查詢。

如果 Azure 資料總管資源位於租使用者 ' A '，而 Log Analytics 工作區位於租使用者 ' B ' 中，請使用下列兩種方法之一：

1. Azure 資料總管可讓您為不同租使用者中的主體新增角色。 在租使用者 ' B ' 中新增您的使用者識別碼，作為 Azure 資料總管叢集上的授權使用者。 驗證 Azure 資料總管叢集中包含租使用者 ' B ' 的 *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* 屬性。 在租使用者 ' B ' 中完整執行交叉查詢。

2. 使用 [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) 將 Azure 監視器資源投影至租使用者 ' A '。
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>從不同的租使用者連接到 Azure 資料總管叢集

Kusto Explorer 會自動將您登入使用者帳戶原本所屬的租使用者。 若要使用相同的使用者帳戶存取其他租使用者中的資源，必須 `tenantId` 在連接字串中明確指定： `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>函數支援能力

Azure 資料總管跨服務查詢支援 Application Insights 和 Log Analytics 的功能。
這項功能可讓跨叢集查詢直接參考 Azure 監視器的表格式函數。
跨服務查詢支援下列命令：

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

下圖描述從 Azure 資料總管 Web UI 查詢表格式函數的範例。
若要使用函數，請在查詢視窗中執行名稱。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="從 Azure 資料總管 Web UI 查詢表格式函數。":::

## <a name="additional-syntax-examples"></a>其他語法範例

呼叫 Log Analytics 或 Application Insights 叢集時，可以使用下列語法選項：

|語法描述  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 叢集內的資料庫僅包含此訂用帳戶中已定義的資源 (**建議用於跨叢集查詢**)  |   叢集 (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`)  | 叢集 (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)      |
| 包含此訂用帳戶中所有應用程式/工作區的叢集    |     叢集 (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)     |    叢集 (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)      |
|包含訂用帳戶中所有應用程式/工作區的叢集，且為此資源群組的成員    |   叢集 (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)       |    叢集 (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)       |
|僅包含此訂用帳戶中已定義資源的叢集      |    叢集 (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)     |  叢集 (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)      |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Log Analytics 工作區和 Application Insights 的資料結構](data-platform-logs.md)。
- 瞭解如何 [在 Azure 資料總管中撰寫查詢](https://docs.microsoft.com/azure/data-explorer/write-queries)。