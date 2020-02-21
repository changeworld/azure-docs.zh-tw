---
title: 檢查是否有集區和節點錯誤 - Azure Batch
description: 本文涵蓋可能發生的背景作業，以及要檢查的錯誤，以及如何在建立集區和節點時加以避免。
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: 88382a5b6e0364145d8504b5e25ef1a9bfd0111a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484123"
---
# <a name="check-for-pool-and-node-errors"></a>檢查是否有集區和節點錯誤

當您建立和管理 Azure Batch 集區時，某些作業將會立即執行。 不過，某些作業是非同步，而且會在背景中執行，花費幾分鐘的時間才能完成。

要偵測立即執行的作業是否失敗並不難，因為 API、CLI 或 UI 會立即傳回所有失敗。

本文說明可能為集區和集區節點執行的背景作業。 文中將闡明如何偵測和避免失敗。

## <a name="pool-errors"></a>集區錯誤

### <a name="resize-timeout-or-failure"></a>調整逾時或失敗

在建立新集區或調整現有集區的大小時，您會指定目標節點數目。  建立或調整大小作業會立即完成，但新節點的實際配置或現有節點的移除作業可能需要幾分鐘的時間。  您可以在[建立](https://docs.microsoft.com/rest/api/batchservice/pool/add)或[調整大小](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API 中指定調整逾時。 如果 Batch 無法在調整大小的超時期間取得目標節點數目，則集區會進入穩定狀態，並回報錯誤大小。

最新評估的[ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror)屬性會列出發生的錯誤。

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
  - 使用自訂 VM 映像的大型集區可能需要較長的時間進行配置，因此可能會發生調整逾時。  如需限制和設定的建議，請參閱[使用共用映射資源庫建立集](batch-sig-images.md)區。

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

即使 Batch 成功地在集區中配置節點，各種問題也可能導致某些節點狀況不良，而且無法執行工作。 這些節點仍會產生費用，因此請務必偵測問題，以避免對無法使用的節點付費。 除了常見的節點錯誤之外，瞭解目前的[作業狀態](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)對於疑難排解很有用。

### <a name="start-task-failures"></a>啟動工作失敗

您可以為集區指定選擇性的[開始工作](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)。 與任何工作一樣，您可以指定命令列和要從儲存體下載的資源檔案。 每個節點啟動後，系統即會分別執行其「開始工作」。 **waitForSuccess** 屬性會指定 Batch 是否應等到「開始工作」順利完成後，再為節點排定任何工作。

如果您已設定要讓節點等待「開始工作」順利完成，但開始工作卻失敗，該如何處理？ 在這種情況下，節點將無法使用，但仍會產生費用。

您可以使用最上層 [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) 節點屬性的 [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) 和 [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) 屬性，來偵測失敗的「開始工作」。

如果**waitForSuccess**設定為**true**，失敗的啟動工作也會導致批次將節點[狀態](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)設定為**starttaskfailed** 。

與任何工作一樣，開始工作失敗的原因可能有許多個。  若要進行疑難排解，請檢查 stdout、stderr 及任何進一步的工作特定記錄檔。

啟動工作必須可以重新進入，因為在相同的節點上可能會多次執行啟動工作;當節點已重新安裝映射或重新開機時，就會執行啟動工作。 在罕見的情況下，啟動工作會在事件導致節點重新開機之後執行，其中其中一個作業系統或暫時磁片已重新安裝映射，而另一個則不是。 由於批次啟動工作（例如所有 Batch 工作）是從暫時磁片執行，這通常不是問題，但在某些情況下，啟動工作會將應用程式安裝到作業系統磁片，並將其他資料保留在暫時磁片上，這可能會導致問題是因為東西不同步。如果您同時使用這兩個磁片，請適當地保護您的應用程式。

### <a name="application-package-download-failure"></a>應用程式套件下載失敗

您可以為集區指定一或多個應用程式套件。 Batch 會將指定的封裝檔案下載到每個節點，並在節點啟動後，但在排定工作之前，uncompresses 檔案。 將開始工作命令列與應用程式套件搭配使用是常見的做法。 例如，用來將檔案複製到不同的位置或執行安裝程式。

[節點[錯誤](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)] 屬性會報告下載和取消壓縮應用程式封裝的失敗;節點狀態設定為 [**無法使用**]。

### <a name="container-download-failure"></a>容器下載失敗

您可以在集區上指定一個或多個容器參考。 Batch 會將指定的容器下載到每個節點。 [節點[錯誤](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)] 屬性會回報失敗以下載容器，並將節點狀態設定為 [**無法使用**]。

### <a name="node-in-unusable-state"></a>處於無法使用狀態的節點

Azure Batch 將[節點狀態](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)設為**無法使用**的原因有很多。 當節點狀態設為**無法使用**時，將無法為節點排定工作，但節點仍將產生費用。

節點處於**無法使用**的狀態，但沒有[錯誤](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)表示 Batch 無法與 VM 通訊。 在此情況下，Batch 一律會嘗試復原 VM。 Batch 不會自動嘗試復原無法安裝應用程式套件或容器的 Vm，即使其狀態為**無法使用**也一樣。

如果 Batch 可判斷出原因，節點的 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 屬性會加以報告。

導致節點**無法使用**的其他原因包括：

- 自訂 VM 映像無效。 例如，未正確備妥的映像。

- VM 因基礎結構失敗或低層級升級而移動。 Batch 會復原節點。

- VM 映射已部署在不支援的硬體上。 例如，嘗試在[Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) VM 上執行 CentOS HPC 映射。

- Vm 位於[Azure 虛擬網路](batch-virtual-network.md)中，且流量已封鎖到金鑰埠。

- Vm 位於虛擬網路中，但對 Azure 儲存體的輸出流量會遭到封鎖。

- Vm 位於具有客戶 DNS 設定的虛擬網路中，且 DNS 伺服器無法解析 Azure 儲存體。

### <a name="node-agent-log-files"></a>節點代理程式記錄檔

在每個集區節點上執行的批次代理程式進程可以提供記錄檔，如果您需要與集區節點問題的支援聯繫，可能會有説明。 節點的記錄檔可透過 Azure 入口網站、Batch Explorer 或 [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs) 來上傳。 上傳並儲存記錄檔，將對您有幫助。 其後，您可以刪除節點或集區，以節省執行節點的成本。

### <a name="node-disk-full"></a>節點磁片已滿

集區節點 VM 的暫存磁片磁碟機是由批次用於作業檔案、工作檔案和共用檔案。

- 應用程式套件檔案
- 工作資源檔
- 應用程式特定的檔案已下載至其中一個 Batch 資料夾
- 每個工作應用程式執行的 Stdout 和 stderr 檔案
- 應用程式特定的輸出檔

這些檔案中的某些檔案只會在建立集區節點時寫入一次，例如集區應用程式套件或集區啟動工作資源檔。 即使在建立節點時只寫入一次，如果這些檔案太大，它們可能會填滿暫存磁片磁碟機。

在節點上執行的每個工作都會寫出其他檔案，例如 stdout 和 stderr。 如果在相同的節點上執行大量工作，而且（或）工作檔案太大，則可能會填滿暫存磁片磁碟機。

暫存磁片磁碟機的大小取決於 VM 大小。 挑選 VM 大小時，一項考慮是要確保暫存磁片磁碟機具有足夠的空間。

- 在 Azure 入口網站新增集區時，可以顯示 VM 大小的完整清單，而且有「資源磁片大小」資料行。
- 描述所有 VM 大小的文章具有具有「暫存儲存體」資料行的資料表;例如[計算優化 VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute)

針對每個工作所寫出的檔案，可以指定每個工作的保留時間，以決定要在自動清除之前保留工作檔案的時間長度。 保留時間可以減少，以降低儲存需求。

如果暫存磁碟空間填滿，則目前節點會停止執行中的工作。 未來將會回報[節點錯誤](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)。


## <a name="next-steps"></a>後續步驟

請確認您已設定應用程式以實作完整的錯誤檢查，特別是針對非同步作業。 這可能是能否立即偵測並診斷問題的關鍵。
