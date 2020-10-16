---
title: 並行執行工作以充分使用 Batch 計算節點
description: 使用較少的計算節點，並在 Azure Batch 集區中的每個節點上平行執行工作，以提升效率並降低成本
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 8bc9f03f05d52df6e400be5c57033ab2a38fa8eb
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102960"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>並行執行工作以充分使用 Batch 計算節點

您可以在每個節點上同時執行多個工作，以將集區中較少數計算節點的資源使用量最大化。

雖然某些案例最適用于單一工作專屬的所有節點資源，但是當多個工作共用這些資源時，某些工作負載可能會看到較短的作業時間和較低的成本：

- 將可共用資料之工作的**資料傳輸降到最低**。 您可以將共用資料複製到較少的節點數目，然後在每個節點上平行執行工作，以大幅減少資料傳輸費用。 此做法尤其適用於當要複製到每個節點的資料必須在地理區域之間傳輸時。
- **最大化** 需要大量記憶體的工作記憶體使用量，但在短時間內，以及在執行期間的變數時間。 您可以運用具有更多記憶體的較少但較大的計算節點，有效率地處理這類高峰流量。 這些節點會有在每個節點上執行的平行工作，但是每個工作會在不同的時間利用節點的大量記憶體。
- 當集區中需要節點間通訊時，**緩和節點數目限制**。 目前，針對節點間通訊設定的集區限制為 50 個計算節點。 如果這類集區中的每個節點能夠以平行方式執行工作，則可以同時執行較多的工作。
- 複寫內部**部署計算**叢集，例如，當您第一次將計算環境移至 Azure 時。 如果目前的內部部署解決方案在每個計算節點執行多個工作，您可以增加節點工作的數目上限，以更密切地反映該組態。

## <a name="example-scenario"></a>範例案例

例如，假設有一個具有 CPU 和記憶體需求的工作應用程式，使 [標準 \_ D1](../cloud-services/cloud-services-sizes-specs.md) 節點足夠。 不過，若要在需要的時間內完成作業，需要1000個節點。

如果不使用 Standard\_D1 節點 (具有 1 個 CPU 核心)，您可以採用具有 16 個核心的 [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) 節點，並啟用平行工作執行。 這表示可以使用 *16 倍的節點* ，而不是1000節點，只需要63。 如果每個節點都需要大型應用程式檔或參考資料，則會再次改善工作持續時間和效率，因為資料只會複製到63節點。

## <a name="enable-parallel-task-execution"></a>啟用平行工作執行

