---
title: 監視 Azure 佇列儲存體 |Microsoft Docs
description: 瞭解如何監視 Azure 佇列儲存體的效能和可用性。 監視 Azure 佇列儲存體資料、瞭解設定，以及分析計量和記錄資料。
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 3fe99543b821810b1479f1e504098d81fd20c534
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711196"
---
# <a name="monitoring-azure-queue-storage"></a>監視 Azure 佇列儲存體

當您有依賴 Azure 資源的重要應用程式和商務程序時，您會想要監視這些資源的可用性、效能和操作。 本文說明 Azure 佇列儲存體所產生的監視資料，以及如何使用 Azure 監視器的功能來分析此資料的警示。

> [!NOTE]
> Azure 監視器中的 Azure 儲存體記錄處於公開預覽狀態，可在所有公用雲端區域中進行預覽測試。 若要註冊預覽，請參閱[本頁](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。 此預覽可讓 blob (記錄，其中包含 Azure Data Lake Storage Gen2) 、檔案、佇列和資料表。 這項功能適用于使用 Azure Resource Manager 部署模型建立的所有儲存體帳戶。 請參閱 [儲存體帳戶總覽](../common/storage-account-overview.md)。

## <a name="monitor-overview"></a>監視概觀

每個佇列儲存體資源的 Azure 入口網站中的 **總覽** 頁面包含資源使用量的簡短觀點，例如要求和每小時計費。 此資訊很實用，但只有少量的監視資料可供使用。 這部分資料會自動收集，並在您建立資源時立即可供分析。 您可使用一些設定來啟用其他類型的資料收集。

## <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？
Azure 佇列儲存體使用 [Azure 監視器](../../azure-monitor/overview.md)來建立監視資料，這是 Azure 中的完整堆疊監視服務。 Azure 監視器提供一組完整的功能，可監視您的 Azure 資源以及其他雲端和內部部署環境中的資源。 

請從使用下列內容來 [監視 Azure 資源](../../azure-monitor/insights/monitor-azure-resource.md) 的文章開始 Azure 監視器說明下列各項：

- Azure 監視器是什麼？
- 與監視相關聯的成本
- 在 Azure 中收集的監視資料
- 設定資料收集
- Azure 中用來分析和警示監視資料的標準工具

下列各節以本文為基礎，描述從 Azure 儲存體收集的特定資料。 其中的範例示範如何使用 Azure 工具來設定資料收集及分析此資料。

## <a name="monitoring-data"></a>監視資料

Azure 佇列儲存體會收集與其他 Azure 資源相同的監視資料類型，這些資源會在 [從 Azure 資源監視資料](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)中說明。 

如需 Azure 佇列儲存體所建立的計量和記錄計量的詳細資訊，請參閱 [Azure 佇列儲存體監視資料參考](monitor-queue-storage-reference.md) 。

Azure 監視器中的計量和記錄只支援 Azure Resource Manager 儲存體帳戶。 Azure 監視器不支援傳統儲存體帳戶。 如果您想在傳統儲存體帳戶上使用計量或記錄，則必須遷移至 Azure Resource Manager 儲存體帳戶。 請參閱[遷移至 Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)。

如果想要，您可繼續使用傳統計量和記錄。 事實上，傳統計量和記錄可與 Azure 監視器中的計量和記錄平行提供。 在 Azure 儲存體結束舊版計量和記錄的服務之前，支援維持不變。

## <a name="collection-and-routing"></a>收集和路由

系統會自動收集平臺計量和活動記錄，但是可以使用診斷設定將其路由至其他位置。 您必須建立診斷設定以收集資源記錄。 

若要使用 Azure 入口網站、Azure CLI 或 PowerShell 來建立診斷設定，請參閱 [建立診斷設定以收集 Azure 中的平臺記錄和計量](../../azure-monitor/platform/diagnostic-settings.md)。 

若要查看建立診斷設定的 Azure Resource Manager 範本，請參閱 [Azure 儲存體的診斷設定](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage)。

當您建立診斷設定時，請選擇您要啟用記錄的儲存體類型，例如 Blob、佇列、資料表或檔案。 針對佇列儲存體，請選擇 [ **佇列**]。 

如果您在 Azure 入口網站中建立診斷設定，可以從清單中選取資源。 如果您使用 PowerShell 或 Azure CLI，則需要使用佇列儲存體端點的資源識別碼。 在 Azure 入口網站中，開啟您儲存體帳戶的 [屬性] 頁面，即可尋找資源識別碼。

您也必須指定下列其中一個要收集記錄的作業類別目錄。 

| 類別 | 描述 |
|:---|:---|
| StorageRead | 物件的讀取作業。 |
| StorageWrite | 對物件進行寫入作業。 |
| StorageDelete | 刪除物件上的作業。 |

## <a name="analyzing-metrics"></a>分析計量

您可使用計量瀏覽器，利用其他 Azure 服務的計量來分析 Azure 儲存體的計量。 從 [Azure 監視器] 功能表中選擇 [計量]，以開啟計量瀏覽器。 如需使用此工具的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)。 

