---
title: 設計有效率的清單查詢
description: 藉由在要求集區、作業、工作和計算節點等 Batch 資源的資訊時篩選查詢，以提高效能。
ms.topic: how-to
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: bcf99dbc55d708af70a28155a3f98c20003e51f7
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960600"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>建立查詢以便有效率地列出 Batch 資源

幾乎所有 Batch 應用程式都必須執行某種類型的監視或其他查詢 Batch 服務的作業，並且通常是定期執行。 例如，若要判斷作業中是否有任何剩餘的已排入佇列的工作，您必須取得作業內每項工作的資料。 若要判斷集區中節點的狀態，您必須取得集區中每個節點的資料。 這篇文章說明如何以最有效率的方式執行這類查詢。

當您使用[Batch .net](/dotnet/api/microsoft.azure.batch)程式庫查詢作業、工作、計算節點和其他資源時，您可以減少服務所傳回的資料量，藉此提升 Azure Batch 應用程式的效能。

> [!NOTE]
> Batch 服務會針對在作業中計算工作的常見案例，以及計算 Batch 集區中的計算節點，提供 API 支援。 您可以呼叫[取得工作計數](/rest/api/batchservice/job/gettaskcounts)和[列出集區節點計數](/rest/api/batchservice/account/listpoolnodecounts)作業，而非使用這些作業的清單查詢。 這些作業比清單查詢更有效率，但會傳回更多不一定是最新狀態的限制資訊。 如需詳細資訊，請參閱[依狀態計算工作和計算節點](batch-get-resource-counts.md)。

## <a name="specify-a-detail-level"></a>指定詳細等級

在生產用 Batch 應用程式中，作業、工作和計算節點等實體的數量可能有數千個。 當您要求這些資源的資訊時，可能會有大量資料必須從 Batch 服務「傳送」到每個查詢上的應用程式。 透過限制項目數量及查詢所傳回的資訊類型，您可以加速查詢，因而提高應用程式的效能。

此 [Batch .NET](/dotnet/api/microsoft.azure.batch) API 程式碼片段會列出與作業相關聯的*每個*工作，以及各工作的*所有*屬性：

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

不過，您可以藉由對查詢套用「詳細資料層級」，以進行更有效率的清單查詢。 在 [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) 方法中提供 [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) 物件即可執行此動作。 此程式碼片段只是傳回已完成之工作的識別碼、命令列和計算節點資訊屬性：

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

在此範例案例中，如果作業中有數千個工作，則第二次查詢傳回結果的速度，通常會比第一次快很多。 使用 Batch .NET API 列出項目時，使用 ODATADetailLevel 的詳細資訊如 [下](#efficient-querying-in-batch-net)所示。

> [!IMPORTANT]
> 我們強烈建議「一律」在 .NET API 清單呼叫中提供 ODATADetailLevel 物件，以確保應用程式發揮最高效率和效能。 透過指定詳細層級，您可以幫助縮短 Batch 服務回應時間、提高網路使用率，以及讓用戶端應用程式的記憶體使用量降到最低。

## <a name="filter-select-and-expand"></a>篩選、選取及展開

[Batch .NET](/dotnet/api/microsoft.azure.batch) 和 [Batch REST](/rest/api/batchservice/) API 可讓您減少清單中傳回的項目數和每個項目傳回的資訊量。 您可以藉由在執行清單查詢時指定**篩選**、**選取**和**展開字串**來執行此動作。

### <a name="filter"></a>Filter

篩選字串是可減少傳回的項目數的運算式。 例如，您可以只列出作業的執行中工作，或僅列出已準備好執行工作的計算節點。

篩選字串包含一個或多個運算式，而運算式由屬性名稱、運算子和值構成。 可指定的屬性及每個屬性支援的運算子，取決於您查詢的每個實體類型。  多個運算式可以透過邏輯運算子 `and` 和 `or` 結合。

此範例篩選字串只會列出執行中「轉譯」工作： `(state eq 'running') and startswith(id, 'renderTask')`。

### <a name="select"></a>選取

選取字串限制每個項目傳回的屬性值。 您可以指定以逗號分隔的屬性名稱清單，而且只會針對查詢結果中的專案傳回那些屬性值。您可以為所查詢的實體類型指定任何屬性。

此範例選取字串會指定應該針對每個工作只傳回三個屬性： `id, state, stateTransitionTime`。

### <a name="expand"></a>展開

展開字串減少取得某些資訊所需的 API 呼叫次數。 當您使用展開字串時，可以透過單一 API 呼叫取得每個項目的相關詳細資訊。 您不需要先取得實體清單，然後要求清單中每個專案的資訊，而是在單一 API 呼叫中使用展開字串來取得相同的資訊，藉由減少 API 呼叫來協助改善效能。

與選取字串相似，展開字串可以控制清單查詢結果中是否包含特定資料。 當需要所有屬性但未指定選取字串時， *必須* 使用展開字串來取得統計資料資訊。 如果使用選取字串來取得屬性子集，則可以在選取字串中指定 `stats` ，不需要指定展開字串。

只有在用於列出作業、作業排程、作業和集區時，才支援展開字串。 目前，它僅支援統計資訊。

此範例展開字串指定應該在清單中傳回每個項目的統計資料資訊： `stats`。

> [!NOTE]
> 建構任何這三種查詢字串類型時 (篩選、選取和展開)，您必須確定屬性名稱和大小寫符合其對應的 Batch REST API 元素。 例如，使用 .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) 類別時，即使 .NET 屬性是 [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State)，也必須指定 **state** 而不是 **State**。 關於 .NET 和 REST API 之間的屬性對應，請參閱下表。

