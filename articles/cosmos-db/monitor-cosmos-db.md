---
title: 監視 Azure Cosmos DB |Microsoft Docs
description: 了解如何監視 Azure Cosmos DB 的效能和可用性。
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 5056762dab18ae23980c7d3b3ebfbb3c3014fa56
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798704"
---
# <a name="monitoring-azure-cosmos-db"></a>監視 Azure Cosmos DB

當您有依賴 Azure 資源的重要應用程式和商務程序時，您會想要監視這些資源的可用性、效能和操作。 本文說明 Azure Cosmos 資料庫所產生的監視資料，以如何使用 Azure 監視器的功能來分析此資料並發出警示。

## <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？
Azure Cosmos DB 會使用 [Azure 監視器](../azure-monitor/overview.md)建立監視資料，Azure 監視器是 Azure 中的完整堆疊監視服務，其提供了一組完整功能以供您監視 Azure 資源，以及其他雲端和內部部署環境中的資源。

如果您還不熟悉如何監視 Azure 服務，請從＜[使用 Azure 監視器來監視 Azure 資源](../azure-monitor/insights/monitor-azure-resource.md)＞一文開始著手，其中會說明下列各項：

- Azure 監視器是什麼？
- 與監視相關聯的成本
- 在 Azure 中收集的監視資料
- 設定資料收集
- Azure 中用來分析和警示監視資料的標準工具

下列各節將以此文章為基礎來描述從 Azure Cosmos DB 收集的特定資料，並提供如何使用 Azure 工具來設定資料收集和分析此資料的範例。

## <a name="azure-monitor-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 Azure 監視器

Azure Cosmos DB 的 Azure 監視器會以 [Azure 監視器的活頁簿功能](../azure-monitor/platform/workbooks-overview.md)為基礎，並使用針對 Cosmos DB 所收集的相同監視資料，如下列各節所述。 Azure 監視器可供您透過統一的互動式體驗，檢視所有 Azure Cosmos DB 資源的整體效能、失敗、容量及作業健康狀態，並利用 Azure 監視器的其他功能來進行詳細的分析和警示。 若要深入了解，請參閱[探索適用於 Azure Cosmos DB 的 Azure 監視器](../azure-monitor/insights/cosmosdb-insights-overview.md)一文。

