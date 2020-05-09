---
title: 監視 Azure 儲存體 |Microsoft Docs
description: 瞭解如何監視 Azure 儲存體的效能和可用性。
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5564634471045838dae3344dc883b6fdc203711e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722917"
---
# <a name="monitoring-azure-storage"></a>監視 Azure 儲存體

當您有依賴 Azure 資源的重要應用程式和商務程式時，您會想要監視這些資源的可用性、效能和操作。 本文說明 Azure 儲存體所產生的監視資料，以及如何使用 Azure 監視器的功能來分析此資料的警示。

> [!NOTE]
> Azure 監視器中的 Azure 儲存體記錄處於公開預覽狀態，並可在所有公用雲端區域中進行預覽測試。 若要註冊預覽，請參閱[此頁面](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。  此預覽會針對一般用途 v1 和一般用途 v2 儲存體帳戶中的 blob （包括 Azure Data Lake Storage Gen2）、檔案、佇列、資料表、premium 儲存體帳戶啟用記錄。 不支援傳統儲存體帳戶。

## <a name="monitor-overview"></a>監視總覽

每個儲存體資源的 Azure 入口網站中的 [**總覽**] 頁面包含資源使用量的簡要觀點，包括其要求和每小時計費使用量。 這是有用的資訊，但只提供少量的監視資料。 這其中的部分資料會自動收集，並可在您建立儲存體資源時立即進行分析。 您可以使用一些設定來啟用其他類型的資料收集。

## <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？
Azure 儲存體會使用[Azure 監視器](../../azure-monitor/overview.md)建立監視資料，這是 Azure 中的完整堆疊監視服務，除了其他雲端和內部部署的資源之外，還提供一組完整的功能來監視您的 Azure 資源。 

從使用[Azure 監視器監視 Azure 資源](../../azure-monitor/insights/monitor-azure-resource.md)一文開始，其中說明下列各項：

- Azure 監視器是什麼？
- 與監視相關聯的成本
- 監視 Azure 中所收集的資料
- 設定資料收集
- Azure 中用來分析和警示監視資料的標準工具

下列各節將描述從 Azure 儲存體收集的特定資料，並提供使用 Azure 工具來設定資料收集和分析此資料的範例，以建立于本文中。

## <a name="monitoring-data-from-azure-storage"></a>監視 Azure 儲存體的資料

Azure 儲存體會收集與其他 Azure 資源相同的監視資料類型，如[從 Azure 資源監視資料](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)中所述。 如需 Azure 儲存體所建立之記錄和計量的詳細參考，請參閱[Azure 儲存體監視資料參考](monitor-storage-reference.md)。

Azure 監視器中的計量和記錄僅支援 Azure Resource Manager 儲存體帳戶。 Azure 監視器不支援傳統儲存體帳戶。 如果您想要在傳統儲存體帳戶上使用計量或記錄，您需要遷移至 Azure Resource Manager 儲存體帳戶。 請參閱[遷移至 Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)。

如有需要，您可以繼續使用傳統計量和記錄。 事實上，傳統計量和記錄檔會與 Azure 監視器中的計量和記錄平行提供。 除非 Azure 儲存體結束舊版計量和記錄上的服務，否則支援仍會保留在原處。 

### <a name="logs-in-azure-monitor-preview"></a>Azure 監視器中的記錄（預覽）

只有在對服務端點提出要求時，才會建立記錄項目。 例如，如果儲存體帳戶在其 blob 端點中有活動，而不是在其資料表或佇列端點中，則只會建立與 blob 服務相關的記錄。 Azure 儲存體記錄包含有關儲存體服務成功和失敗要求的詳細資訊。 這項資訊可用來監視個別要求，並診斷儲存體服務的問題。 系統會以最佳方式來記錄要求。

#### <a name="logging-authenticated-requests"></a>記錄驗證要求

 系統將記錄下列類型的驗證要求：

- 成功的要求
- 失敗的要求，包括逾時、節流、網路、授權和其他錯誤
- 使用共用存取簽章 (SAS) 或 OAuth 的要求，包括失敗和成功的要求
- 分析資料的要求（ **$logs**容器中的傳統記錄資料，以及 **$metric**資料表中的類別度量資料）

儲存體服務本身所提出的要求（例如記錄檔建立或刪除）不會記錄下來。 記錄資料的完整清單記載于[儲存體記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)和[儲存體記錄檔格式](monitor-storage-reference.md)主題。

#### <a name="logging-anonymous-requests"></a>記錄匿名要求

 系統將記錄下列類型的匿名要求：

- 成功的要求
- 伺服器錯誤
- 用戶端和伺服器的逾時錯誤
- 失敗的 GET 要求，錯誤碼為 304 (未修改)

系統不會記錄所有其他失敗的匿名要求。 記錄資料的完整清單記載于[儲存體記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)和[儲存體記錄檔格式](monitor-storage-reference.md)主題。

## <a name="configuration"></a>設定

系統會自動收集平臺計量和活動記錄檔，但您必須建立診斷設定來收集資源記錄，或將它們轉送到 Azure 監視器外部。 如需使用 Azure 入口網站、CLI 或 PowerShell 建立診斷設定的詳細程式，請參閱[建立診斷設定以收集 Azure 中的平臺記錄和計量](../../azure-monitor/platform/diagnostic-settings.md)。

當您建立診斷設定時，您必須選擇您想要啟用記錄的儲存體類型（blob、佇列、資料表、檔案）。 如果您在 Azure 入口網站中建立診斷設定，則可以從清單中選取資源。 如果您使用 PowerShell 或 Azure CLI，則必須使用儲存體類型的資源識別碼。 您可以藉由開啟儲存體帳戶的 [**屬性**] 頁面，在 [Azure 入口網站中找到資源識別碼。

您也必須指定要收集記錄的作業類別。 下表列出 Azure 儲存體的類別：

| 類別 | 描述 |
|:---|:---|
| StorageRead | Blob 的讀取作業。  |
| StorageWrite | Blob 上的寫入作業。 |
| StorageDelete | Blob 上的刪除作業。 |

## <a name="analyzing-metric-data"></a>分析度量資料

您可以使用計量瀏覽器，利用來自其他 Azure 服務的計量來分析 Azure 儲存體的計量。 從 [ **Azure 監視器**] 功能表選擇 [**計量**] 來開啟計量瀏覽器。 如需使用此工具的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)。 

下列範例說明如何檢視帳戶層級上的**交易**。

![在 Azure 入口網站中存取計量的螢幕擷取畫面](./media/monitor-storage/access-metrics-portal.png)

針對支援維度的計量，您可以使用所需的維度值來篩選計量。 下列範例說明如何藉由選取 [API 名稱]**** 維度的值，來檢視特定作業在帳戶層級上的 [交易]****。

![在 Azure 入口網站中存取具有維度之計量的螢幕擷取畫面](./media/monitor-storage/access-metrics-portal-with-dimension.png)

如需 Azure 儲存體支援之維度的完整清單，請參閱[計量維度](monitor-storage-reference.md#metrics-dimensions)。

Azure 儲存體的所有計量都位於下列命名空間：

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices

如需所有 Azure 監視器支援計量（包括 Azure 儲存體）的清單，請參閱[Azure 監視器支援的計量](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)。


### <a name="access-metrics"></a>存取計量

> [!TIP]
> 若要查看 Azure CLI 或 .NET 範例，請選擇下方對應的索引標籤。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>列出度量定義

您可以列出儲存體帳戶的計量定義，或個別的儲存體服務（例如 blob、檔案、資料表或佇列服務）。 使用[AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) Cmdlet。

在此範例中，請`<resource-ID>`將預留位置取代為整個儲存體帳戶的資源識別碼，或是個別儲存體服務（例如 blob、檔案、資料表或佇列服務）的資源識別碼。 您可以在 Azure 入口網站的儲存體帳戶的 [**屬性**] 頁面中找到這些資源識別碼。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>讀取度量值

您可以讀取儲存體帳戶的帳戶層級計量值，或個別儲存體服務（例如 blob、檔案、資料表或佇列服務）。 使用[AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) Cmdlet。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-account-level-metric-definition"></a>列出帳戶層級的度量定義

您可以列出儲存體帳戶的計量定義，或個別的儲存體服務（例如 blob、檔案、資料表或佇列服務）。 使用[az monitor 計量 list-定義](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions)命令。
 
在此範例中，請`<resource-ID>`將預留位置取代為整個儲存體帳戶的資源識別碼，或是個別儲存體服務（例如 blob、檔案、資料表或佇列服務）的資源識別碼。 您可以在 Azure 入口網站的儲存體帳戶的 [**屬性**] 頁面中找到這些資源識別碼。

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>讀取帳戶層級的度量值

您可以讀取儲存體帳戶的計量值，或個別儲存體服務（例如 blob、檔案、資料表或佇列服務）。 使用[az monitor 計量 list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)命令。

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure 監視器提供[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)來讀取計量定義和值。 [範例程式碼](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)示範如何使用 SDK 搭配不同的參數。 您必須使用 `0.18.0-preview` 或更新版本的儲存體計量。
 
在這些範例中，請`<resource-ID>`將預留位置取代為整個儲存體帳戶的資源識別碼，或是個別儲存體服務（例如 blob、檔案、資料表或佇列服務）的資源識別碼。 您可以在 Azure 入口網站的儲存體帳戶的 [**屬性**] 頁面中找到這些資源識別碼。

將`<subscription-ID>`變數取代為您的訂用帳戶識別碼。  如需如何`<tenant-ID>`取得、 `<application-ID>`和`<AccessKey>`之值的指引，請參閱[如何：使用入口網站建立可存取資源的 Azure AD 應用程式和服務主體](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/)。 

#### <a name="list-account-level-metric-definition"></a>列出帳戶層級的度量定義

下列範例示範如何列出帳戶層級的計量定義：

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

#### <a name="read-account-level-metric-values"></a>讀取帳戶層級的度量值

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

#### <a name="read-multi-dimensional-metric-values"></a>讀取多維度度量值

對於多維度的計量，如果您想要讀取特定維度值的計量資料，就必須定義中繼資料篩選條件。

下列範例示範如何讀取支援多維度計量的計量資料：

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
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

## <a name="analyzing-log-data"></a>分析記錄資料

您可以使用儲存體帳戶中的 blob、事件資料，或透過記錄分析查詢來存取資源記錄。

如需這些記錄中所顯示欄位的詳細參考，請參閱[Azure 儲存體監視資料參考](monitor-storage-reference.md)。

> [!NOTE]
> Azure 監視器中的 Azure 儲存體記錄處於公開預覽狀態，並可在所有公用雲端區域中進行預覽測試。 若要註冊預覽，請參閱[此頁面](https://www.microsoft.com)。  此預覽會針對一般用途 v1 和一般用途 v2 儲存體帳戶中的 blob （包括 Azure Data Lake Storage Gen2）、檔案、佇列、資料表、premium 儲存體帳戶啟用記錄。 不支援傳統儲存體帳戶。

### <a name="access-logs-in-a-storage-account"></a>存取儲存體帳戶中的記錄

記錄會顯示為儲存在目標儲存體帳戶中容器的 blob。 資料會收集並儲存在單一 blob 內，做為以行分隔的 JSON 承載。 此 blob 名稱會遵循下列命名慣例：

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

以下是範例：

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-event-hub"></a>存取事件中樞內的記錄

傳送至事件中樞的記錄不會儲存為檔案，但您可以確認事件中樞已收到記錄資訊。 在 [Azure 入口網站中，流覽至您的事件中樞，然後確認內送**訊息**計數大於零。 

![稽核記錄](media/monitor-storage/event-hub-log.png)

您可以使用安全性資訊和事件管理和監視工具，存取和讀取傳送到事件中樞的記錄資料。 如需詳細資訊，請參閱[我可以如何處理傳送到事件中樞的監視資料？](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)。

### <a name="access-logs-in-log-analytics-workspace"></a>Log Analytics 工作區中的存取記錄

您可以使用 Azure 監視器記錄查詢，存取傳送至 Log Analytics 工作區的記錄。

請參閱[Azure 監視器中的 Log Analytics 入門](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。

資料會儲存在下列資料表中。

| Table | 描述 |
|:---|:---|
|StorageBlobLogs | 描述 blob 儲存體中活動的記錄。 |
|StorageFileLogs | 描述檔案共用中活動的記錄檔。 |
|StorageQueueLogs | 描述佇列中活動的記錄。|
|StorageTableLogs| 描述資料表中活動的記錄。|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Azure 儲存體 Azure 監視器中的 Log Analytics 查詢

以下是一些您可以在**記錄搜尋**搜尋列中輸入的查詢，以協助您監視 Azure 儲存體帳戶。 這些查詢使用[新語言](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

以下是您可以用來協助您監視 Azure 儲存體帳戶的查詢。

* 列出過去3天內最常見的10個錯誤。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* 列出過去3天內導致最多錯誤的前10個作業。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* 列出過去3天內最長端對端延遲的前10個作業。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* 列出在過去3天內導致伺服器端節流錯誤的所有作業。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* 列出過去3天內具有匿名存取權的所有要求。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* 建立過去3天使用的作業圓形圖。
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>常見問題集

**Azure 儲存體是否支援受控磁碟或非受控磁碟的計量？**

否。Azure 計算支援磁碟的計量。 如需詳細資訊，請參閱[這篇文章](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)。

## <a name="next-steps"></a>後續步驟

- [Azure 儲存體監視資料參考](monitor-storage-reference.md)，以取得 Azure 儲存體所建立之記錄和計量的參考。
- [使用 Azure 監視器監視 azure 資源](../../azure-monitor/insights/monitor-azure-resource.md)，以取得有關監視 azure 資源的詳細資料。
- [Azure 儲存體計量移轉](./storage-metrics-migration.md)