### <a name="rules-for-filter-select-and-expand-strings"></a>篩選、選取和展開字串的規則

- [篩選]、[選取] 和 [展開字串] 中的屬性名稱應該會如同在[BATCH REST](/rest/api/batchservice/) API 中一樣出現，即使您使用[batch .net](/dotnet/api/microsoft.azure.batch)或其他其中一個 batch sdk 也一樣。
- 所有屬性名稱都會區分大小寫，但屬性值不會區分大小寫。
- 日期/時間字串有兩種格式，開頭必須加上 `DateTime`。
  
  - W3C-DTF 格式範例：`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - RFC 1123 格式範例：`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- 布林值字串為 `true` 或 `false`。
- 如果指定無效的屬性或運算子，將會導致 `400 (Bad Request)` 錯誤。

## <a name="efficient-querying-in-batch-net"></a>在 Batch .NET 中有效率地查詢

在 [Batch .NET](/dotnet/api/microsoft.azure.batch) API 內，[ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) 類別用來提供篩選、選取和展開字串給清單作業。 ODataDetailLevel 物件有三個公用字串屬性，可以在建構函式中指定或是直接在物件上設定。 然後您可以將 ODataDetailLevel 物件當作參數，傳遞到各種清單作業，例如 [ListPools](/dotnet/api/microsoft.azure.batch.pooloperations)、[ListJobs](/dotnet/api/microsoft.azure.batch.joboperations) 和 [ListTasks](/dotnet/api/microsoft.azure.batch.joboperations)。

- [ODATADetailLevel. odatadetaillevel.filterclause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause)：限制傳回的專案數。
- [ODATADetailLevel. SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause)：指定隨著每個專案一起傳回的屬性值。
- [ODATADetailLevel. ExpandClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.expandclause)：取得單一 API 呼叫中所有專案的資料，而不是針對每個專案個別呼叫。

下列程式碼片段使用 Batch .NET API，有效率地向 Batch 服務查詢一組特定集區的統計資料。 在此案例中，Batch 使用者具有測試與生產的集區。 這些測試集區識別碼前面會加上 "test"，而生產集區識別碼則會加上 "prod"。 在程式碼片段中，「myBatchClient」  是適當初始化的 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) 類別的執行個體。

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> 使用「選取」和「展開」子句設定的 [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) 執行個體也可以傳遞到適當的 Get 方法 (例如 [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__))，以限制傳回的資料量。

## <a name="batch-rest-to-net-api-mappings"></a>Batch REST 與 .NET API 的對應

篩選、選取和展開字串中的屬性名稱在名稱和大小寫方面， 必須 反映其 REST API 對應項目。 下表提供 .NET 和 REST API 對應項目之間的對應。

### <a name="mappings-for-filter-strings"></a>篩選字串的對應

