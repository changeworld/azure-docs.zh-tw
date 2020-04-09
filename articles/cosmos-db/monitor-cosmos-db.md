---
title: 監視 Azure 宇宙資料庫 |微軟文件
description: 瞭解如何監視 Azure Cosmos DB 的性能和可用性。
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: db9e86706ecd4e5b2526e1d801dda45ed6b345c6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887243"
---
# <a name="monitoring-azure-cosmos-db"></a>監視 Azure 宇宙資料庫
當依賴於 Azure 資源的關鍵應用程式和業務流程時,需要監視這些資源的可用性、性能和操作。 本文介紹了 Azure Cosmos 資料庫生成的監視數據,以及如何使用 Azure 監視器的功能來分析和警報此數據。

## <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？
Azure Cosmos DB 使用[Azure 監視器](../azure-monitor/overview.md)創建監視數據,Azure 監視器是 Azure 中的完整堆疊監視服務,除了其他雲端和本地的資源外,還提供一整套功能來監視 Azure 資源。 

如果您還不熟悉監視 Azure 服務,則從[使用 Azure 監視器監視 Azure 資源](../azure-monitor/insights/monitor-azure-resource.md)一文開始,該文章描述了以下內容:

- Azure 監視器是什麼？
- 與監控相關的成本
- 監視在 Azure 中收集的資料
- 設定資料收集
- Azure 中用於分析和警示監控資料的標準工具

以下各節通過描述從 Azure Cosmos DB 收集的數據,並提供用於使用 Azure 工具配置數據收集和分析此數據的範例,從而建構本文。

## <a name="azure-monitor-for-cosmos-db-preview"></a>用於宇宙資料庫的 Azure 監視器(預覽)
[Azure Cosmos DB 的 Azure 監視器](../azure-monitor/insights/cosmosdb-insights-overview.md)基於 Azure[監視器的工作簿功能](../azure-monitor/app/usage-workbooks.md),並使用以下各節中所述為 Cosmos DB 收集的相同監視數據。 使用此工具查看統一互動式體驗中所有 Azure Cosmos DB 資源的總體性能、故障、容量和操作運行狀況,並利用 Azure 監視器的其他功能進行詳細分析和警報。 

