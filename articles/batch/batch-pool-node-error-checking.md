---
title: 檢查是否有集區和節點錯誤 - Azure Batch
description: 本文介紹了可能發生的背景操作，以及要檢查的錯誤以及如何在創建池和節點時避免這些操作。
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: a68d812a044c776819d169d5bf179f011d06390f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472940"
---
# <a name="check-for-pool-and-node-errors"></a>檢查是否有集區和節點錯誤

當您建立和管理 Azure Batch 集區時，某些作業將會立即執行。 但是，某些操作是非同步，在後臺運行，需要幾分鐘才能完成。

要偵測立即執行的作業是否失敗並不難，因為 API、CLI 或 UI 會立即傳回所有失敗。

本文說明可能為集區和集區節點執行的背景作業。 文中將闡明如何偵測和避免失敗。

## <a name="pool-errors"></a>集區錯誤

### <a name="resize-timeout-or-failure"></a>調整逾時或失敗

在建立新集區或調整現有集區的大小時，您會指定目標節點數目。  創建或調整大小操作將立即完成，但實際分配新節點或刪除現有節點可能需要幾分鐘時間。  您可以在[建立](https://docs.microsoft.com/rest/api/batchservice/pool/add)或[調整大小](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API 中指定調整逾時。 如果 Batch 在調整超時期間無法獲取目標節點數，則池將進入穩定狀態並報告調整大小錯誤。

最近評估的["調整大小錯誤"](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror)屬性列出了發生的錯誤。

調整大小錯誤的常見原因包括：

- 調整逾時太短
  - 在一般情況下，預設的逾時 15 分鐘通常即足以進行集區節點的配置或移除。
  - 如果您將配置大量的節點，建議您將調整逾時設為 30 分鐘。 例如，當您要從 Azure Marketplace 映像將大小調整為 1000 個以上的節點，或從自訂 VM 映像調整為 300 個以上的節點。
- 核心配額不足
  - Batch 帳戶可配置給所有集區的核心數目有其限制。 達到該配額之後，Batch 就會停止配置節點。 您[可以增加](https://docs.microsoft.com/azure/batch/batch-quota-limit)核心配額，使 Batch 能夠配置更多節點。
- 當[集區位於虛擬網路中](https://docs.microsoft.com/azure/batch/batch-virtual-network)時，子網路 IP 不足
  - 虛擬網路子網路必須有足夠的未指派 IP 位址可配置給每個要求的集區節點。 否則，就會無法建立節點。
- 當[集區位於虛擬網路中](https://docs.microsoft.com/azure/batch/batch-virtual-network)時，資源不足
  - 您可以在與 Batch 帳戶相同的訂用帳戶中建立資源，例如負載平衡器、公用 IP 和網路安全性群組。 請確認訂用帳戶配額足夠供這些資源使用。
- 使用自訂 VM 映像的大型集區
  - 使用自訂 VM 映像的大型集區可能需要較長的時間進行配置，因此可能會發生調整逾時。  有關限制和配置的建議[，請參閱使用共用映射庫創建池](batch-sig-images.md)。

### <a name="automatic-scaling-failures"></a>自動調整失敗

您也可以設定 Azure Batch，使其自動調整集區中的節點數目。 您必須為[集區的自動調整公式](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)定義參數。 Batch 服務會使用此公式定期評估集區中的節點數目，並設定新的目標數目。 以下是可能偵測到的問題類型：

- 自動調整評估失敗。
- 產生的調整大小作業失敗並逾時。
- 自動調整公式的問題會導致不正確的節點目標值。 調整大小可能會成功或逾時。

您可以使用 [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) 屬性取得上次自動調整評估的相關資訊。 此屬性會報告評估時間、值和結果，以及任何效能錯誤。

[集區調整大小完成事件](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event)會擷取所有評估的相關資訊。

### <a name="delete"></a>刪除

當您刪除包含節點的集區時，Batch 會先刪除節點。 然後它會刪除集區物件本身。 刪除這些節點可能需要幾分鐘的時間。

在刪除過程中，Batch 會將[集區狀態](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate)設為**刪除中**。 呼叫端應用程式可以使用 **state** 和 **stateTransitionTime** 屬性來偵測集區刪除作業是否耗費太多時間。

## <a name="pool-compute-node-errors"></a>集區計算節點錯誤

即使 Batch 成功分配池中的節點，各種問題也可能導致某些節點不正常且無法運行任務。 這些節點仍會產生費用，因此檢測問題以避免為無法使用的節點付費非常重要。 除了常見的節點錯誤之外，瞭解當前[作業狀態](/rest/api/batchservice/job/get#jobstate)對於故障排除也很有用。

### <a name="start-task-failures"></a>啟動任務失敗

您可以為集區指定選擇性的[開始工作](/rest/api/batchservice/pool/add#starttask)。 與任何工作一樣，您可以指定命令列和要從儲存體下載的資源檔案。 每個節點啟動後，系統即會分別執行其「開始工作」。 **waitForSuccess** 屬性會指定 Batch 是否應等到「開始工作」順利完成後，再為節點排定任何工作。

如果您已設定要讓節點等待「開始工作」順利完成，但開始工作卻失敗，該如何處理？ 在這種情況下，節點將不可用，但仍會產生費用。

您可以使用最上層 [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) 節點屬性的 [result](/rest/api/batchservice/computenode/get#taskexecutionresult) 和 [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) 屬性，來偵測失敗的「開始工作」。

如果**WaitFor成功**設置為**true，** 則失敗的啟動任務還會導致 Batch 將節點[狀態](/rest/api/batchservice/computenode/get#computenodestate)設置為**啟動任務失敗**。

與任何工作一樣，開始工作失敗的原因可能有許多個。  若要進行疑難排解，請檢查 stdout、stderr 及任何進一步的工作特定記錄檔。

啟動任務必須重新進入，因為啟動任務可能在同一節點上運行多次;重新映射或重新開機節點時運行啟動任務。 在極少數情況下，啟動任務將在導致節點重新開機的事件後運行，其中其中一個作業系統或臨時磁片被重新映射，而另一個磁片則未重新映射。 由於 Batch 啟動任務（與所有 Batch 任務一樣）從臨時磁片運行，這通常不是問題，但在某些情況下，啟動任務正在將應用程式安裝到作業系統磁片並將其他資料保留在臨時磁片上，這可能導致問題，因為事情不同步。如果使用兩個磁片，請相應地保護應用程式。

### <a name="application-package-download-failure"></a>應用程式套件下載失敗

您可以為集區指定一或多個應用程式套件。 Batch 將指定的包檔下載到每個節點，並在節點啟動後（但在計畫任務之前）解壓縮檔。 將開始工作命令列與應用程式套件搭配使用是常見的做法。 例如，用來將檔案複製到不同的位置或執行安裝程式。

節點[錯誤](/rest/api/batchservice/computenode/get#computenodeerror)屬性報告下載和取消壓縮應用程式包失敗;節點狀態設置為**不可用**。

### <a name="container-download-failure"></a>容器下載失敗

您可以在池上指定一個或多個容器引用。 批次處理將指定的容器下載到每個節點。 節點[錯誤](/rest/api/batchservice/computenode/get#computenodeerror)屬性報告下載容器失敗並將節點狀態設置為**不可用**。

### <a name="node-in-unusable-state"></a>處於無法使用狀態的節點

Azure Batch 將[節點狀態](/rest/api/batchservice/computenode/get#computenodestate)設為**無法使用**的原因有很多。 當節點狀態設為**無法使用**時，將無法為節點排定工作，但節點仍將產生費用。

處於**不可用**狀態的節點，但無[錯誤](/rest/api/batchservice/computenode/get#computenodeerror)意味著 Batch 無法與 VM 通信。 在這種情況下，Batch 始終嘗試恢復 VM。 Batch 不會自動嘗試恢復未能安裝應用程式包或容器的 VM，即使其狀態**不可用**。

如果 Batch 可判斷出原因，節點的 [errors](/rest/api/batchservice/computenode/get#computenodeerror) 屬性會加以報告。

導致節點**無法使用**的其他原因包括：

- 自訂 VM 映像無效。 例如，未正確備妥的映像。

- VM 因基礎結構失敗或低層級升級而移動。 Batch 會復原節點。

- VM 映射已部署在不支援它的硬體上。 例如，嘗試在[Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) VM 上運行 CentOS HPC 映射。

- VM 位於 Azure[虛擬網路中](batch-virtual-network.md)，並且流量已阻塞到關鍵埠。

- VM 位於虛擬網路中，但 Azure 存儲的出站流量被阻止。

- VM 位於具有客戶 DNS 配置的虛擬網路中，DNS 伺服器無法解決 Azure 存儲。

### <a name="node-agent-log-files"></a>節點代理程式記錄檔

在每個池節點上運行的 Batch 代理進程可以提供日誌檔，如果您需要就池節點問題與支援人員聯繫，這些日誌檔可能會有所説明。 節點的記錄檔可透過 Azure 入口網站、Batch Explorer 或 [API](/rest/api/batchservice/computenode/uploadbatchservicelogs) 來上傳。 上傳並儲存記錄檔，將對您有幫助。 其後，您可以刪除節點或集區，以節省執行節點的成本。

### <a name="node-disk-full"></a>節點磁片已滿

批次處理將池節點 VM 的臨時磁碟機用於作業檔、任務檔和共用檔。

- 應用程式包檔
- 任務資源檔
- 下載到其中一個 Batch 資料夾的應用程式特定檔
- 每個任務應用程式執行的抖號和穩達檔
- 特定于應用程式的輸出檔案

創建池節點時，其中一些檔僅寫入一次，例如池應用程式包或池啟動任務資源檔。 即使創建節點時只寫入一次，如果這些檔太大，它們也可以填充臨時磁碟機。

其他檔會針對在節點上運行的每個任務（如停滯和穩穩）進行寫入。 如果在同一節點上運行的大量任務和/或任務檔太大，它們可能會填滿臨時磁碟機。

臨時磁碟機的大小取決於 VM 大小。 選擇 VM 大小的一個考慮因素是確保臨時磁碟機有足夠的空間。

- 在 Azure 門戶中，可以顯示 VM 大小的完整清單，並顯示"資源磁片大小"列。
- 描述所有 VM 大小的文章具有帶有"臨時存儲"列的表;例如[計算優化的 VM 大小](/azure/virtual-machines/windows/sizes-compute)

對於每個任務寫入的檔，可以為每個任務指定保留時間，以確定任務檔在自動清理之前保留的時間。 可以縮短保留時間以降低存儲要求。


如果臨時磁碟空間不足（或非常接近耗盡空間），則節點將移動到[不可用](/rest/api/batchservice/computenode/get#computenodestate)狀態，並且將報告節點錯誤，指出磁片已滿。

### <a name="what-to-do-when-a-disk-is-full"></a>磁片已滿時應該怎麼做

確定磁片已滿的原因：如果您不確定節點上佔用的空間是什麼，建議將其遠端到節點並手動調查空間已消失的位置。 您還可以使用[批次處理清單檔 API](https://docs.microsoft.com/rest/api/batchservice/file/listfromcomputenode)檢查批次處理託管資料夾中的檔（例如，任務輸出）。 請注意，此 API 僅列出 Batch 託管目錄中的檔，如果任務在其他地方創建檔，您將看不到這些檔。

確保所需的任何資料都已從節點檢索或上載到持久存儲。 磁片滿的問題的所有緩解都涉及刪除資料以釋放空間。

### <a name="recovering-the-node"></a>恢復節點

1. 如果您的池是[C.loudService 配置](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration)池，則可以通過[Batch 重新映射 API](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage)重新映射節點。這將清理整個磁片。 [虛擬機器配置](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)池當前不支援重新映射。

2. 如果池是[虛擬機器配置](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)，則可以使用[刪除節點 API](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes)從池中刪除節點。 然後，您可以再次擴展池，以替換損壞的節點。

3.  刪除任務資料仍在節點上的舊已完成作業或舊已完成的任務。 有關節點上的作業/任務資料提示，您可以在節點上的["最近任務"集合](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskinformation)或[節點上的檔](https://docs.microsoft.com//rest/api/batchservice/file/listfromcomputenode)中查看。 刪除作業將刪除作業中的所有任務，刪除作業中的任務將觸發要刪除的節點上的任務目錄中的資料，從而釋放空間。 釋放足夠的空間後，重新開機節點，該節點應從"不可用"狀態移出，並再次進入"空閒"。

## <a name="next-steps"></a>後續步驟

請確認您已設定應用程式以實作完整的錯誤檢查，特別是針對非同步作業。 這可能是能否立即偵測並診斷問題的關鍵。