- **.NET 清單方法**：此欄中的每個 .NET API 方法都接受 [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) 物件作為參數。
- **REST 清單要求**：此資料行的每個 REST API 頁面都連結至一個資料表，其中指定「篩選」字串中允許的屬性和作業。 當您建立[ODATADetailLevel odatadetaillevel.filterclause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause)字串時，會使用這些屬性名稱和作業。

| .NET 清單方法 | REST 清單要求 |
| --- | --- |
| [CertificateOperations.ListCertificates](/dotnet/api/microsoft.azure.batch.certificateoperations) |[列出帳戶中的憑證](/rest/api/batchservice/certificate/list) |
| [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask) |[列出與工作相關聯的檔案](/rest/api/batchservice/file/listfromtask) |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus](/dotnet/api/microsoft.azure.batch.joboperations) |[列出作業準備和作業解除工作的狀態](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) |
| [JobOperations.ListJobs](/dotnet/api/microsoft.azure.batch.joboperations) |[列出帳戶中的作業](/rest/api/batchservice/job/list) |
| [JobOperations.ListNodeFiles](/dotnet/api/microsoft.azure.batch.joboperations) |[列出節點上的檔案](/rest/api/batchservice/file/listfromcomputenode) |
| [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) |[列出與作業相關聯的工作](/rest/api/batchservice/task/list) |
| [JobScheduleOperations.ListJobSchedules](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[列出帳戶中的作業排程](/rest/api/batchservice/jobschedule/list) |
| [JobScheduleOperations.ListJobs](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[列出與作業排程相關聯的作業](/rest/api/batchservice/job/listfromjobschedule) |
| [PoolOperations.ListComputeNodes](/dotnet/api/microsoft.azure.batch.pooloperations) |[列出集區中的計算節點](/rest/api/batchservice/computenode/list) |
| [PoolOperations.ListPools](/dotnet/api/microsoft.azure.batch.pooloperations) |[列出帳戶中的集區](/rest/api/batchservice/pool/list) |

### <a name="mappings-for-select-strings"></a>選取字串的對應

- **Batch .NET 類型**：Batch .NET API 類型。
- **REST API 實體**：此資料行中的每個頁面包含一個或多個資料表，其中列出類型的 REST API 屬性名稱。 建構「選取」  字串時會使用這些屬性名稱。 當您建立[ODATADetailLevel SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause)字串時，會使用這些相同的屬性名稱。

| Batch .NET types | REST API entities |
| --- | --- |
| [[MSSQLSERVER 的通訊協定內容]](/dotnet/api/microsoft.azure.batch.certificate) |[取得憑證的相關資訊](/rest/api/batchservice/certificate/get) |
| [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) |[取得作業的相關資訊](/rest/api/batchservice/job/get) |
| [CloudJobSchedule](/dotnet/api/microsoft.azure.batch.cloudjobschedule) |[取得作業排程的相關資訊](/rest/api/batchservice/jobschedule/get) |
| [ComputeNode](/dotnet/api/microsoft.azure.batch.computenode) |[取得節點的相關資訊](/rest/api/batchservice/computenode/get) |
| [CloudPool](/dotnet/api/microsoft.azure.batch.cloudpool) |[取得集區的相關資訊](/rest/api/batchservice/pool/get) |
| [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) |[取得工作的相關資訊](/rest/api/batchservice/task/get) |

## <a name="example-construct-a-filter-string"></a>範例：建構篩選字串

建構 [ODATADetailLevel.FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause) 的篩選字串時，請參閱「篩選字串的對應」下方的資料表，找出與您想要執行的清單作業相對應的 REST API 文件頁面。 在該頁面的第一個含有多資料列的資料表中，您可以找到可篩選的屬性及其支援的運算子。 例如，如果您想要擷取其結束代碼不為零的所有工作，[列出與作業相關聯的工作](/rest/api/batchservice/task/list)上的這一列會指定適用的屬性字串和允許的運算子：

| 屬性 | 允許的作業 | 類型 |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

因此，為了列出具有非零結束碼的所有作業，篩選字串如下：

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>範例：建構選取字串

若要建構 [ODATADetailLevel.SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause)，請參閱「選取字串的對應」下方的資料表，並瀏覽至與您要列出的實體類型相對應的 REST API 頁面。 在該頁面的第一個含有多資料列的資料表中，您可以找到可選取的屬性及其支援的運算子。 例如，如果您只想要擷取清單中每個工作的識別碼和命令列，您將在[取得工作的相關資訊](/rest/api/batchservice/task/get)上的適當資料表中找到這幾列：

| 屬性 | 類型 | 注意 |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

為了讓每個列出的作業只包含識別碼和命令列，選取字串如下列：

`id, commandLine`

## <a name="code-samples"></a>程式碼範例

### <a name="efficient-list-queries-code-sample"></a>有效率的清單查詢程式碼範例

GitHub 上的[EfficientListQueries](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries)範例專案會顯示有效率的清單查詢如何影響應用程式的效能。 這個 C# 主控台應用程式會建立並將大量工作加入至作業。 然後，其會對 [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) 方法進行多個呼叫，並傳遞設定了不同屬性值的 [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) 物件，以變更要傳回的資料量。 它會產生類似下列的輸出：

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

如同經過的時間所顯示的，您可以透過限制屬性和傳回的項目數目來大幅降低查詢回應時間。 您可以在 GitHub 上的 [azure-batch-samples](https://github.com/Azure-Samples/azure-batch-samples) 存放庫中，找到此範例專案與其他範例專案。

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics 程式庫和程式碼範例

除了上述的 EfficientListQueries 程式碼範例， [BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics)範例專案也會示範如何使用 Batch API 有效率地監視 Azure Batch 作業進度。

[BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) 範例包含可併入自己專案的 .NET 類別庫專案，以及用來運作和示範如何使用程式庫的簡單命令列程式。

專案內的範例應用程式會示範下列作業：

1. 選取特定屬性以便只下載您需要的屬性
2. 篩選狀態轉換時間以便只下載上次查詢之後的變更

例如，下列方法會出現在 BatchMetrics 程式庫。 它會傳回 ODATADetailLevel，指出只應該取得所查詢實體的 `id` 和 `state` 屬性。 它也會指出只應傳回自指定的 `DateTime` 參數之後其狀態已變更的實體。

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用並行節點工作，將 Azure Batch 計算資源使用量最大化](batch-parallel-node-tasks.md)。 某些類型的工作負載可受益于在較大（但較少）計算節點上執行平行作業。 如需這類案例的詳細資訊，請查看 [範例案例](batch-parallel-node-tasks.md#example-scenario) 。
- 瞭解如何藉[由依狀態計算工作和節點來監視 Batch 解決方案](batch-get-resource-counts.md)


[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_ctor]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_expand]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_filter]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_select]: /dotnet/api/microsoft.azure.batch.odatadetaillevel

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[rest_list_certs]: /rest/api/batchservice/certificate/list
[rest_list_compute_nodes]: /rest/api/batchservice/computenode/list
[rest_list_job_schedules]: /rest/api/batchservice/jobschedule/list
[rest_list_jobprep_status]: /rest/api/batchservice/job/listpreparationandreleasetaskstatus
[rest_list_jobs]: /rest/api/batchservice/job/list
[rest_list_nodefiles]: /rest/api/batchservice/file/listfromcomputenode
[rest_list_pools]: /rest/api/batchservice/pool/list
[rest_list_schedule_jobs]: /rest/api/batchservice/job/listfromjobschedule
[rest_list_task_files]: /rest/api/batchservice/file/listfromtask
[rest_list_tasks]: /rest/api/batchservice/task/list

[rest_get_cert]: /rest/api/batchservice/certificate/get
[rest_get_job]: /rest/api/batchservice/job/get
[rest_get_node]: /rest/api/batchservice/computenode/get
[rest_get_pool]: /rest/api/batchservice/pool/get
[rest_get_schedule]: /rest/api/batchservice/jobschedule/get
[rest_get_task]: /rest/api/batchservice/task/get

[net_cert]: /dotnet/api/microsoft.azure.batch.certificate
[net_job]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_node]: /dotnet/api/microsoft.azure.batch.computenode
[net_pool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_schedule]: /dotnet/api/microsoft.azure.batch.cloudjobschedule
[net_task]: /dotnet/api/microsoft.azure.batch.cloudtask

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
