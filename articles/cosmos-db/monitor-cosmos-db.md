---
title: 監視 Azure Cosmos DB |Microsoft Docs
description: 瞭解如何監視 Azure Cosmos DB 的效能和可用性。
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: ec0894818c0c246223749e1efcf7ea9e5ebee463
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194528"
---
# <a name="monitoring-azure-cosmos-db"></a>監視 Azure Cosmos DB
當您的重要應用程式和商務程式依賴 Azure 資源時，您會想要監視這些資源的可用性、效能和操作。 本文說明 Azure Cosmos 資料庫所產生的監視資料，以及您可以如何使用 Azure 監視器的功能來分析此資料併發出警示。

## <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？
Azure Cosmos DB 會使用[Azure 監視器](../azure-monitor/overview.md)建立監視資料，這是 Azure 中的完整堆疊監視服務，除了其他雲端和內部部署的資源之外，還提供一組完整的功能來監視您的 Azure 資源。 

如果您還不熟悉監視 Azure 服務，請從[使用 Azure 監視器來監視 azure 資源](../azure-monitor/insights/monitor-azure-resource.md)一文開始，其描述如下：

- Azure 監視器是什麼？
- 與監視相關聯的成本
- 監視 Azure 中所收集的資料
- 設定資料收集
- Azure 中用來分析和警示監視資料的標準工具

下列各節將描述從 Azure Cosmos DB 收集的特定資料，並提供使用 Azure 工具來設定資料收集和分析此資料的範例，以建立于本文中。

## <a name="azure-monitor-for-cosmos-db-preview"></a>Cosmos DB 的 Azure 監視器（預覽）
[Azure Cosmos DB 的 Azure 監視器是以](../azure-monitor/insights/cosmosdb-insights-overview.md)Azure 監視器的活頁[簿功能](../azure-monitor/app/usage-workbooks.md)為基礎，並使用針對下列各節所述的 Cosmos DB 所收集的相同監視資料。 使用此工具來查看所有 Azure Cosmos DB 資源的整體效能、失敗、容量和操作健全狀況，並以統一的互動體驗來進行，並利用 Azure 監視器的其他功能來進行詳細的分析和警示。 

