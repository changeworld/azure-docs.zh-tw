---
title: 將大量工作提交至 Batch 作業
description: 瞭解如何在單一 Azure Batch 作業中有效率地提交非常大量的工作。
ms.topic: how-to
ms.date: 12/30/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 08cf92507a4556afbf56c9cb7e2c9c1b3a6c9479
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831511"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>將大量工作提交至 Batch 作業

當您執行大規模 Azure Batch 工作負載時，您也許是想要將成千上萬，甚至更多的工作提交至單一作業。

本文說明如何以大幅增加的輸送量將大量的工作提交至單一批次作業。 提交工作之後，工作會進入 Batch 佇列，在您為作業指定的集區上進行處理。

## <a name="use-task-collections"></a>使用工作集合

新增大量工作時，請使用 Batch Api 提供的適當方法或多載，將工作新增為 *集合* ，而不是一次一個。 一般而言，您會藉由在逐一查看一組作業的輸入檔或參數時定義工作，來建構工作集合。

您可以在單一呼叫中新增的工作集合大小上限，取決於您所使用的 Batch API。

### <a name="apis-allowing-collections-of-up-to-100-tasks"></a>允許最多100個工作集合的 Api

這些 Batch Api 會將集合限制為100個工作。 視工作的大小而定，限制可能較小 (例如，如果工作有大量的資源檔或環境變數) 。

- [REST API](/rest/api/batchservice/task/addcollection)
- [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations)
- [Node.js API](/javascript/api/@azure/batch/task)

使用這些 Api 時，您需要提供邏輯來分割工作的數目以符合集合限制，以及在工作新增失敗時處理錯誤和重試。 如果工作集合太大而無法新增，要求會產生錯誤，應該以較少的工作重試。

### <a name="apis-allowing-collections-of-larger-numbers-of-tasks"></a>允許大量工作集合的 Api

其他 Batch Api 支援更大的工作集合，僅受提交用戶端上 RAM 的可用性限制。 這些 Api 會以透明的方式，將工作集合分成較低層級 Api 的「區塊」，並在工作新增失敗時重試。

- [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
- [Java API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
- [Azure Batch CLI 擴充功能](batch-cli-templates.md)與 Batch CLI 範本
- [Python SDK 擴充功能](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>增加工作提交的輸送量

將大型工作集合新增至作業可能需要一些時間。 例如，透過 .NET API 新增20000工作可能需要最多一分鐘的時間。 根據 Batch API 和您的工作負載，您可以藉由修改下列一或多項來改善工作輸送量。

### <a name="task-size"></a>工作大小

加入大型工作所花的時間會比新增較小的工作更多。 若要減少集合中每個工作的大小，您可以簡化工作命令列、減少環境變數數目，或者更有效率地處理工作執行需求。

例如，不使用大量資源檔，而是使用集區上的 [啟動](jobs-and-tasks.md#start-task) 工作來安裝工作相依性，或使用 [應用程式套件](batch-application-packages.md) 或 [Docker 容器](batch-docker-container-workloads.md)。

### <a name="number-of-parallel-operations"></a>平行作業的數目

根據 Batch API，您可以藉由增加 Batch 用戶端的並行作業數目上限來增加輸送量。 使用 .NET API 中的 [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) 屬性，或方法 (例如 Batch Python SDK 擴充功能中的 [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations)) 的 `threads` 參數，進行這項設定。 (這個屬性不適用於原生 Batch Python SDK。)

根據預設，這個屬性會設定為1，但您可以將它設定為較高的值，以改善作業的輸送量。 您會耗用網路頻寬和一些 CPU 效能，來換取增加的輸送量。 工作輸送量會增加到最多 100 乘以 `MaxDegreeOfParallelism` 或 `threads`。 在實務上，您應該將平行作業的數目設定為低於100。

 Azure Batch CLI 擴充功能與 Batch 範本，會根據可用核心數目自動增加並行作業數目，但是這個屬性在 CLI 中無法設定。

### <a name="http-connection-limits"></a>HTTP 連接限制

當 Batch 用戶端新增大量工作時，有許多並行 HTTP 連線可以節流處理其效能。 某些 Api 會限制 HTTP 連接的數目。 例如，使用 .NET API 進行開發時，[ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) 屬性預設會設為 2。 我們建議您將此值增加為接近或大於平行作業數目的值。

## <a name="example-batch-net"></a>範例：Batch .NET

下列 C# 程式碼片段會顯示使用 Batch .NET API 新增大量工作時，所要進行的設定。

若要增加工作輸送量，請針對 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) 的 [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) 屬性增加值。 例如：

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```

使用 [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync) 或 [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
) 方法的適當多載，將工作集合新增至作業。 例如：

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```

## <a name="example-batch-cli-extension"></a>範例：Batch CLI 擴充功能

使用 Azure Batch CLI 擴充功能與 [Batch CLI 範本](batch-cli-templates.md)來建立作業範本 JSON 檔案，其中包含[工作處理站](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md) (英文)。 工作處理站會根據單一工作定義，設定作業的相關工作集合。  

以下是一維參數化作業的範例工作範本，其中包含大量工作 (在此案例中為 250000) 。 工作命令列是簡單的 `echo` 命令。

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

若要使用範本執行作業，請參閱[使用 Azure Batch CLI 範本和檔案傳輸](batch-cli-templates.md)。

## <a name="example-batch-python-sdk-extension"></a>範例：Batch Python SDK 擴充功能

若要使用 Azure Batch Python SDK 擴充功能，請先安裝 Python SDK 和擴充功能：

```
pip install azure-batch
pip install azure-batch-extensions
```

設定 `BatchExtensionsClient`，它會使用 SDK 擴充功能：

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

建立要新增至作業的工作集合。 例如：

```python
tasks = list()
# Populate the list with your tasks
...
```

使用 [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations) 來新增工作集合。 設定 `threads` 參數來增加並行作業數目：

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Batch Python SDK 擴充功能也支援使用工作處理站的 JSON 規格，將工作參數新增至作業。 例如，為參數掃掠設定作業參數，類似於前述 Batch CLI 範本範例中的項目：

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

將作業參數新增至作業。 設定 `threads` 參數來增加並行作業數目：

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>後續步驟

- 深入了解如何使用 Azure Batch CLI 擴充功能，來搭配 [Batch CLI 範本](batch-cli-templates.md)。
- 深入了解 [Batch Python SDK 擴充功能](https://pypi.org/project/azure-batch-extensions/)。
- 閱讀 [Azure Batch 的最佳做法](best-practices.md)。
