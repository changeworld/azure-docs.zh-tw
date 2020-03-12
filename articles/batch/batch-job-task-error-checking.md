---
title: 檢查作業和工作錯誤-Azure Batch |Microsoft Docs
description: 要檢查和疑難排解作業和工作的錯誤
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087002"
---
# <a name="job-and-task-error-checking"></a>作業和工作錯誤檢查

新增作業和工作時，可能會發生各種錯誤。 偵測這些作業的失敗很簡單，因為 API、CLI 或 UI 會立即傳回任何失敗。  不過，稍後在排程和執行作業和工作時，可能會發生失敗。

本文涵蓋作業和工作提交後可能發生的錯誤。 它會列出並說明需要檢查和處理的錯誤。

## <a name="jobs"></a>工作

「作業」是一或多個工作的群組，工作實際上指定要執行的命令列。

新增作業時，可以指定下列參數，這可能會影響作業失敗的方式：

- [作業條件約束](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - 您可以選擇性地指定 `maxWallClockTime` 屬性，以設定作業可以是作用中或執行中的最大時間量。 如果超過，作業將會以作業的[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob)中設定的 `terminateReason` 屬性來終止。
- [作業準備工作](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - 若已指定，則會在第一次針對節點上的作業執行工作時，執行作業準備工作。 作業準備工作可能會失敗，這會導致工作無法執行，且作業無法完成。
- [作業釋放工作](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - 只有在設定作業準備工作時，才能指定作業解除工作。 當作業終止時，作業釋放工作會在執行作業準備工作的每個集區節點上執行。 作業釋放工作可能會失敗，但作業仍會移至 `completed` 狀態。

### <a name="job-properties"></a>工作屬性

應檢查下列作業屬性是否有錯誤：

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)'：
  - `terminateReason` 屬性可以有值，指出已超過作業條件約束中指定的 `maxWallClockTime`，因此作業已終止。 如果已適當設定 [作業 `onTaskFailure`] 屬性，也可以將它設定為表示工作失敗。
  - 如果發生排程錯誤，則會設定[schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror)屬性。
 
### <a name="job-preparation-tasks"></a>作業準備工作

如果針對作業指定了作業準備工作，則該工作的實例將會在第一次在節點上執行作業的工作時執行。 在作業上設定的作業準備工作可視為工作範本，其中會執行多個作業準備工作實例，最多可達集區中的節點數目。

應檢查作業準備工作實例，以判斷是否有錯誤：
- 當作業準備工作執行時，觸發「作業準備」工作的工作將會移至 `preparing`的[狀態](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)。如果作業準備工作失敗，則觸發工作會還原為 `active` 狀態，而且不會執行。  
- 已執行的作業準備工作的所有實例，都可以使用[清單準備和釋放工作狀態](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)API 從作業中取得。 就像任何工作一樣，`failureInfo`、`exitCode`和 `result`等屬性都有可用的[執行資訊](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)。
- 如果作業準備工作失敗，則觸發作業工作將不會執行，作業將不會完成，且會停滯。 如果沒有其他工作可排程，則集區可能會未運用。

### <a name="job-release-tasks"></a>作業釋放工作

如果已為作業指定作業釋放工作，則當作業終止時，會在執行作業準備工作的每個集區節點上執行作業釋放工作的實例。  應檢查作業釋放工作實例，以判斷是否有錯誤：
- 所有正在執行之作業釋放工作的實例，都可以使用 API[清單準備和釋放工作狀態](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)從作業中取得。 就像任何工作一樣，`failureInfo`、`exitCode`和 `result`等屬性都有可用的[執行資訊](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)。
- 如果一或多個作業釋放工作失敗，則作業仍會終止，並移至 `completed` 狀態。

## <a name="tasks"></a>工作

作業工作可能會因為許多原因而失敗：

- 工作命令列失敗，並傳回非零的結束代碼。
- 已為工作指定 `resourceFiles`，但發生一或多個未下載的檔案。
- 已為工作指定 `outputFiles`，但發生一或多個檔案未上傳的失敗。
- 超過工作的已耗用時間（由工作[條件約束](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)中的 `maxWallClockTime` 屬性所指定）。

在所有情況下，都必須檢查下列屬性是否有錯誤和錯誤的相關資訊：
- Tasks [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation)屬性包含多個屬性，可提供錯誤的相關資訊。 [結果](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult)指出工作是否因任何原因而失敗，`exitCode` 和 `failureInfo` 提供有關失敗的詳細資訊。
- 工作一律會移至 [`completed`][狀態](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)，而不受其成功或失敗的影響。

工作失敗對作業的影響，以及任何任務相依性都必須考慮。  您可以為工作指定[exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions)屬性，以設定相依性和作業的動作。
- 對於相依性， [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction)會控制是否要封鎖或執行依存于失敗工作的工作。
- 針對此作業， [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction)會控制失敗的工作會導致作業停用、終止或保持不變。

### <a name="task-command-line-failures"></a>工作命令列失敗

執行工作命令列時，會將輸出寫入 `stderr.txt` 並 `stdout.txt`。 此外，應用程式可能會寫入應用程式特定的記錄檔。

如果工作執行所在的集區節點仍存在，則可以取得及查看記錄檔。 例如，Azure 入口網站會列出並可查看工作或集區節點的記錄檔。 多個 Api 也允許列出並取得工作檔案，例如 [[從工作取得](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask)]。

由於集區和集區節點經常是暫時的，因此會持續新增和刪除節點，因此建議保存記錄檔。 工作[輸出](https://docs.microsoft.com/azure/batch/batch-task-output-files)檔案是將記錄檔儲存至 Azure 儲存體的便利方式。

### <a name="output-file-failures"></a>輸出檔失敗
在每次檔案上傳時，Batch 會將 `fileuploadout.txt` 和 `fileuploaderr.txt` 兩個記錄檔寫入計算節點中。 若要進一步了解特定錯誤，您可以檢查這些記錄檔。 在從未嘗試過檔案上傳的情況下（例如因為工作本身無法執行），這些記錄檔將不存在。  

## <a name="next-steps"></a>後續步驟

檢查您的應用程式是否會執行完整的錯誤檢查;立即偵測並診斷問題可能非常重要。