您可以針對集區層級的平行工作執行，設定計算節點。 使用 Batch .NET 程式庫時，請在建立集區時設定 [>cloudpool.maxtaskspercomputenode TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 屬性。 如果您使用批次 REST API，請在集區建立期間設定要求主體中的 [taskSlotsPerNode](/rest/api/batchservice/pool/add) 元素。

> [!NOTE]
> 您只能在建立集區 `taskSlotsPerNode` 時設定元素和 [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 屬性。 建立集區之後，就無法修改它們。

Azure Batch 可讓您將每個節點的工作位置設定為 (4x) 節點核心數目。 例如，如果集區設定的節點大小為 [大] \(四個核心)，則 `taskSlotsPerNode` 可以設定為 16。 但是，不論節點有多少核心，每個節點不能有超過256個工作位置。 如需每個節點大小的核心數目的詳細資料，請參閱 [雲端服務的大小](../cloud-services/cloud-services-sizes-specs.md)。 如需服務限制的詳細資訊，請參閱 [Azure Batch 服務的配額和限制](batch-quota-limit.md)。

> [!TIP]
> 為您的集區建構[自動調整公式](/rest/api/batchservice/pool/enableautoscale)時，請務必考慮 `taskSlotsPerNode` 值。 例如，評估 `$RunningTasks` 的公式可能大幅受到每個節點的工作增加的影響。 如需詳細資訊，請參閱 [自動調整 Azure Batch 集區中的計算節點](batch-automatic-scaling.md)。

## <a name="specify-task-distribution"></a>指定工作散發

當啟用並行工作時，請務必指定要如何將工作分散到集區中的各個節點。

使用 [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) 屬性，您可以指定工作應該跨集區中的所有節點平均指派 (「散佈」)。 或者，您可以在工作指派到集區中其他節點之前，盡可能將最多工作指派給每個節點 (「封裝」)。

例如，假設上述範例中的 [標準 \_ D14](../cloud-services/cloud-services-sizes-specs.md) 節點集區 (，) 的 [>cloudpool.maxtaskspercomputenode. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 值為16。 如果已使用*Pack* [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype)設定[>cloudpool.maxtaskspercomputenode >cloudpool.taskschedulingpolicy](/dotnet/api/microsoft.azure.batch.cloudpool) ，則會充分利用每個節點的16個核心，並允許自動調整[集](batch-automatic-scaling.md)區移除未使用的節點 (節點，而不會從集區) 指派任何工作。 這可最小化資源使用量和節省金錢。

## <a name="define-variable-slots-per-task"></a>定義每項工作的變數位置

您可以使用 [CloudTask RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) 屬性來定義工作，並指定在計算節點上執行所需的插槽數目。 預設值為1。 如果您的工作在計算節點上有與資源使用量不同的加權，您可以設定變數工作位置。 這可讓每個計算節點擁有合理數目的並存執行工作，而不會佔用像是 CPU 或記憶體等系統資源。

例如，針對具有屬性的集區， `taskSlotsPerNode = 8` 您可以使用來提交多核心需要大量 CPU 的工作 `requiredSlots = 8` ，而其他工作則可以設定為 `requiredSlots = 1` 。 排程此混合工作負載時，需要大量 CPU 的工作才會在其計算節點上執行，而其他工作則可以同時執行 (多達八個工作，在其他節點上) 。 這可協助您在計算節點之間平衡工作負載，並提升資源使用效率。

> [!TIP]
> 使用變數工作位置時，可能會暫時無法排程具有更多必要位置的大型工作，因為沒有足夠的位置可用於任何計算節點，即使某些節點上仍有閒置的位置也是一樣。 您可以提高這些工作的作業優先順序，以增加其競爭節點上可用位置的機會。
>
> 當批次服務無法排程要執行的工作時，會發出 [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) ，然後在必要的位置可供使用之前，繼續重試排程。 您可以接聽該事件，以偵測可能的工作排程問題，並據以減緩。

> [!NOTE]
> 請勿將工作的指定 `requiredSlots` 為大於集區的 `taskSlotsPerNode` 。 這會導致工作永遠無法執行。 當您提交工作時，Batch 服務目前不會驗證這項衝突，因為工作在提交時可能沒有系結的集區，或藉由停用/重新啟用來變更為不同的集區。

## <a name="batch-net-example"></a>Batch .NET 範例

下列 [Batch .Net](/dotnet/api/microsoft.azure.batch) API 程式碼片段示範如何建立每個節點具有多個工作位置的集區，以及如何提交具有必要位置的工作。

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>建立每個節點具有多個工作位置的集區

此程式碼片段示範如何建立包含四個節點的集區，且每個節點都允許四個工作位置。 它會指定工作排程原則，該原則會先以工作填滿每個節點，再將工作指派給集區中的其他節點。

如需有關使用 Batch .NET API 新增集區的詳細資訊，請參閱 [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations)。

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>建立具有所需位置的工作

此程式碼片段會建立非預設的工作 `requiredSlots` 。 只有當計算節點上有足夠的可用插槽時，才會執行此工作。

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>列出具有執行中工作和位置計數的計算節點

此程式碼片段會列出集區中的所有計算節點，並印出每個節點執行中工作和工作位置的計數。

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>列出作業的工作計數

此程式碼片段會取得作業的工作計數，包括每個工作狀態的工作和工作位置計數。

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch REST 範例

下列 [BATCH REST](/rest/api/batchservice/) API 程式碼片段示範如何建立每個節點具有多個工作位置的集區，以及如何提交具有必要位置的工作。

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>建立每個節點具有多個工作位置的集區

此程式碼片段顯示建立集區的要求，該集區包含兩個大型節點，每個節點最多有四個工作。

如需有關如何使用 REST API 新增集區的詳細資訊，請參閱[將集區新增至帳戶](/rest/api/batchservice/pool/add)。

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>建立具有所需位置的工作

此程式碼片段顯示使用非預設值新增工作的要求 `requiredSlots` 。 只有當計算節點上有足夠的可用插槽時，才會執行此工作。

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>GitHub 上的程式碼範例

GitHub 上的 [>paralleltasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) 專案說明如何使用 [>cloudpool.maxtaskspercomputenode. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 屬性。

這個 C# 主控台應用程式使用 [Batch .NET](/dotnet/api/microsoft.azure.batch) 程式庫來建立具有一或多個計算節點的集區。 它會在這些節點上執行可設定的工作數目，以模擬變數負載。 應用程式的輸出會顯示執行每個工作的節點。 此應用程式也會提供作業參數和持續時間的摘要。

例如，以下是來自兩個不同執行 >paralleltasks 範例應用程式之輸出的摘要部分。 此處所顯示的作業持續時間不包括集區建立時間，因為每個作業都已提交至先前建立的集區，其計算節點在提交時處於 *閒置* 狀態。

首次執行範例應用程式會顯示該部分與集區中的單一節點，以及每個節點一項作業的預設設定，作業持續時間超過 30 分鐘。

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

第二次執行範例會顯示作業持續時間大幅降低。 這是因為集區已設定每個節點四項工作，讓平行工作執行在幾乎一季的時間內完成作業。

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>後續步驟

- 試用 [Batch Explorer](https://azure.github.io/BatchExplorer/) 熱度圖。 Batch Explorer是免費、功能豐富、獨立用戶端的工具，可以協助建立、偵錯及監視 Azure Batch 應用程式。 當您執行 [>paralleltasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) 範例應用程式時，Batch Explorer 熱度圖功能可讓您輕鬆地視覺化每個節點上的平行工作執行。
- 探索 [GitHub 上的 Azure Batch 範例](https://github.com/Azure/azure-batch-samples)。
- 深入瞭解 [Batch](batch-task-dependencies.md)工作相依性。