![Cosmos DB 的 Azure 監視器](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

> [!NOTE]
> 建立容器時，請確定您不會建立兩個名稱相同但大小寫不同的容器。 這是因為 Azure 平臺的某些部分不會區分大小寫，而這可能導致在具有這類名稱的容器上進行遙測和動作的混淆/衝突。

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>查看 Azure Cosmos DB 的作業層級計量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從左側導覽列選取 [**監視**]，然後選取 [**計量**]。

   ![Azure 監視器中的 [計量] 窗格](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. 從 [**計量**] 窗格 >**選取資源**> 選擇所需的**訂**用帳戶和**資源群組**。 針對 [**資源類型**]，選取 [ **Azure Cosmos DB 帳戶**] **，選擇其中**一個現有的 Azure Cosmos 帳戶，然後選取 [套用]。

   ![選擇 Cosmos DB 帳戶來查看計量](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. 接下來，您可以從可用的計量清單中選取度量。 您可以選取 [要求單位]、[儲存體]、[延遲]、[可用性]、[Cassandra] 和其他的特定計量。 若要深入瞭解此清單中所有可用的計量，請參閱[依類別](monitor-cosmos-db-reference.md)區分的計量一文。 在此範例中，讓我們選取 [**要求單位**] 和 [**平均**] 作為匯總值。

   除了這些詳細資料之外，您也可以選取度量的**時間範圍**和**時間細微性**。 在 [最大值] 中，您可以查看過去30天的計量。  套用篩選之後，就會根據您的篩選器來顯示圖表。 您可以看到所選期間內每分鐘耗用的平均要求單位數。  

   ![從 Azure 入口網站選擇度量](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>將篩選新增至計量

您也可以篩選特定**CollectionName**、 **DatabaseName**、 **OperationType**、 **Region**和**StatusCode**所顯示的計量和圖表。 若要篩選計量，請選取 [**新增篩選**] 並選擇必要的屬性（例如**OperationType** ），然後選取 [**查詢**] 之類的值。 圖形接著會顯示所選期間內，查詢作業所耗用的要求單位。 不會記錄透過預存程式執行的作業，因此它們無法在 OperationType 度量下提供。

![新增篩選以選取度量資料細微性](./media/monitor-cosmos-db/add-metrics-filter.png)

您可以使用 [套用**分割**] 選項將計量分組。 例如，您可以將每個作業類型的 [要求單位] 分組，並一次查看所有作業的圖形，如下圖所示：

![新增套用分割篩選](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>監視從 Azure Cosmos DB 收集的資料

Azure Cosmos DB 會收集與其他 Azure 資源相同的監視資料類型，如[從 Azure 資源監視資料](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)中所述。 如需 Azure Cosmos DB 所建立之記錄和計量的詳細參考，請參閱[Azure Cosmos DB 監視資料參考](monitor-cosmos-db-reference.md)。

每個 Azure Cosmos 資料庫的 Azure 入口網站中的 [**總覽**] 頁面包含資料庫使用量的簡要觀點，包括其要求和每小時計費使用量。 這是有用的資訊，但只提供少量的監視資料。 當您建立資料庫時，會自動收集這項資料並可供分析，而您可以使用一些設定來啟用其他資料收集。

![概觀分頁](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>分析度量資料

Azure Cosmos DB 提供使用計量的自訂體驗。 如需使用此體驗和分析不同 Azure Cosmos DB 案例的詳細資訊，請參閱[從 Azure 監視器監視和偵錯工具 Azure Cosmos DB 計量](cosmos-db-azure-monitor-metrics.md)。

您可以從 [ **Azure 監視器**] 功能表開啟 [**計量**]，以使用計量瀏覽器從其他 Azure 服務中的計量來分析 Azure Cosmos DB 的計量。 如需使用此工具的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](../azure-monitor/platform/metrics-getting-started.md)。 Azure Cosmos DB 的所有計量都在命名空間**Cosmos DB 標準計量**中。 將篩選加入至圖表時，您可以使用下列維度搭配這些計量：

- CollectionName
- DatabaseName
- OperationType
- 區域
- StatusCode


## <a name="analyzing-log-data"></a>分析記錄資料
Azure 監視器記錄檔中的資料會儲存在資料表中，每個資料表都有一組專屬的唯一屬性。 Azure Cosmos DB 會將資料儲存在下列資料表中。

| Table | 描述 |
|:---|:---|
| AzureDiagnostics | 多個服務用來儲存資源記錄的通用資料表。 可以使用`MICROSOFT.DOCUMENTDB`來識別 Azure Cosmos DB 中的資源記錄。   |
| AzureActivity    | 儲存活動記錄中所有記錄的通用資料表。 


> [!IMPORTANT]
> 當您從 [Azure Cosmos DB] 功能表中選取 [**記錄**] 時，會開啟 Log Analytics，並將查詢範圍設定為目前的 Azure Cosmos 資料庫。 這表示記錄查詢只會包含來自該資源的資料。 如果您想要執行的查詢包含來自其他資料庫的資料或來自其他 Azure 服務的資料，請從 [ **Azure 監視器**] 功能表中選取 [**記錄**]。 如需詳細資訊，請參閱[Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍](../azure-monitor/log-query/scope.md)。

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Azure 監視器中的 Log Analytics 查詢

以下是一些您可以在**記錄搜尋**搜尋列中輸入的查詢，以協助您監視 Azure Cosmos 容器。 這些查詢使用[新語言](../log-analytics/log-analytics-log-search-upgrade.md)。

以下是您可以用來協助您監視 Azure Cosmos 資料庫的查詢。

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

* 若要查詢所有作業（依資源分組）：

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
* 取得大於 100 ru 的所有查詢，並與**DataPlaneRequests**和**QueryRunTimeStatistics**中的資料聯結。

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
    
* 若要取得分割區索引鍵統計資料，以評估資料庫帳戶的前3個磁碟分割的扭曲：

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

- 如需 Azure Cosmos DB 所建立之記錄和計量的參考，請參閱[Azure Cosmos DB 監視資料參考](monitor-cosmos-db-reference.md)。
- 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器監視 azure 資源](../azure-monitor/insights/monitor-azure-resource.md)。
