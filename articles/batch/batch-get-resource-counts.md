---
title: 計算工作和節點的狀態數目
description: 計算 Azure Batch 作業和計算節點狀態的數目，以利管理及監視 Batch 解決方案。
ms.date: 06/18/2020
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 90f741b9ec5e17da4fd0cc95ef921e116b0c27dc
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960583"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>依狀態計算作業和節點數目以監視 Batch 解決方案

若要監視及管理大規模的 Azure Batch 解決方案，您可能需要判斷各種狀態的資源計數。 Azure Batch 提供有效率的作業，以取得 Batch 工作和計算節點的計數。 您可以使用這些作業，而不是可能耗費時間的清單查詢，以傳回有關大量工作或節點集合的詳細資訊。

- [取得工作計數](/rest/api/batchservice/job/gettaskcounts)可取得作業中的作用中、執行中和已完成工作的彙總計數，以及成功或失敗的工作計數。 

  藉由計算處於各個狀態的工作數目，您可以更輕鬆地向使用者顯示作業進度，或偵測可能會影響作業的非預期延遲或失敗。 「取得工作計數」適用於 Batch 服務 API 2017-06-01.5.1 起的版本，以及相關 SDK 和工具。

- [列出集區節點計數](/rest/api/batchservice/account/listpoolnodecounts)可取得每個集區中處於各種狀態的專用和低優先順序計算節點數目：正在建立、閒置、離線、已先佔、正在重新開機、正在重新安裝映像、正在啟動和其他狀態。

  藉由計算各個狀態的節點數目，您將可判斷是否有足夠的計算資源可執行作業，並找出集區的潛在問題。 「列出集區節點計數」適用於 Batch 服務 API 2018-03-01.6.1 起的版本，以及相關 SDK 和工具。

請注意，在某些情況下，這些作業所傳回的數位可能不是最新狀態。 如果您需要確定計數是正確的，請使用清單查詢來計算這些資源的計數。 [清單查詢] 也可讓您取得其他 Batch 資源（例如應用程式）的相關資訊。 如需將篩選條件套用至清單查詢的詳細資訊，請參閱[建立查詢以便有效率地列出 Batch 資源](batch-efficient-list-queries.md)。

## <a name="task-state-counts"></a>工作狀態計數

「取得工作計數」作業會依下列狀態計算工作數目：

- **作用中** - 已排入佇列且能夠執行、但目前尚未指派給計算節點的工作。 如果工作[相依於尚未完成的父工作](batch-task-dependencies.md)，也會計為 `active`。 
- **執行中** - 已指派給計算節點、但尚未完成的工作。 處於 `preparing` 或 `running` 狀態的工作會計為 `running`，如[取得工作相關資訊](/rest/api/batchservice/task/get)作業所示。
- **已完成** - 因已順利完成，或未順利完成且已達到其重試限制，而不再符合執行條件的工作。 
- **成功** - 工作執行結果為 `success` 的工作。 Batch 會藉由檢查 [executionInfo](/rest/api/batchservice/task/get) 屬性的 `TaskExecutionResult` 屬性來判斷工作是否成功。
- **失敗** - 工作執行結果為 `failure` 的工作。

下列 .NET 程式碼範例示範如何依狀態擷取工作計數：

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

您可以使用類似 REST 的模式和其他支援的語言來取得作業的工作計數。 

> [!NOTE]
> 2018-08-01.7.0 之前的 Batch 服務 API 版本也會在「取得工作計數」回應中傳回 `validationStatus` 屬性。 這個屬性會指出 Batch 是否已檢查狀態計數有沒有與「列出工作 API」所回報的狀態保持一致。 值為 `validated` 只表示 Batch 已至少對作業是否一致檢查過一次。 屬性的值 `validationStatus` 不會指出取得工作計數傳回的計數目前是否為最新狀態。

## <a name="node-state-counts"></a>節點狀態計數

「列出集區節點計數」作業會依據每個集區中的下列狀態為計算節點計數。 對於每個集區中的專用節點和低優先順序節點，會提供個別的彙總計數。

- **建立中** - 由 Azure 配置、尚未開始加入集區的 VM。
- **閒置** - 目前未執行工作的可用計算節點。
- **LeavingPool** - 因使用者明確加以移除，或是因集區調整大小或自動調整已關閉而退出集區的節點。
- **離線** - Batch 無法用來排程新工作的節點。
- **先佔** - 因 Azure 回收 VM 而已從集區中移除的低優先順序節點。 在可替換低優先順序 VM 容量時，可以重新初始化節點 `preempted`。
- **重新開機中** - 正在重新啟動的節點。
- **重新安裝映像中** - 正在重新安裝作業系統的節點。
- **執行中** - 正在執行一或多個工作 (非啟動工作) 的節點。
- **啟動中** - 正在啟動 Batch 服務的節點。 
- **StartTaskFailed** - [啟動工作](/rest/api/batchservice/pool/add#starttask)失敗並已耗盡所有重試次數，且其啟動工作設定了 `waitForSuccess` 的節點。 此類節點無法用來執行工作。
- **不明** - 失去與 Batch 服務的聯繫、且狀態不明的節點。
- **無法使用** - 因發生錯誤而無法用來執行工作的節點。
- **WaitingForStartTask** - 啟動工作已開始執行、但設定了 `waitForSuccess`且啟動工作未完成的節點。

下列 C# 程式碼片段說明如何列出目前帳戶中所有集區的節點計數：

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

下列 C# 程式碼片段說明如何列出目前帳戶中給定集區的節點計數。

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

您可以將類似的模式用於 REST 和其他支援的語言，以取得集區的節點計數。

## <a name="next-steps"></a>後續步驟

- 了解 [Batch 服務工作流程和主要資源](batch-service-workflow-features.md)，例如集區、節點、作業和工作。
- 瞭解如何將篩選套用至列出 Batch 資源的查詢，請參閱[建立查詢以有效率地列出 batch 資源](batch-efficient-list-queries.md)。
