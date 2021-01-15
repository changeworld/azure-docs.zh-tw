---
title: 轉譯概觀
description: 使用 Azure 進行轉譯的簡介及 Azure Batch 轉譯功能的概觀
author: mscurrell
ms.author: markscu
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 1cd07f9322837c03e15aaeabec993820deb3170a
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232109"
---
# <a name="rendering-using-azure"></a>使用 Azure 進行轉譯

轉譯是取用 3D 模型並將其轉換成 2D 影像的程序。 3D 場景檔案可在 Autodesk 3ds Max、Autodesk Maya 和 Blender 等應用程式中撰寫。  Autodesk Maya、Autodesk Arnold、Chaos Group V-Ray 和 Blender Cycles 等轉譯應用程式可產生 2D 影像。  有時候，會從場景檔案建立單一映像。 不過，一般通常會建立多個影像的模型並加以轉譯，然後將其結合在動畫中。

媒體和娛樂業的特效 (VFX) 會大量使用轉譯工作負載。 其他如廣告、零售、石油和天然氣及製造等許多產業，也會使用轉譯。

轉譯的程序會耗用大量運算資源；其間可能會產生許多畫面格/影像，且每個影像的轉譯可能都會耗時數小時。  因此，轉譯是一種完美的批次處理工作負載，可利用 Azure 來平行執行許多轉譯，並使用各種不同的硬體，包括 Gpu。

## <a name="why-use-azure-for-rendering"></a>為何要使用 Azure 進行轉譯？

基於許多原因，轉譯是完全適用于 Azure 的工作負載：

* 轉譯作業可以分割成許多可使用多個 VM 以平行方式執行的部分：
  * 動畫由許多畫面格組成，且每個畫面格可以平行方式轉譯。  可用來處理每個畫面格的 VM 愈多，所有畫面格和動畫的產生速度就愈快。
  * 某些轉譯軟體可讓單一畫面格分成多個部分，例如圖格或配量。  每個部分可個別進行轉譯，然後在每個部分都完成後再結合為最終影像。  可用的 VM 愈多，轉譯畫面格的速度就愈快。
* 轉譯專案可能需要大幅的調整：
  * 即使使用高階硬體，個別畫面格仍可能十分複雜而需要數小時進行轉譯，而動畫可能包含數十萬個畫面格。  若要在合理的時間內呈現高品質的動畫，必須經過大量計算。  在某些情況下，用來以平行方式轉譯數千個畫面格的核心，會超過 100,000 個。
* 轉譯專案以專案為基礎，且需要不同的計算量：
  * 在必要時配置計算和儲存體容量、根據專案期間的負載將其相應增加或相應減少，並在專案完成後加以移除。
  * 在容量配置時支付其費用，但在沒有負載時無須付費，例如在專案間的空窗期。
  * 因應非預期的變更所產生的高載；如果在專案晚期才出現非預期的變更，且這些變更需要以緊迫的時程處理，請進行擴充調整。
* 根據應用程式、工作負載和時間範圍選擇各式各樣的硬體：
  * 在 Azure 中可選擇使用多種可透過 Batch 來配置和管理的硬體。
  * 視專案的不同，最佳價格/效能或最佳整體效能會有不同的需求。  不同場景和/或轉譯應用程式會有不同的記憶體需求。  某些轉譯應用程式可利用 GPU 來達到最佳效能或執行特定功能。 
* 低優先順序或 [點 vm](https://azure.microsoft.com/pricing/spot/) 降低成本：
  * 相較于標準 Vm，低優先順序和現成的 Vm 可提供大量折扣，適用于某些作業類型。
  
## <a name="existing-on-premises-rendering-environment"></a>現有的內部部署呈現環境

最常見的情況是，現有的內部部署轉譯伺服器陣列受轉譯管理應用程式管理，例如 PipelineFX Qube、Royal 轉譯、Thinkbox deadline 期限或自訂應用程式。  其需求是必須使用 Azure VM 擴充內部部署轉譯伺服器陣列容量。

Azure 基礎結構和服務是用來建立混合式環境，其中 Azure 會用來補充內部部署容量。 例如：

* 使用 [虛擬網路](../virtual-network/virtual-networks-overview.md) 將 Azure 資源放在與內部部署轉譯伺服器陣列相同的網路上。
* 使用 [Avere vFXT for Azure](../avere-vfxt/avere-vfxt-overview.md) 或 [Azure HPC Cache](../hpc-cache/hpc-cache-overview.md) 來快取 Azure 中的來源檔案，以減少頻寬使用和延遲，並將效能最大化。
* 請確認現有的授權伺服器位於虛擬網路上，並購買符合額外 Azure 容量所需的額外授權。

## <a name="no-existing-render-farm"></a>沒有現有的轉譯伺服器陣列

用戶端工作站可能正在執行轉譯，但轉譯負載正在增加，而且需要太長的時間才能完全使用工作站容量。

有兩個主要選項可用：

* 部署內部部署轉譯管理員（例如 Royal 轉譯），並在需要進一步的容量或效能時，設定混合式環境以使用 Azure。 轉譯管理員是專為轉譯工作負載量身打造的，並且包含適用于熱門用戶端應用程式的外掛程式，可讓您輕鬆地提交轉譯工作。

* 使用 Azure Batch 的自訂解決方案，可配置和管理計算容量，以及提供執行轉譯作業的工作排程。

## <a name="next-steps"></a>後續步驟

 瞭解如何 [使用 Azure 基礎結構和服務來擴充現有的內部部署轉譯伺服器](https://azure.microsoft.com/solutions/high-performance-computing/rendering/)陣列。

深入瞭解 [Azure Batch 轉譯功能](batch-rendering-functionality.md)。
