---
title: 建立工作相依性來執行工作
description: 建立相依於其他工作完成的工作，以便在 Azure Batch 中處理 MapReduce 樣式和類似的巨量資料工作負載。
ms.topic: how-to
ms.date: 05/22/2017
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 05b1bb289c215208be448d8ca7de144c82b313b8
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936974"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>建立工作相依性，以便執行相依於其他工作的工作

您可以定義工作相依性，只有在父系工作完成後才執行一項工作或一組工作。 一些實用的工作相依性案例包括︰

- 雲端中的 MapReduce 樣式工作負載。
- 資料處理工作可表示為有向非循環圖 (DAG) 的工作。
- 轉譯前和轉譯後程序，其中的每項工作必須在下一項工作開始前完成。
- 下游工作相依於上游工作輸出的任何其他工作。

您可以利用 Batch 工作相依性，建立排定於一或多項父系工作完成後，才在計算節點上執行的工作。 例如，您可以建立一個將 3D 電影的每個影格以個別、平行工作轉譯的工作。 在成功轉譯所有影格之後，最終工作「合併工作」會將轉譯的影格合併為一部完整的電影。

根據預設，相依工作會排定只會在父系工作順利完成之後執行。 您可以指定相依性動作，以覆寫預設行為，並且在父系工作失敗時執行工作。 如需詳細資訊，請參閱[相依性動作](#dependency-actions)一節。  

您可以在一對一或一對多的關係中建立相依於其他工作的工作。 您也可以建立一個範圍相依性，其中的工作相依於在指定的工作識別碼範圍內完成的一組工作。 您可以結合這三種基本案例來建立多對多關聯性。

## <a name="task-dependencies-with-batch-net"></a>Batch .NET 的工作相依性

在此文章中，我們將討論如何使用 [Batch .NET][net_msdn] 程式庫來設定工作相依性。 我們會先告訴您如何對作業[啟用工作相依性](#enable-task-dependencies)，然後再示範如何[設定工作的相依性](#create-dependent-tasks)。 我們也說明如何指定相依性動作，以便在父系失敗時執行相依工作。 最後，我們將討論 Batch 支援的 [相依性案例](#dependency-scenarios) 。

## <a name="enable-task-dependencies"></a>啟用工作相依性

若要在 Batch 應用程式中使用工作相依性，您必須先將作業設定成使用工作相依性。 在 Batch .NET 中，在 [CloudJob][net_cloudjob] 上，藉由將其 [UsesTaskDependencies][net_usestaskdependencies] 屬性設定為 `true` 加以啟用：

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

在上述程式碼片段中，"batchClient" 是 [BatchClient][net_batchclient] 類別的執行個體。

## <a name="create-dependent-tasks"></a>建立相依的工作

若要建立相依於一或多個父系工作完成的工作，您可以指定此工作「相依於」其他工作。 在 Batch .NET 中，使用 [TaskDependencies][net_taskdependencies] 類別的執行個體來設定 [CloudTask][net_cloudtask].[DependsOn][net_dependson] 屬性：

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

此程式碼片段會使用工作識別碼 "Flowers" 建立相依工作。 "Flowers" 工作相依於 "Rain" 和 "Sun" 工作。 "Flowers" 工作將排定為只會在 "Rain" 和 "Sun" 工作順利完成後，才於計算節點上執行。

> [!NOTE]
> 根據預設，當工作處於**已完成**狀態且其**結束代碼**是 `0` 時，才會將其視為已順利完成。 在 Batch .NET 中，這表示 [CloudTask][net_cloudtask].[State][net_taskstate] 屬性值為 `Completed`，而 CloudTask 的 [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] 屬性值為 `0`。 如需變更方式的相關資訊，請參閱[相依性動作](#dependency-actions)一節。

## <a name="dependency-scenarios"></a>相依性案例

Azure Batch 中可使用的基本工作相依性案例有三種︰一對一、一對多、工作識別碼範圍相依性。 結合這三種案例即可提供第四種案例，也就是多對多。

| 案例&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 範例 | 圖例 |
|:---:| --- | --- |
|  [一對一](#one-to-one) |「taskB」相依於「taskA」 <p/> 「taskB」要等到「taskA」順利完成後，才會排定執行 |![圖表︰一對一工作相依性][1] |
|  [一對多](#one-to-many) |「taskC」同時相依於「taskA」和「taskB」 <p/> 「taskC」要等到「taskA」和「taskB」順利完成後，才會排定執行 |![圖表︰一對多工作相依性][2] |
|  [工作識別碼範圍](#task-id-range) |「taskD」相依於某一範圍的工作 <p/> 「taskD」要等到識別碼「1」到「10」的工作順利完成後，才會排定執行 |![圖表：工作識別碼範圍相依性][3] |

> [!TIP]
> 您可以建立**多對多**關聯性，例如工作 C、D、E 和 F 均相依於工作 A 和 B。舉例來說，在下游工作相依於多個上游工作輸出的平行前置處理案例中，這種關聯性就很有用。
> 
> 在本節的範例中，相依工作只會在父系工作順利完成之後才執行。 這種行為是相依工作的預設行為。 指定相依性動作來覆寫預設行為，即可在父系工作失敗時執行相依工作。 如需詳細資訊，請參閱[相依性動作](#dependency-actions)一節。

### <a name="one-to-one"></a>一對一

在一對一關聯性中，工作相依於一項父系工作的成功完成。 若要建立相依性，在填入 [CloudTask][net_cloudtask] 的 [DependsOn][net_dependson] 屬性時，對 [TaskDependencies][net_taskdependencies].[OnId][net_onid] 靜態方法提供單一工作識別碼。

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>一對多

在一對多關聯性中，工作相依於多項父工作的完成。 若要建立相依性，在填入 [CloudTask][net_cloudtask] 的 [DependsOn][net_dependson] 屬性時，對 [TaskDependencies][net_taskdependencies].[OnIds][net_onids] 靜態方法提供工作識別碼集合。

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>工作識別碼範圍

在父系工作範圍的相依性中，工作相依於識別碼落在範圍內之工作的完成。
若要建立相依性，在填入 [CloudTask][net_cloudtask] 的 [DependsOn][net_dependson] 屬性時，對 [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange] 靜態方法提供範圍中的第一個和最後一個工作識別碼。

> [!IMPORTANT]
> 在使用相依性的工作識別碼範圍時，範圍中只會選出識別碼代表的是整數值的工作。 因此，範圍 `1..10` 會選出工作 `3` 和 `7`，而非 `5flamingoes`。 
>
> 在評估範圍相依性時，前置的零並不具有任何意義，所以字串識別碼為 `4`、`04` 及 `004` 的工作都會落在範圍「之內」，而且都會被視為工作 `4`，因此第一項完成的工作將能滿足相依性。
>
> 範圍中的每項工作必須藉由下列方式來符合相依性：順利完成，或完成但對應至相依性動作的錯誤設定為 [符合]。 如需詳細資訊，請參閱[相依性動作](#dependency-actions)一節。

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>相依性動作

根據預設，只有在父系工作順利完成後，才會執行一項工作或一組工作。 在某些情況下，即使父系工作失敗，您也想要執行相依工作。 您可以指定相依性動作，以覆寫預設行為。 相依性動作會根據父系工作成功或失敗，指定相依工作是否有資格執行。 

例如，假設相依工作正在等候上游工作完成的資料。 如果上游工作失敗，相依工作仍然可以使用較舊的資料來執行。 在此情況下，儘管父系工作失敗，相依性動作仍可指定相依工作是否有資格執行。

相依性動作是以父系工作的結束情況為基礎。 您可以針對下列任一個結束條件指定相依性動作；若為 .NET，請參閱 [ExitConditions][net_exitconditions] 類別，以取得詳細資訊：

- 發生前置處理錯誤時。
- 發生檔案上傳錯誤時。 如果工作結束時的結束代碼是透過 **exitCodes** 或 **exitCodeRanges** 所指定，又發生檔案上傳錯誤，則優先執行結束代碼指定的動作。
- 工作結束時的結束代碼是由 **ExitCodes** 屬性所定義。
- 工作結束時的結束代碼落在 **ExitCodeRanges** 屬性所指定的範圍內。
- 預設的情況是，如果工作結束時的結束代碼不是由 **ExitCodes** 或 **ExitCodeRanges** 所定義，或如果工作結束時發生前置處理錯誤且未設定 **PreProcessingError** 屬性，或如果工作失敗並發生檔案上傳錯誤且未設定 **FileUploadError** 屬性。 

若要在 .NET 中指定相依性動作，請針對結束條件設定 [ExitOptions][net_exitoptions].[DependencyAction][net_dependencyaction] 屬性。 **DependencyAction** 屬性會採用兩個值之一︰

- 將 **DependencyAction** 屬性設定為 [符合]，表示如果父系工作結束時出現指定的錯誤，相依工作就有資格執行。
- 將 **DependencyAction** 屬性設定為 [封鎖]，表示相依工作沒有資格執行。

結束代碼 0 的**DependencyAction** 屬性預設設定為 [符合]，而所有其他結束情況的預設設定則為 [封鎖]。

下列程式碼片段可設定父系工作的 **DependencyAction** 屬性。 如果父系工作結束時發生前置處理錯誤，或出現指定的錯誤碼，則會封鎖相依工作。 如果父系工作結束時出現任何其他非零的錯誤，相依工作就有資格執行。

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>程式碼範例

[TaskDependencies][github_taskdependencies] 範例專案是 GitHub 上的 [Azure Batch 程式碼範例][github_samples]之一。 此 Visual Studio 解決方案示範︰

- 如何啟用作業的工作相依性
- 如何建立相依於其他工作的工作
- 如何在運算節點集區上執行這些工作。

## <a name="next-steps"></a>後續步驟

- Batch 的 [應用程式封裝](batch-application-packages.md) 功能提供了簡單的方法，供您部署您的工作在計算節點上執行的應用程式並設定其版本。
- 請參閱 Azure Batch 論壇中的[在 Batch 計算節點上安裝應用程式和暫存資料][forum_post]，以取得用來準備節點以執行工作的方法概觀。 這篇文章是由 Azure Batch 小組的其中一名成員所撰寫，非常適合做為入門指南，讓您了解將應用程式、工作輸入資料和其他檔案複製到計算節點的不同方法。

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_dependson]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_exitcode]: /dotnet/api/microsoft.azure.batch.taskexecutioninformation
[net_exitconditions]: /dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: /dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: /dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: /dotnet/api/microsoft.azure.batch
[net_onid]: /dotnet/api/microsoft.azure.batch.taskdependencies
[net_onids]: /dotnet/api/microsoft.azure.batch.taskdependencies
[net_onidrange]: /dotnet/api/microsoft.azure.batch.taskdependencies
[net_taskexecutioninformation]: /dotnet/api/microsoft.azure.batch.taskexecutioninformation
[net_taskstate]: /dotnet/api/microsoft.azure.batch.common.taskstate
[net_usestaskdependencies]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_taskdependencies]: /dotnet/api/microsoft.azure.batch.taskdependencies

[1]: ./media/batch-task-dependency/01_one_to_one.png "圖表︰一對一相依性"
[2]: ./media/batch-task-dependency/02_one_to_many.png "圖表︰一對多相依性"
[3]: ./media/batch-task-dependency/03_task_id_range.png "圖表︰工作識別碼範圍相依性"