![宇宙 DB 的 Azure 監視器](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>檢視 Azure 宇宙 DB 的操作層級指標

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從左側導航欄中選擇 **「監視器**」,然後選擇 **「指標**」。。

   ![Azure 監視器中的指標窗格](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. 從 **「指標」** 窗格>**選擇資源**>选择所需的**訂閱**與資源**群組**。 對於**資源類型**,選擇**Azure 宇宙資料庫帳戶**,選擇現有 Azure Cosmos 帳戶之一,然後選擇 **"應用**"。

   ![選擇 Cosmos DB 帳號以檢視指標](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. 接下來,您可以從可用指標清單中選擇指標。 您可以選擇特定於請求單位、儲存、延遲、可用性、Cassandra 等的指標。 要詳細瞭解此清單中的所有可用指標,請參閱[按類別的指標](monitor-cosmos-db-reference.md)。 在此範例中,讓我們選擇**請求單位**,**選擇 avg**作為聚合值。

   除了這些詳細資訊之外,您還可以選擇指標**的時間範圍**和**時間粒度**。 最多時,您可以查看過去 30 天的指標。  應用篩選器後,會根據您的篩選器顯示圖表。 您可以看到所選期間每分鐘消耗的請求單位的平均數量。  

   ![從 Azure 門戶選擇指標](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>將篩選器加入到指標

您還可以篩選指標和由特定**集合名稱**、**資料庫名稱**、**操作類型**、**區域**和**狀態碼顯示的**圖表。 要篩選指標,請選擇 **「添加篩選器」** 並選擇所需的屬性(如**操作類型**)並**選擇查詢等**值。 然後,該圖顯示所選期間的查詢操作使用的請求單位。 不會記錄通過存儲過程執行的操作,因此在操作類型指標下它們不可用。

![新增篩選器以選擇指標粒度](./media/monitor-cosmos-db/add-metrics-filter.png)

您可以使用 **「應用分割**」選項對指標進行分組。 例如,您可以按工序類型對請求單位進行分組,並一次查看所有操作的圖形,如下圖所示:

![新增應用程式分割篩選器](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>監視從 Azure 宇宙資料庫收集的資料

Azure Cosmos DB 收集與其他 Azure 資源相同的監視數據,這在監視[Azure 資源中描述](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)的數據。 有關 Azure Cosmos DB 建立的紀錄和指標的詳細資訊,請參閱[Azure Cosmos DB 監視資料參考](monitor-cosmos-db-reference.md)。

每個 Azure Cosmos 資料庫的 Azure 門戶中的 **「概述」** 頁包括資料庫使用方式的簡要視圖,包括其請求和每小時計費使用方式。 這是有用的資訊,但可用的監視數據只有少量。 其中一些數據會自動收集,並在創建資料庫后立即可供分析,同時可以使用某些配置啟用其他數據收集。

![概觀分頁](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>分析指標資料

Azure Cosmos DB 提供了用於處理指標的自定義體驗。 有關使用此體驗和分析不同的 Azure Cosmos DB 方案的詳細資訊,請參閱[Azure 監視器中的監視和除錯 Azure 宇宙資料庫指標](cosmos-db-azure-monitor-metrics.md)。

您可以使用指標資源管理員使用來自其他 Azure 服務的指標分析 Azure Cosmos DB 的指標,從 Azure**監視器**選單中開啟**指標**。 有關使用此工具的詳細資訊[,請參閱使用 Azure 指標資源管理器入門](../azure-monitor/platform/metrics-getting-started.md)。 Azure Cosmos DB 的所有指標都在命名空間**Cosmos DB 標準指標中**。 將篩選器新增到圖表時,可以將以下維度與以下指標一起使用:

- CollectionName
- DatabaseName
- OperationType
- 區域
- StatusCode


## <a name="analyzing-log-data"></a>剖析紀錄資料
Azure 監視器日誌中的數據存儲在每個表具有其自己的唯一屬性集的表中。 Azure Cosmos DB 將數據存儲在下表中。

| Table | 描述 |
|:---|:---|
| AzureDiagnostics | 多個服務用於存儲資源日誌的通用表。 Azure Cosmos DB 的資源日`MICROSOFT.DOCUMENTDB`誌可以使用 標識。   |
| AzureActivity    | 存儲活動日誌中所有記錄的常見表。 


> [!IMPORTANT]
> 從 Azure Cosmos DB 選單中選擇 **「日誌」** 時,將打開日誌分析,查詢範圍設置為當前 Azure Cosmos 資料庫。 這意味著日誌查詢將僅包括來自該資源的數據。 如果要運行包含其他資料庫的數據或其他 Azure 服務的數據的查詢,請選擇**Azure 監視器**功能表中的 **「日誌**」。 有關詳細資訊,請參閱[Azure 監視器紀錄分析中的紀錄查詢範圍和時間範圍](../azure-monitor/log-query/scope.md)。

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure 宇宙 DB 紀錄分析查詢在 Azure 監視器中

下面是一些查詢,您可以在**日誌搜索**欄中輸入這些查詢,以説明您監視 Azure Cosmos 容器。 這些查詢使用[新語言](../log-analytics/log-analytics-log-search-upgrade.md)。

以下是可用於幫助監視 Azure Cosmos 資料庫的查詢。

* 若要查詢 Azure Cosmos DB 中在指定的時段內的所有診斷記錄：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* 若要查詢 10 個最近記錄的事件：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* 若要查詢所有作業 (依作業類型分組)：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* 要查詢所有操作,請按資源群組:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* 若要查詢所有使用者活動 (依資源分組)：

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* 將所有超過 100 個 R 的查詢與**DataPlane 請求**和**查詢 RunTime 統計資訊**中的數據聯接。

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* 若要查詢哪些作業費時超過 3 毫秒：

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* 若要查詢哪些代理程式正在執行此作業：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* 若要查詢長時間執行的作業於何時執行：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* 要取得分區金鑰統計資訊以評估資料庫帳戶的前 3 個分區之間的偏差,請進行以下計算:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>以程式設計方式監視 Azure Cosmos DB
可在入口網站中取得的帳戶層級計量 (例如，帳戶儲存體使用量和要求總數) 無法透過 SQL API 取得。 不過，您可以使用 SQL API 來擷取集合層級的使用量資料。 若要擷取集合層級的資料，請執行下列動作：

* 若要使用 REST API，請 [在集合上執行 GET](https://msdn.microsoft.com/library/mt489073.aspx)。 集合的配額和使用量資訊會在回應的 x-ms-resource-quota 和 x-ms-resource-usage 標頭中傳回。
* 若要使用 .NET SDK，請使用 [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) 方法，此方法會傳回包含 **CollectionSizeUsage**、**DatabaseUsage**、**DocumentUsage** 等幾個使用量屬性的 [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx)。

若要存取其他度量，請使用 [Azure 監視器 SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)。 您可以呼叫下列程式碼來擷取可用的度量定義：

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

擷取個別度量的查詢會使用下列格式：

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>後續步驟

- 有關 Azure Cosmos DB 建立的紀錄和指標的參考,請參閱[Azure Cosmos DB 監視資料參考](monitor-cosmos-db-reference.md)。
- 有關監視 Azure 資源的詳細資訊,請參閱[使用 Azure 監視器監視 Azure 資源](../azure-monitor/insights/monitor-azure-resource.md)。
