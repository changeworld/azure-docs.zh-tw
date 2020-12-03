---
title: 使用 Azure 監視器跨資源查詢 Azure 資料總管
description: 使用 Azure 監視器在 Azure 資料總管、Log Analytics 工作區與 Azure 監視器中的傳統 Application Insights 應用程式之間執行交叉乘積查詢。
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 8884fd56cfc1e18a0a358d6902f7f8bcbfc1f5d3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552001"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>使用 Azure 監視器跨資源查詢 Azure 資料總管
Azure 監視器支援 Azure 資料總管、 [Application Insights (AI) ](/azure/azure-monitor/app/app-insights-overview)和 [LOG Analytics (LA) ](/azure/azure-monitor/platform/data-platform-logs)之間的跨服務查詢。 然後，您可以使用 Log Analytics/Application Insights 工具來查詢 Azure 資料總管叢集，並在跨服務查詢中加以參考。 本文說明如何進行跨服務查詢。

Azure 監視器的跨服務流程： :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="azure 監視器和 azure 資料總管跨服務流程。":::

>[!NOTE]
>* Azure 監視器 proxy 處於私人預覽狀態-必須有 AllowListing。
>* 如有任何問題，請洽詢 [服務小組](mailto:ADXProxy@microsoft.com) 。
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>跨查詢 Log Analytics 或 Application Insights 資源和 Azure 資料總管

您可以使用支援 Kusto 查詢的用戶端工具來執行跨資源查詢，例如： Log Analytics web UI、活頁簿、PowerShell REST API 等等。

* 在 ' adx ' 模式中的查詢中輸入 Azure 資料總管叢集的識別碼，並在後面加上資料庫名稱和資料表。

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="跨服務查詢範例。":::

> [!NOTE]
>* 資料庫名稱會區分大小寫。
>* 不支援將跨資源查詢作為警示。
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>結合 Azure 資料總管叢集資料表 (使用聯集，並加入) 的 LA 工作區。

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="使用 union 的跨服務查詢範例。":::

>[!Tip]
>* 允許使用速記格式-ClusterName/InitialCatalog。 例如 adx ( ' help/Samples ' ) 會轉譯為 adx ( ' help. kusto. net/Samples ' ) 
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>使用另一個租使用者中的 Azure 監視器資源，從一個租使用者中的 Azure 資料總管叢集中聯結資料

不支援服務之間的跨租使用者查詢。 您已登入單一租使用者，以執行橫跨這兩個資源的查詢。

如果 Azure 資料總管資源位於租使用者 ' A '，而 Log Analytics 工作區位於租使用者 ' B ' 中，請使用下列兩種方法之一：

*  Azure 資料總管可讓您為不同租使用者中的主體新增角色。 在租使用者 ' B ' 中新增您的使用者識別碼，作為 Azure 資料總管叢集上的授權使用者。 驗證 Azure 資料總管叢集中包含租使用者 ' B ' 的 *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* 屬性。 在租使用者 ' B ' 中完整執行交叉查詢。
*  使用 [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) 將 Azure 監視器資源投影至租使用者 ' A '。
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>從不同的租使用者連接到 Azure 資料總管叢集

Kusto Explorer 會自動將您登入使用者帳戶原本所屬的租使用者。 若要使用相同的使用者帳戶存取其他租使用者中的資源，必須 `tenantId` 在連接字串中明確指定： `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="next-steps"></a>後續步驟
* [撰寫查詢](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [使用 Azure 資料總管在 Azure 監視器中查詢資料](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [在 Azure 監視器中執行跨資源記錄查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)