此範例說明如何檢視帳戶層級的**交易**。

![在 Azure 入口網站中存取計量的螢幕擷取畫面](./media/monitor-queue-storage/access-metrics-portal.png)

針對支援維度的計量，您可使用所需的維度值來篩選計量。 此範例說明如何藉由選取 [API 名稱] 維度的值，檢視特定作業在帳戶層級的 [交易]。

![在 Azure 入口網站中存取含維度之計量的螢幕擷取畫面](./media/monitor-queue-storage/access-metrics-portal-with-dimension.png)

如需 Azure 儲存體支援的完整維度清單，請參閱[計量維度](monitor-queue-storage-reference.md#metrics-dimensions)。

Azure 佇列儲存體的計量位於下列命名空間： 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/queueServices

如需所有 Azure 監視器支援計量的清單，包括 Azure 佇列儲存體，請參閱 [Azure 監視器支援的度量](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)。


### <a name="accessing-metrics"></a>存取計量

> [!TIP]
> 若要檢視 Azure CLI 或 .NET 範例，請選擇此處所列的對應索引標籤。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>列出計量定義

您可以列出儲存體帳戶或佇列儲存體服務的度量定義。 使用 [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition) Cmdlet。

在此範例中，請將 `<resource-ID>` 預留位置取代為整個儲存體帳戶的資源識別碼或佇列儲存體服務的資源識別碼。  您可在 Azure 入口網站中儲存體帳戶的 [屬性] 頁面上找到這些資源識別碼。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>讀取度量值

您可以讀取儲存體帳戶或佇列儲存體服務的帳戶層級度量值。 使用 [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric) Cmdlet。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>列出帳戶層級的計量定義

您可以列出儲存體帳戶或佇列儲存體服務的度量定義。 使用 [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) 命令。
 
在此範例中，請將 `<resource-ID>` 預留位置取代為整個儲存體帳戶的資源識別碼或佇列儲存體服務的資源識別碼。 您可在 Azure 入口網站中儲存體帳戶的 [屬性] 頁面上找到這些資源識別碼。

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>讀取帳戶層級的計量值

您可以讀取儲存體帳戶或佇列儲存體服務的度量值。 使用 [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list) 命令。

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure 監視器提供 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) 來讀取計量定義和值。 [範例程式碼](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)示範如何使用 SDK 搭配不同的參數。 您必須使用 `0.18.0-preview` 或更新版本的儲存體計量。
 
在這些範例中，請將 `<resource-ID>` 預留位置取代為整個儲存體帳戶或佇列儲存體服務的資源識別碼。 您可在 Azure 入口網站中儲存體帳戶的 [屬性] 頁面上找到這些資源識別碼。

將 `<subscription-ID>` 變數取代為您的訂用帳戶識別碼。 如需有關如何取得 `<tenant-ID>`、`<application-ID>` 和 `<AccessKey>`值的指引，請參閱[使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/)。 

#### <a name="list-the-account-level-metric-definition"></a>列出帳戶層級的計量定義

下列範例說明如何列出帳戶層級的計量定義：

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>讀取帳戶層級的度量值

下列範例說明如何讀取帳戶層級的 `UsedCapacity` 資料：

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>讀取多維度度量值

對於多維度的計量，如果您想要讀取特定維度值的計量資料，就必須定義中繼資料篩選條件。

下列範例示範如何讀取支援多維度計量的計量資料：

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for queue storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

---

## <a name="analyzing-logs"></a>分析記錄

您可以透過儲存體帳戶中的佇列、事件資料，或透過記錄分析查詢來存取資源記錄。

如需這些記錄檔中出現之欄位的詳細參考，請參閱 [Azure 佇列儲存體監視資料參考](monitor-queue-storage-reference.md)。

