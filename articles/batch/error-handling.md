---
title: Azure Batch 中的錯誤處理和偵測
description: 從開發觀點了解 Batch 服務工作流程中的錯誤處理。
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 3bd460598dae08fa18415e1c9865249f3ca4c9c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85964272"
---
# <a name="error-handling-and-detection-in-azure-batch"></a>Azure Batch 中的錯誤處理和偵測

有時候，您可能需要處理 Batch 解決方案中的工作和應用程式失敗。 本文討論錯誤的類型，以及如何加以解決。

## <a name="error-codes"></a>錯誤碼

一般的錯誤類型包括：

- 從未達到 Batch 的要求，或 Batch 回應未及時到達用戶端時的網路失敗。
- 內部伺服器錯誤 (標準 5xx 狀態碼 HTTP 回應)。
- 節流相關的錯誤，例如 429 或 503 狀態碼 HTTP 回應與 Retry-after 標頭。
- 4xx 錯誤，例如 AlreadyExists 和 InvalidOperation。 這表示資源不是處於狀態轉換的正確狀態。

如需特定錯誤碼的詳細資訊，包括 REST API、Batch 服務和作業工作/排程的錯誤碼，請參閱 [Batch 狀態和錯誤碼](/rest/api/batchservice/batch-status-and-error-codes)。

## <a name="application-failures"></a>應用程式失敗

在執行期間，應用程式可能會產生診斷輸出，以便用來排解疑難問題。 如[檔案和目錄](files-and-directories.md)中所述，Batch 服務會將標準輸出和標準錯誤輸出寫入計算節點上工作目錄中的 `stdout.txt` 和 `stderr.txt` 檔案。

您可以使用 Azure 入口網站或其中一個 Batch SDK 來下載這些檔案。 例如，您可以使用 Batch .NET 程式庫中的 [ComputeNode.GetNodeFile](/dotnet/api/microsoft.azure.batch.computenode) 和 [CloudTask.GetNodeFile](/dotnet/api/microsoft.azure.batch.cloudtask)，擷取這些和其他檔案來進行疑難排解。

## <a name="task-errors"></a>工作錯誤

工作錯誤分為數個類別。

### <a name="pre-processing-errors"></a>前置處理錯誤

如果工作無法啟動，系統便會對該工作設定前置處理錯誤。  

前置處理錯誤的發生原因，可能是工作的資源檔案已移動、儲存體帳戶已無法使用，或發生其他使檔案無法成功複製到節點的問題。

### <a name="file-upload-errors"></a>檔案上傳錯誤

如果在上傳針對工作指定的檔案時因故失敗，系統會對該工作設定檔案上傳錯誤。

檔案上傳錯誤的發生原因可能是所提供來存取 Azure 儲存體的 SAS 無效或未提供寫入權限、儲存體帳戶已無法再使用，或發生其他讓系統無法成功從節點複製檔案的問題。

### <a name="application-errors"></a>應用程式錯誤

工作的命令列所指定的程序也可能會失敗。 工作所執行的程序傳回非零的結束碼時，此程序會被視為失敗 (請參閱下一節的＜工作結束代碼＞  )。

針對應用程式錯誤，您可以將 Batch 設定成自動重試工作直到指定的次數為止。

### <a name="constraint-errors"></a>條件約束錯誤

您可以設定條件約束來指定作業或工作的最大執行持續期間「maxWallClockTime」 。 這很適合用於終止無法進行的工作。

超過時間量上限時，則會將工作標示為「已完成」，但結束代碼會設為 `0xC000013A`，[schedulingError] 欄位會標示為 `{ category:"ServerError", code="TaskEnded"}`。

## <a name="task-exit-codes"></a>工作結束代碼

如前文所述，如果工作所執行的程序傳回非零的結束代碼，則 Batch 服務會將此工作標示為失敗。 當工作執行一個程序時，Batch 會使用「程序的傳回代碼」填入工作的結束代碼屬性。

請務必注意，工作的結束代碼不會取決於 Batch 服務。 工作的結束代碼取決於程序本身，或程序執行所在的作業系統。

## <a name="task-failures-or-interruptions"></a>工作失敗或中斷

工作可能偶爾會失敗或中斷。 工作應用程式本身可能失敗、執行工作的節點可能重新開機，或節點在調整大小作業期間，可能因為集區的取消配置原則設為不需等待工作完成便立即移除節點而從集區中移除。 在所有的情況下，此工作均可由 Batch 自動重新排入佇列，並且在另一個節點上執行。

