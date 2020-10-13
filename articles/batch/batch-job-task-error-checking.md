---
title: 檢查作業和工作錯誤
description: 檢查錯誤並針對作業和工作進行疑難排解
author: mscurrell
ms.topic: how-to
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: ece0f1473b3c453ca5506f06b7ef094533d146aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85964323"
---
# <a name="job-and-task-error-checking"></a>作業和工作錯誤檢查

新增作業和工作時，可能會發生各種錯誤。 要偵測這些作業是否失敗並不難，因為 API、CLI 或 UI 會立即傳回所有失敗。  不過，有些失敗可能會在已排定和執行作業和工作後才發生。

本文將說明提交作業和工作後可能發生的錯誤。 並且列出及說明需要檢查和處理的錯誤。

## <a name="jobs"></a>工作

作業是一個或多個工作的群組，而工作會實際指定要執行的命令列。

下列是您可以在新增作業時指定的參數，而這些參數可能會影響作業如何失敗：

- [作業條件約束](/rest/api/batchservice/job/add#jobconstraints)
  - 您可以選擇性地指定 `maxWallClockTime` 屬性，以設定作業在作用中或執行中的時間上限。 如果超過，作業將會以作業在 [executionInfo](/rest/api/batchservice/job/get#cloudjob) 中設定的 `terminateReason` 屬性來終止。
- [作業準備工作](/rest/api/batchservice/job/add#jobpreparationtask)
  - 若指定此項目，當您第一次在節點上執行作業的工作時，系統會執行作業準備工作。 作業準備工作可能會失敗，這會導致工作無法執行，且作業無法完成。
- [作業釋放工作](/rest/api/batchservice/job/add#jobreleasetask)
  - 只有在已設定作業準備工作時，才能指定作業釋放工作。 當作業終止時，作業釋放工作會在執行作業準備工作的每個集區節點上執行。 作業釋放工作可能會失敗，但作業仍會移至 `completed` 狀態。

### <a name="job-properties"></a>作業屬性

您應檢查下列作業屬性是否有錯誤：

- '[executionInfo](/rest/api/batchservice/job/get#jobexecutioninformation)'：
  - `terminateReason` 屬性的值會指出作業條件約束中指定的 `maxWallClockTime` 已超過，因此作業終止。 如果已適當設定作業的 `onTaskFailure` 屬性，則也可以將其設定為指出工作已失敗。
  - 如果發生排程錯誤，則會設定 [schedulingError](/rest/api/batchservice/job/get#jobschedulingerror) 屬性。
 
### <a name="job-preparation-tasks"></a>作業準備工作

如果針對作業指定了作業準備工作，則該工作的執行個體會在您第一次在節點上執行該作業的工作執行。 在作業上設定的作業準備工作可視為工作範本，其中可執行多個作業準備工作執行個體，其數目最多可與集區中的節點數目一樣。

您應檢查作業準備工作執行個體，判斷其是否有以下錯誤：
- 當作業準備工作執行時，觸發作業準備工作的工作將會移至 `preparing` 的[狀態](/rest/api/batchservice/task/get#taskstate)；如果作業準備工作失敗，則觸發工作會還原為 `active` 狀態，而且不會執行。  
- 您可以使用[清單準備和釋放工作狀態](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) API，從作業中取得所有已執行的作業準備工作執行個體。 就像任何工作一樣，[執行資訊](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)會隨附 `failureInfo`、`exitCode` 和 `result` 等屬性。
- 如果作業準備工作失敗，則觸發作業工作將不會執行，作業將不會完成，且會停滯。 如果沒有其他作業有可排程的工作，則集區可能會進入未使用的狀態。

### <a name="job-release-tasks"></a>作業釋放工作

如果已為作業指定作業釋放工作，則當作業終止時，作業釋放工作的執行個體就會在已執行作業準備工作的每個集區節點上執行。  您應檢查作業釋放工作執行個體，判斷其是否有以下錯誤：
- 您可以使用[清單準備和釋放工作狀態](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) API，從作業中取得執行中的作業釋放工作執行個體。 就像任何工作一樣，[執行資訊](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)會隨附 `failureInfo`、`exitCode` 和 `result` 等屬性。
- 如果一個或多個作業釋放工作失敗，則作業仍會終止，並移至 `completed` 狀態。

## <a name="tasks"></a>工作

作業工作失敗有多種原因：

- 工作命令列失敗，並傳回非零的結束代碼。
- 已為工作指定 `resourceFiles`，但發生有一個或多個檔案未下載的錯誤。
- 已為工作指定 `outputFiles`，但發生有一個或多個檔案未上傳的錯誤。
- 已超過工作的已耗用時間 (由[條件約束](/rest/api/batchservice/task/add#taskconstraints)工作中的 `maxWallClockTime` 屬性所指定)。

在所有情況下，都必須檢查下列屬性是否有錯誤，並查看錯誤的相關資訊：
- 工作的 [executionInfo](/rest/api/batchservice/task/get#taskexecutioninformation) 屬性包含多個可提供錯誤相關資訊的屬性。 [result](/rest/api/batchservice/task/get#taskexecutionresult) 會指出工作是否因任何原因而失敗，並以 `exitCode` 和 `failureInfo` 來提供有關失敗的詳細資訊。
- 不論是否成功或失敗，工作一律會移至 `completed` [狀態](/rest/api/batchservice/task/get#taskstate)。

工作失敗對作業的影響，以及任何工作相依性都必須考慮。  您可以為工作指定 [exitConditions](/rest/api/batchservice/task/add#exitconditions) 屬性，以設定相依性和作業的動作。
- 針對相依性，[DependencyAction](/rest/api/batchservice/task/add#dependencyaction) 會控制是否封鎖或執行依存於失敗工作的工作。
- 針對作業，[JobAction](/rest/api/batchservice/task/add#jobaction) 會控制失敗的工作是否會導致作業停用、終止或保持不變。

### <a name="task-command-line-failures"></a>工作命令列失敗

執行工作命令列時，輸出會寫入 `stderr.txt` 和 `stdout.txt`。 此外，應用程式可能會寫入應用程式專屬的記錄檔。

如果工作執行所在的集區節點仍存在，則可以取得及檢視記錄檔。 例如，Azure 入口網站會列出工作或集區節點的記錄檔，以供您查看。 許多 API 也允許列出並取得工作檔案，例如「[從工作取得](/rest/api/batchservice/file/getfromtask)」。

由於集區和集區節點的存留期常常很短暫 (因為節點會持續新增和刪除)，因此建議保存記錄檔。 [工作輸出檔案](./batch-task-output-files.md)是將記錄檔儲存至 Azure 儲存體的便利方式。

### <a name="output-file-failures"></a>輸出檔案失敗
在每次檔案上傳時，Batch 會將 `fileuploadout.txt` 和 `fileuploaderr.txt` 兩個記錄檔寫入計算節點中。 若要進一步了解特定錯誤，您可以檢查這些記錄檔。 在從未嘗試檔案上傳的案例中 (例如因為工作本身無法執行)，這些記錄檔就不會存在。  

## <a name="next-steps"></a>後續步驟

檢查您的應用程式是否會執行完整的錯誤檢查；立即偵測並診斷問題是非常重要的功能。