> [!NOTE]
> 建立容器時，請確定您不會建立兩個名稱相同但大小寫不同的容器。 這是因為 Azure 平台的某些部分不會區分大小寫，而這可能導致在具有這類名稱的容器上發生遙測和動作的混淆/衝突。

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>檢視 Azure Cosmos DB 的作業層級計量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從左側導覽列選取 [監視器]，然後選取 [計量]。

   ![Azure 監視器中的 [計量] 窗格](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. 從 [計量] 窗格 > **選取資源** > 選擇必要的**訂用帳戶**和**資源群組**。 在 [資源類型] 中，選取 [Azure Cosmos DB 帳戶]，然後選擇其中一個現有的 Azure Cosmos 帳戶並選取 [套用]。

   ![選擇 Cosmos DB 帳戶來檢視計量](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. 接下來，您可以從可用的計量清單中選取計量。 您可以選取 [要求單位]、[儲存體]、[延遲]、[可用性]、[Cassandra] 等等的專屬計量。 若要深入了解此清單中所有可用的計量，請參閱[依類別區分的計量](monitor-cosmos-db-reference.md)一文。 在此範例中，我們選取 [要求單位] 和 [平均] 來作為彙總值。

   除了這些詳細資料之外，您也可以選取計量的 [時間範圍] 和 [時間細微性]。 在 [最大值] 中，您可以檢視過去 30 天的計量。  套用篩選之後，圖表就會根據您的篩選條件來顯示。 您可以看到所選期間內每分鐘耗用的平均要求單位數。  

   ![從 Azure 入口網站選擇計量](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>將篩選條件新增至計量

您也可以篩選計量，讓圖表依據特定的 **CollectionName**、**DatabaseName**、**OperationType**, **Region** 和 **StatusCode** 來顯示。 若要篩選計量，請選取 [新增篩選條件]，然後選擇必要的屬性 (例如 **OperationType**)，然後選取 [查詢] 之類的值。 圖形接著會顯示所選期間內，查詢作業所耗用的要求單位。 透過預存程序執行的作業並不會記錄，因此其無法在 OperationType 計量下提供。

![新增篩選條件以選取計量細微性](./media/monitor-cosmos-db/add-metrics-filter.png)

您可以使用 [套用分割] 選項來將計量分組。 例如，您可以將每個作業類型的要求單位分組，並一次檢視所有作業的圖形，如下圖所示：

![新增套用分割篩選條件](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>監視從 Azure Cosmos DB 收集的資料

Azure Cosmos DB 會收集與其他 Azure 資源相同的監視資料類型，如[監視 Azure 資源中的資料](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)所說明。 如需 Azure Cosmos DB 所建立之記錄和計量的詳細參考，請參閱 [Azure Cosmos DB 監視資料參考](monitor-cosmos-db-reference.md)。

在 Azure 入口網站中，每個 Azure Cosmos 資料庫的 [概觀] 頁面都會包含資料庫使用量的簡要觀點，包括其要求和每小時計費的使用量。 此資訊很實用，但只有少量的監視資料可供使用。 當您建立資料庫時，部分這類資料會自動收集並可供分析，而您可以使用一些設定來啟用其他資料收集。

![概觀分頁](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>分析計量資料

Azure Cosmos DB 提供使用計量的自訂體驗。 如需使用此體驗和分析不同 Azure Cosmos DB 案例的詳細資訊，請參閱從 [Azure 監視器來監視和偵錯 Azure Cosmos DB 計量](cosmos-db-azure-monitor-metrics.md)。

您可以從 [Azure 監視器] 功能表開啟 [計量]，以透過計量瀏覽器使用其他 Azure 服務中的計量來分析 Azure Cosmos DB 的計量。 如需使用此工具的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](../azure-monitor/platform/metrics-getting-started.md)。 Azure Cosmos DB 的所有計量都在 **Cosmos DB 標準計量**命名空間中。 將篩選條件新增至圖表時，您可以使用下列維度來搭配這些計量：

- CollectionName
- DatabaseName
- OperationType
- 區域
- StatusCode

## <a name="analyzing-log-data"></a>分析記錄資料
Azure 監視器記錄中的資料會儲存在資料表中，其中每個資料表都有一組專屬的唯一屬性。 Azure Cosmos DB 會將資料儲存在下列資料表中。

| Table | 描述 |
|:---|:---|
| AzureDiagnostics | 多個服務用來儲存資源記錄的通用資料表。 可以使用 `MICROSOFT.DOCUMENTDB` 來識別 Azure Cosmos DB 中的資源記錄。   |
| AzureActivity    | 儲存活動記錄中所有記錄的通用資料表。 


> [!IMPORTANT]
> 當您從 [Azure Cosmos DB] 功能表中選取 [記錄] 時，查詢範圍設定為目前 Azure Cosmos 資料庫的 Log Analytics 會隨即開啟。 這表示記錄查詢只會包含來自該資源的資料。 如果您想要執行包含其他資料庫資料或其他 Azure 服務資料的查詢，請從 [Azure 監視器] 功能表中選取 [記錄]。 如需詳細資訊，請參閱 [Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍](../azure-monitor/log-query/scope.md)。

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure 監視器中的 Azure Cosmos DB Log Analytics 查詢

以下是一些您可以在 [記錄搜尋] 搜尋列中輸入以利監視 Azure Cosmos 容器的查詢。 這些查詢使用[新語言](../log-analytics/log-analytics-log-search-upgrade.md)。

以下是可用來協助您監視 Azure Cosmos 資料庫的查詢。

* 若要查詢 Azure Cosmos DB 中在指定的時段內的所有診斷記錄：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* 若要查詢所有作業 (依資源分組)：

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* 若要查詢所有使用者活動 (依資源分組)：

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
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

- 如需 Azure Cosmos DB 所建立之記錄和計量的參考，請參閱 [Azure Cosmos DB 監視資料參考](monitor-cosmos-db-reference.md)。
- 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器來監視 Azure 資源](../azure-monitor/insights/monitor-azure-resource.md)。