也可能是間歇性問題導致工作停止回應或花太長時間執行。 您可以設定工作的執行間隔上限。 如果超過最大執行間隔，Batch 服務會中斷工作應用程式。

## <a name="connect-to-compute-nodes"></a>連線到計算節點

您可以從遠端登入計算節點，以執行額外的偵錯和疑難排解。 您可以使用 Azure 入口網站下載 Windows 節點的遠端桌面通訊協定 (RDP) 檔案，並取得 Linux 節點的安全殼層 (SSH) 連線資訊。 您也可以使用 Batch API (例如透過 [Batch .NET](/dotnet/api/microsoft.azure.batch.computenode) 或 [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh)) 進行此項作業。

> [!IMPORTANT]
> 若要透過 RDP 或 SSH 連接到節點，您必須先在節點上建立使用者。 若要這樣做，您可以使用 Azure 入口網站，透過 Batch REST API [將使用者帳戶新增至節點](/rest/api/batchservice/computenode/adduser)、在 Batch .NET 中呼叫 [ComputeNode.CreateComputeNodeUser](/dotnet/api/microsoft.azure.batch.computenode) 方法，或在 Batch Python 模組中呼叫 [add_user](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) 方法。

如果您需要限制或停用對計算節點的 RDP 或 SSH 存取，請參閱[設定或停用對 Azure Batch 集區中的計算節點的遠端存取](pool-endpoint-configuration.md)。

## <a name="troubleshoot-problem-nodes"></a>針對問題節點進行疑難排解

在部分工作失敗的情況下，Batch 用戶端應用程式或服務可以檢查失敗工作的中繼資料來找出行為異常的節點。 集區中的每個節點都有唯一的 ID，而執行工作的節點會包含在工作中繼資料中。 在找出問題節點之後，您即可對其採取數個行動︰

- **重新啟動節點** ([REST](/rest/api/batchservice/computenode/reboot) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reboot)))

    重新啟動節點有時可以清除潛在的問題，例如停滯或損毀的程序。 如果集區使用啟動工作或作業使用作業準備工作，則在節點重新啟動時，會執行這些工作。
- **重新安裝節點映像** ([REST](/rest/api/batchservice/computenode/reimage) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reimage))

    這會在節點上重新安裝作業系統。 和重新啟動節點一樣，在重新安裝映像節點後，便會重新執行啟動工作和作業準備工作。
- **從集區中移除節點** ([REST](/rest/api/batchservice/pool/removenodes) | [.NET](/dotnet/api/microsoft.azure.batch.pooloperations))

    有時候您必須從集區中完整移除節點。
- **停用節點上的工作排程** ([REST](/rest/api/batchservice/computenode/disablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.disablescheduling))

    這實際上會讓節點離線，以便不再指派任何工作給它，但允許該節點繼續執行並留在集區中。 這可讓您執行進一步的調查以了解失敗原因，卻又不會遺失失敗工作的資料，而且不會讓節點造成額外的工作失敗。 例如，您可以停用節點上的工作排程，然後從遠端登入以檢查節點的事件記錄，或執行其他疑難排解。 在完成調查之後，您就可以啟用工作排程 ([REST](/rest/api/batchservice/computenode/enablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.enablescheduling)) 讓節點重新上線，或是執行稍早所討論的其中一個動作。

> [!IMPORTANT]
> 您可以使用上述的動作，指定當您執行動作時，如何處理節點上目前正在執行的工作。 例如，當您使用 Batch .NET 用戶端程式庫停用節點上的工作排程時，可以指定 [DisableComputeNodeSchedulingOption](/dotnet/api/microsoft.azure.batch.common.disablecomputenodeschedulingoption) 列舉值，以指定是要**終止**執行中的工作、將工作**重新放入佇列**以在其他節點上排程，還是允許執行中的工作先完成再執行動作 (**TaskCompletion**)。

## <a name="retry-after-errors"></a>錯誤後重試

Batch API 會在發生失敗時通知您。 其全都可以重試，且全都包含該用途的全域重試處理常式。 最佳做法是使用此內建機制。

失敗之後，您應該等候一段時間 (重試之間數秒)，然後再重試。 如果您太頻繁或太快重試，重試處理常式將會節流。

## <a name="next-steps"></a>後續步驟

- 了解如何[檢查是否有集區和節點錯誤](batch-pool-node-error-checking.md)。
- 了解如何[檢查作業和工作錯誤](batch-job-task-error-checking.md)。
- 檢閱 [Batch 狀態和錯誤碼](/rest/api/batchservice/batch-status-and-error-codes)的清單。