> [!NOTE]
> Azure 監視器中的 Azure 儲存體記錄處於公開預覽狀態，可在所有公用雲端區域中進行預覽測試。 若要註冊預覽，請參閱[本頁](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。 此預覽可啟用 Blob (包括 Azure Data Lake Storage Gen2)、檔案、佇列、資料表、一般用途 v1 高階儲存體帳戶及一般用途 v2 儲存體帳戶的記錄。 不支援傳統儲存體帳戶。

只有在對服務端點提出要求時，才會建立記錄項目。 例如，如果儲存體帳戶在其佇列端點中有活動，而不是在其資料表或 blob 端點中，則只會建立與佇列服務相關的記錄。 Azure 儲存體記錄包含對儲存體服務之成功和失敗要求的詳細資訊。 這項資訊可用來監視個別要求，並診斷儲存體服務的問題。 系統會以最佳方式來記錄要求。

### <a name="log-authenticated-requests"></a>記錄已驗證的要求

 系統將記錄下列類型的驗證要求：

- 成功的要求
- 失敗的要求，包括逾時、節流、網路、授權和其他錯誤
- 使用共用存取簽章 (SAS) 或 OAuth 的要求，包括失敗和成功的要求
- 對分析資料 ( **$logs** 中的傳統記錄資料和 **$metric** 資料表中的類別計量資料) 的要求

佇列儲存體服務本身所提出的要求（例如記錄建立或刪除）不會記錄下來。 如需已記錄資料的完整清單，請參閱[儲存體記錄的作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[儲存體記錄格式](monitor-queue-storage-reference.md)。

### <a name="log-anonymous-requests"></a>記錄匿名要求

 系統將記錄下列類型的匿名要求：

- 成功的要求
- 伺服器錯誤
- 用戶端與伺服器的逾時錯誤
- 失敗的 GET 要求，其錯誤碼為 304 (未修改)

系統不會記錄所有其他失敗的匿名要求。 如需已記錄資料的完整清單，請參閱[儲存體記錄的作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[儲存體記錄格式](monitor-queue-storage-reference.md)。

### <a name="accessing-logs-in-a-storage-account"></a>存取儲存體帳戶中的記錄

記錄會顯示為儲存在目標儲存體帳戶中容器的 Blob。 系統會以行分隔的 JSON 承載形式將資料收集並儲存在單一 Blob 內。 此 Blob 的名稱會遵循以下命名慣例：

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/queueServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

以下是範例：

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>存取事件中樞內的記錄

傳送至事件中樞的記錄不會儲存為檔案，但您可確認事件中樞已收到記錄資訊。 在 Azure 入口網站中，移至您的事件中樞並確認**內送郵件**計數大於零。 

![稽核記錄](media/monitor-queue-storage/event-hub-log.png)

您可使用安全性資訊及事件管理和監視工具，存取及讀取傳送到事件中樞的記錄資料。 如需詳細資訊，請參閱[傳送至事件中樞的監視資料有何作用？](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)。

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>存取 Log Analytics 工作區中的記錄

您可使用 Azure 監視器記錄查詢來存取傳送至 Log Analytics 工作區的記錄。

如需詳細資訊，請參閱[開始使用 Azure 監視器中的 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。

資料會儲存在 **StorageQueueLogs** 資料表中。  

#### <a name="sample-kusto-queries"></a>範例 Kusto 查詢

以下是一些您可以在 **記錄搜尋** 列中輸入的查詢，可協助您監視佇列儲存體。 這些查詢使用[新語言](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

> [!IMPORTANT]
> 當您從 [儲存體帳戶資源群組] 功能表選取 [ **記錄** ] 時，會開啟 Log Analytics，並將查詢範圍設定為目前的資源群組。 這表示記錄查詢只會包含該資源群組中的資料。 如果您想要執行包含來自其他 Azure 服務之其他資源或資料之資料的查詢，請從 [ **Azure 監視器**] 功能表中選取 [**記錄**]。 如需詳細資訊，請參閱 [Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍](/azure/azure-monitor/log-query/scope/)。

使用這些查詢可協助您監視 Azure 儲存體帳戶：

* 列出過去三天內最常見的 10 個錯誤。

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* 列出過去三天內導致最多錯誤的前 10 個作業。

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* 列出過去三天內最長端對端延遲的前 10 個作業。

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* 列出過去三天內導致伺服器端節流錯誤的所有作業。

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* 列出過去三天內具有匿名存取權的所有要求。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* 建立過去三天內所用作業的圓形圖。
    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>常見問題集

**Azure 儲存體是否支援受控磁碟或非受控磁碟的計量？**

否。 Azure 計算支援磁碟的計量。 如需詳細資訊，請參閱[受控和非受控磁碟的每一磁碟計量](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)。

## <a name="next-steps"></a>後續步驟

- 如需 Azure 佇列儲存體所建立之記錄和計量的參考，請參閱 [Azure 佇列儲存體監視資料參考](monitor-queue-storage-reference.md)。
- 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器來監視 Azure 資源](../../azure-monitor/insights/monitor-azure-resource.md)。
- 如需有關計量移轉的詳細資訊，請參閱 [Azure 儲存體計量移轉](../common/storage-metrics-migration.md)。
