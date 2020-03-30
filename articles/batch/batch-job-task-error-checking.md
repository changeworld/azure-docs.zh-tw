---
title: 檢查作業和任務錯誤 - Azure 批次處理 |微軟文檔
description: 要檢查的錯誤和作業和任務故障
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087002"
---
# <a name="job-and-task-error-checking"></a>作業和任務錯誤檢查

添加作業和任務時可能會出現各種錯誤。 檢測這些操作的故障非常簡單，因為 API、CLI 或 UI 會立即返回任何故障。  但是，在計畫並運行作業和任務時，以後可能會發生一些失敗。

本文介紹提交作業和任務後可能發生的錯誤。 它列出並解釋需要檢查和處理的錯誤。

## <a name="jobs"></a>工作

作業是一個或多個任務的分組，任務實際上指定要運行的命令列。

添加作業時，可以指定以下參數，這些參數可能會影響作業失敗的方式：

- [作業條件約束](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - 可以選擇`maxWallClockTime`指定該屬性以設置作業可以處於活動狀態或運行的最大時間量。 如果超過，該作業將終止與在作業`terminateReason`的執行[資訊](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob)中設置的屬性。
- [工作準備任務](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - 如果指定，則首次為節點上的作業運行任務時運行作業準備任務。 作業準備任務可能會失敗，這將導致任務未運行，作業未完成。
- [作業發佈任務](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - 僅當配置了作業準備任務時，才能指定作業發佈任務。 終止作業時，作業釋放任務將在運行作業準備任務的每個池節點上運行。 作業釋放任務可能會失敗，但作業仍將移動到狀態`completed`。

### <a name="job-properties"></a>工作屬性

應檢查以下作業屬性是否存在錯誤：

- '[執行資訊](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)'：
  - 屬性`terminateReason`可以具有指示`maxWallClockTime`超過 作業約束中指定的 的值，因此作業已終止。 如果作業`onTaskFailure`屬性設置得當，也可以將其設置為指示任務失敗。
  - 如果出現計畫錯誤，則設置[計畫錯誤](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror)屬性。
 
### <a name="job-preparation-tasks"></a>工作準備任務

如果為作業指定了作業準備任務，則該任務的實例將在首次在節點上運行該作業的任務時運行該任務。 作業上配置的作業準備任務可視為任務範本，運行多個作業準備任務實例，最多為池中的節點數。

應檢查作業準備任務實例以確定是否存在錯誤：
- 運行作業準備任務時，觸發作業準備任務的任務將移動到[state](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) `preparing`如果作業準備任務隨後失敗，觸發任務將恢復為狀態`active`，並且不會運行。  
- 可以使用[清單準備和發佈任務狀態](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)API 從作業獲取已運行的作業準備任務的所有實例。 與任何任務一樣，屬性（[execution information](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)如`failureInfo`）`exitCode`和`result`.
- 如果作業準備任務失敗，則觸發作業任務將不會運行，作業將無法完成並卡住。 如果沒有具有可計畫任務的其他作業，池可能會未使用。

### <a name="job-release-tasks"></a>作業發佈任務

如果為作業指定了作業釋放任務，則當作業被終止時，將在運行作業準備任務的每個池節點上運行作業釋放任務的實例。  應檢查作業釋放任務實例以確定是否存在錯誤：
- 可以使用 API[清單準備和發佈任務狀態](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)從作業獲取正在運行的作業發佈任務的所有實例。 與任何任務一樣，屬性（[execution information](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)如`failureInfo`）`exitCode`和`result`.
- 如果一個或多個作業釋放任務失敗，則作業仍將終止並移動到狀態`completed`。

## <a name="tasks"></a>工作

作業任務可能由於多種原因失敗：

- 任務命令列失敗，返回時帶有非零結束代碼。
- 為`resourceFiles`任務指定了任務，但失敗意味著一個或多個檔未下載。
- 為`outputFiles`任務指定了任務，但失敗意味著一個或多個檔未上載。
- 超過任務`maxWallClockTime`[約束](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)中的屬性指定的任務的經過時間。

在所有情況下，都必須檢查以下屬性是否存在錯誤和有關錯誤的資訊：
- 任務[執行資訊](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation)屬性包含多個屬性，這些屬性提供有關錯誤的資訊。 [結果](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult)指示任務是否由於任何原因失敗，並`exitCode``failureInfo`提供有關故障的詳細資訊。
- 任務將始終移動到`completed`[狀態](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)，而不管它是成功還是失敗。

必須考慮任務失敗對作業和任何任務依賴項的影響。  可以為任務指定[exit條件](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions)屬性，以配置依賴項和作業的操作。
- 對於依賴項，[依賴項操作](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction)控制依賴于失敗任務的任務是被阻止還是正在運行。
- 對於作業[，JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction)控制失敗的任務是否導致作業被禁用、終止或保持不變。

### <a name="task-command-line-failures"></a>任務命令列失敗

運行任務命令列時，輸出將`stderr.txt`寫入 和`stdout.txt`。 此外，應用程式可能會寫入特定于應用程式的日誌檔。

如果運行任務的池節點仍然存在，則可以獲取和查看日誌檔。 例如，Azure 門戶列出並可以查看任務或池節點的日誌檔。 多個 API 還允許列出和獲取任務檔，例如[從任務獲取](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask)。

由於池和池節點經常是短暫的，節點被不斷添加和刪除，因此建議保留日誌檔。 [任務輸出檔案](https://docs.microsoft.com/azure/batch/batch-task-output-files)是將日誌檔保存到 Azure 存儲的便捷方法。

### <a name="output-file-failures"></a>輸出檔案失敗
在每次檔案上傳時，Batch 會將 `fileuploadout.txt` 和 `fileuploaderr.txt` 兩個記錄檔寫入計算節點中。 若要進一步了解特定錯誤，您可以檢查這些記錄檔。 在從未嘗試檔上載的情況下，例如，由於任務本身無法運行，則這些日誌檔將不存在。  

## <a name="next-steps"></a>後續步驟

檢查應用程式是否實現全面的錯誤檢查;及時檢測和診斷問題至關重要。
